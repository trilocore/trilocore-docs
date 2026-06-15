# BEVM — security workbench

BEVM is a visual workbench for the EVM. You load a contract, inspect how it works, hunt for
vulnerabilities with detectors and a GPU fuzzer, then **prove** a finding by replaying it on
a forked mainnet — all in the browser.

## Getting started

1. Open **[bevm.trilocore.ai](https://bevm.trilocore.ai/)** and sign in.
2. Open a **session** — this creates an isolated workspace with its own mainnet fork.
3. In **Smart Contract**, paste an **address** or **bytecode** to load and disassemble the target.
4. Run a **Passive scan** for instant detector findings.
5. Fuzz with **Intruder** / **Active scan**, then **Repeater** / **Block** to replay and confirm an exploit.

## The workbench layout

BEVM is panel-based, like a security IDE. A top bar holds the target and command bar; the
center hosts tabbed panels; an activity feed tracks running jobs. Open panels from the
sidebar and arrange them as tabs or floating windows.

!!! note "Aliases"
    Aliases are used everywhere: `attacker`, `target`, `me`, `zero`, `max`. Type an alias
    instead of a `0x` address in most inputs and BEVM resolves it for you.

## Load a target

In the **Smart Contract** panel, either *attach an existing contract* by address (BEVM
fetches its on-chain bytecode) or paste raw bytecode (`0x6080604052…`). Once loaded, BEVM
disassembles the code and exposes its function selectors, so every other panel can operate
on the target.

## Inspect

### Smart Contract
The entry point: attach a target by address or bytecode, give it an alias (e.g. `target`),
and view its disassembly and detected selectors. Every session begins here.

### Visual Graph (CFG)
Renders the contract's **control-flow graph** — basic blocks, jumps, and reachability. Click
**Run analysis** to build it, then *filter by selector or name* to focus on a single
function's paths. Use it to spot gated branches, computed jumps, and guarded code.

### Decoder
Encode and decode **calldata** and ABI-encoded values. Paste a 32-byte word or full
calldata, optionally provide an ABI signature, and BEVM decodes the head/tail of dynamic
types. Handy for reading a function's arguments or crafting one by hand.

### Storage
Read and write contract **storage slots** on your fork. Look up a slot by number or by a
declared mapping slot (key + base slot), using addresses or aliases as keys. Overriding
storage lets you set up the exact state a candidate exploit needs.

## Find

### Passive scan
Runs Trilocore's static **detector** suite over the bytecode — 200+ detectors across many
categories — and lists findings with their category and the code address they hit. Fast, no
execution; the first thing to run on any target.

### Active Scanner
Launches an **active scan job** that executes against the fork to find — and validate — real
issues. The panel shows job status and findings as they arrive; results are backed by fork
execution rather than static heuristics.

### Intruder (fuzz)
The fuzzing workbench. Mark **payload positions** in calldata and choose attack types (e.g.
*battering ram*) and wordlists — addresses (`attacker/target/zero/max`), `bytes32`
role/proxy-slot values, and more. BEVM's GPU fuzzer drives high-throughput execution to
surface inputs that move value or hit dangerous states.

## Prove

### Repeater
Hand-craft and re-send a single transaction: pick a selector, fill arguments, set the sender
(`attacker`), value, and gas, then send it against the fork and inspect the result/return
data. The tool for iterating on a precise exploit transaction.

### Block & Teleport
Compose and send exploit transactions with state setup. **Teleport** applies overrides
(balances, storage, etc.) and then sends the exploit tx — letting you reproduce an
attacker's starting conditions and confirm the outcome. Handles `DELEGATECALL` and
computed-jump targets in the flow.

### Multi-contract chains
Model exploits that span **several contracts** — e.g. flash loan → oracle → drain — as one
sequence, following `DELEGATECALL` and cross-contract calls. This is where real DeFi
kill-chains are reproduced end to end.

## More panels

### Heap Viewer
Inspect the EVM **memory/heap** and a call's profile during execution — calldata size,
annotations, and how memory evolves. Useful for understanding ABI layout and
memory-expansion behaviour.

### Transient
Analyzes **transient storage** (EIP-1153 `TSTORE`/`TLOAD`): which functions store/load
transient keys, cross-function key reuse, and related findings.

### Comparer
Diff two states or transaction results — compare storage/state before and after a
transaction to see exactly what an exploit changed.

### AI Explain
In-workbench AI help: **AI Explain** turns a revert/error or a finding into a plain-English
explanation, and **AI Tasks** suggests next steps. For deeper, attacker-style analysis, take
the finding to [Janus](janus.md).

---

!!! note "Support"
    Questions or want to support the project? Contact
    [saiteja@trilocore.com](mailto:saiteja@trilocore.com).
