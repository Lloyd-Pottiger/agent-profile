---
name: grill-me
description: Stress-test a plan or design through an interview that resolves material decisions within the current scope. Use when the user asks to stress-test a plan, get grilled on a design, or mentions "grill me".
---

Stress-test the plan within the agreed scope until we reach a shared understanding. Map unresolved decisions that materially affect behavior, data, security, cost, or operational risk as a **design tree**: each decision branches into the decisions that depend on it. State low-risk assumptions and continue rather than adding them as blocking questions.

Work the tree in **rounds**. Maintain the **frontier**: every unresolved decision whose prerequisites are settled. In each round, ask the entire frontier, number the questions, and give your recommended answer for each. A question that depends on any unsettled decision or fact—including another question in the current round—is not yet on the frontier and belongs to a later round. Wait for the user's answers, then update the tree and recompute the frontier instead of following a fixed questionnaire.

Facts are the agent's responsibility; decisions are the user's. Before grilling, inspect the current files, docs, recent commits, structure, and code. Read `CONTEXT.md` (or `CONTEXT-MAP.md` in a multi-context repo) and relevant ADRs under `docs/adr/`; work against the language and decisions already recorded there.

When a frontier question needs a fact available from the environment, dispatch a sub-agent to investigate it instead of asking the user. Treat the running investigation as an unsettled prerequisite: omit only its downstream questions from the current round, ask the rest of the frontier without waiting, then incorporate the result and recompute the frontier when the sub-agent reports.

The context/ADR file layout and formats live in the [`domain-modeling`](../domain-modeling) skill — see [CONTEXT-FORMAT.md](../domain-modeling/CONTEXT-FORMAT.md) and [ADR-FORMAT.md](../domain-modeling/ADR-FORMAT.md). Create files lazily, only when you have something to write.

## During the session

Run the [`domain-modeling`](../domain-modeling) discipline throughout the interview: challenge terms that conflict with the existing glossary, sharpen fuzzy language into precise canonical terms, stress-test relationships with concrete edge-case scenarios, and cross-reference the code when the user states how something works. Update `CONTEXT.md` inline the moment a term resolves, and offer an ADR sparingly — only when a decision is hard to reverse, surprising without context, and the result of a real trade-off. The full rules for each of those moves live in the `domain-modeling` skill.

Once the material decisions within the current scope are resolved and low-risk assumptions are recorded, ask the user to confirm the shared understanding and end the interview. Do not act on the plan before that confirmation.
