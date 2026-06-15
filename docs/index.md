# Trilocore Documentation

Trilocore is smart-contract security testing, made visual — a security workbench for the
EVM, right in your browser. Disassemble any contract, see its selectors and control-flow,
fuzz it, and **prove real exploits on a mainnet fork** — no command line, no local setup.

!!! tip "New here?"
    Open **[bevm.trilocore.ai](https://bevm.trilocore.ai/)**, sign in, paste a contract
    address, and run a passive scan. The [Getting started](bevm.md#getting-started) guide
    walks through it in two minutes.

## The two tools

<div class="grid cards" markdown>

-   ### 🔬 BEVM — security workbench

    A visual EVM workbench: disassembly, control-flow graph, calldata decoder, a
    GPU-accelerated fuzzer with 200+ detectors, transaction repeater, storage editor, and
    mainnet-fork exploit replay.

    [Read the BEVM guide →](bevm.md)

-   ### 🤖 Janus — AI analyst

    An AI model that reasons like an attacker: analyze a contract or a finding, get a
    plain-English exploit explanation and an attack-chain walkthrough.

    [Read the Janus guide →](janus.md)

</div>

## Getting started

1. Go to **[bevm.trilocore.ai](https://bevm.trilocore.ai/)** and sign in. One account works
   across BEVM and Janus (shared sign-in).
2. In the **Smart Contract** panel, paste a contract **address** (mainnet) or raw
   **bytecode**. Trilocore fetches the code and disassembles it.
3. Run a **Passive scan** for detector findings, or open the **Visual Graph** to inspect
   selectors and control-flow blocks.
4. To go deeper, **fuzz** the target (Intruder / active scan) and **replay** a candidate
   exploit on a forked mainnet to confirm it's real.
5. Open **[janus.trilocore.ai](https://janus.trilocore.ai/)** and ask Janus to explain any
   finding in plain English.

## Key ideas

| Term | What it means in Trilocore |
|------|----------------------------|
| **Session / Workspace** | An isolated analysis context with its own mainnet fork. Open one to load a target and run tools. |
| **Aliases** | Friendly names for addresses used across panels: `attacker`, `target`, `me`, plus `zero` and `max`. |
| **Passive vs Active** | **Passive** = static detectors over the bytecode (fast). **Active** = fuzzing/execution against a fork (finds *and proves* real exploits). |
| **Fork replay** | Re-executing a candidate exploit against real, forked mainnet state — so a finding is a proven exploit, not a guess. |

!!! note "Support"
    Questions or want to support the project? Contact
    [saiteja@trilocore.com](mailto:saiteja@trilocore.com).
