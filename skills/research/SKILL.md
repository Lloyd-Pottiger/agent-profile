---
name: research
description: Investigate a question against high-trust primary sources and capture the findings as a Markdown file in the repo. Use when the user wants a topic researched, docs or API facts gathered, or reading legwork delegated to a background agent.
---

# Research

Investigate a bounded research question locally. Delegate only when the user explicitly authorizes delegation and it offers a concrete benefit such as context isolation or parallel progress.

1. Define the exact question, scope, and output path. Follow the repository's existing research-note convention; if none exists, choose a concise repo-local path and report it.
2. Research locally, or, when delegation is authorized and useful, spawn one background agent with the question, relevant repository context, output path, and the evidence contract below. Give it the research task directly; do not ask it to invoke `$research` again.
3. In either mode:
   - use high-trust primary sources such as official documentation, source code, specifications, and first-party APIs;
   - trace material claims to the source that owns them, distinguish evidence from inference, and expose conflicting or missing evidence;
   - write one self-contained Markdown file with findings and citations at the assigned path;
   - avoid modifying implementation, tracker state, or unrelated files.
4. When delegating, continue independent work while the agent researches and wait for its result before reporting completion. In either mode, inspect the artifact and verify that the question is answered and material claims are source-backed.

When delegation is authorized and useful for multiple independent questions, use one agent and one non-conflicting artifact per question, parallelized within the environment's available agent capacity.
