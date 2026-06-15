# Janus — AI analyst

Janus is an AI model that reasons like an attacker. Give it a contract or a finding and it
explains the vulnerability in plain English, walks the attack chain, and suggests how to
reproduce or fix it.

## What Janus is

A chat console backed by an EVM-specialized model. Unlike a general chatbot, it's tuned for
smart-contract security: attack-chain reasoning, exploit explanation, and turning raw
findings into readable write-ups. It complements [BEVM](bevm.md) — BEVM finds and proves;
Janus explains and reasons.

## Getting started

1. Open **[janus.trilocore.ai](https://janus.trilocore.ai/)** and sign in (same account as BEVM).
2. Start a new chat and describe what you're looking at — a contract address, a snippet of
   bytecode/Solidity, or a finding from BEVM.
3. Ask your question (see examples below) and iterate in the conversation.

## What to ask

Janus is strongest on concrete, security-specific prompts. Examples:

- "Analyze this contract — where could an attacker extract value?"
- "Explain this BEVM finding in plain English and show the attack chain."
- "This call reverts with `0x…` — what does that revert mean and how do I get past it?"
- "How would I chain a flash loan + oracle manipulation against this pool?"
- "Given this calldata and storage layout, craft the exploit transaction."

## Attachments & context

You can include context with your message — paste bytecode, calldata, a Solidity snippet, or
a finding. The more precise the context (selector, calldata, addresses, storage values), the
sharper Janus's reasoning. Copy these straight from BEVM's Decoder, Storage, or Scanner
panels.

## Sessions

Conversations are kept as sessions in the sidebar, so you can return to an analysis later.
Keep one chat per target/investigation to preserve context.

## Using Janus with BEVM

1. In BEVM, run a passive/active scan or a fuzz and get a finding.
2. Copy the finding details (type, selector, calldata, profit/wei, addresses).
3. Paste them into Janus and ask for a plain-English explanation + attack chain.
4. Take Janus's suggested transaction back to BEVM's [Repeater](bevm.md#prove) /
   [Block](bevm.md#prove) and replay it on the fork to confirm.

!!! tip "The loop"
    **BEVM finds & proves → Janus explains & suggests → BEVM replays to confirm.**

## Tips

- Be specific — paste real bytecode/calldata rather than describing it.
- Treat output as expert reasoning to *verify*, not gospel — always confirm an exploit by
  replaying it on a fork in BEVM.
- Iterate: if the first attack path is blocked by a gate, tell Janus what reverted and ask
  for the next path.

---

!!! note "Support"
    Questions or want to support the project? Contact
    [saiteja@trilocore.com](mailto:saiteja@trilocore.com).
