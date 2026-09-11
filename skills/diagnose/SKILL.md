---
name: diagnose
description: Disciplined diagnosis loop for bugs, test failures, unexpected behavior, and performance regressions. Build a reliable feedback loop before fixing; reproduce, compare, hypothesize, instrument, fix, regression-test, and clean up.
---

# Diagnose

A disciplined loop for hard bugs and performance regressions. Use this before proposing fixes.

Establish evidence of the reported failure before changing production code. Prefer an agent-runnable reproduction; use captured traces or other direct evidence when reproduction is unavailable. Do not present an unverified hypothesis as a confirmed cause.

Use the project's domain glossary and ADRs when they exist.

## Phase 1: Build a Feedback Loop

Use the smallest reliable check that demonstrates the user's symptom. For intermittent or hard-to-reproduce failures, invest in improving the signal before attempting a fix.

Choose a suitable feedback mechanism:

1. Failing test at the seam that reaches the bug: unit, integration, or e2e.
2. CLI or HTTP script with fixture input and expected output.
3. Headless browser script that asserts DOM, console, and network behavior.
4. Replay a captured trace: request payload, event log, HAR, core dump, or fixture dataset.
5. Throwaway harness that starts the smallest subset of the system needed to exercise the path.
6. Property, fuzz, stress, or repetition loop for intermittent output or timing failures.
7. Bisection harness for commit, dataset, version, or configuration regressions.
8. Differential loop that compares old versus new behavior, or two configurations.
9. Human-in-the-loop script only as a last resort; use `scripts/hitl-loop.template.sh`.

Improve the loop before relying on it:

- Make it faster by narrowing setup and skipping unrelated work.
- Make the signal sharper by asserting the exact symptom.
- Make it deterministic by pinning time, seeds, filesystem state, and network boundaries.

For non-deterministic bugs, raise the reproduction rate first. Loop the trigger, add stress, run in parallel, or inject timing probes until the failure is frequent enough to debug.

If no loop can be built, document what was tried and inspect available captured evidence. Continue independent investigation within existing authorization. If confirming the cause requires missing access, artifacts, or instrumentation outside that authorization, request the specific prerequisite before the dependent work.

## Phase 2: Reproduce and Read the Evidence

Run the reproduction when available. Otherwise, inspect captured evidence and record the conditions it covers and what remains unobserved.

Confirm:

- The failure matches the user-reported symptom.
- The reproduction rate is adequate for investigation, or captured evidence establishes the failure under identified conditions.
- The exact error, wrong output, or timing regression is captured.
- Error messages, stack traces, file paths, warning text, and error codes have been read completely.
- Recent changes have been checked: diffs, commits, dependencies, configuration, environment, data shape, and deployment changes.

Evidence of the failure permits further investigation; it does not confirm a cause. If neither reproduction nor captured evidence establishes the reported failure, keep hypotheses provisional and seek the missing evidence before proposing a fix.

## Phase 3: Compare Working and Broken Paths

Before forming a fix, find the pattern:

- Locate similar working code or a known-good version.
- Read the relevant reference implementation completely enough to understand the pattern.
- List concrete differences between working and broken behavior.
- Identify required dependencies, configuration, environment, ordering, data invariants, and caller assumptions.

For multi-component systems, instrument boundaries before guessing:

- Log or inspect what enters and exits each component.
- Verify environment and configuration propagation at each layer.
- Check state at service, RPC, database, cache, filesystem, queue, and signing/build boundaries as relevant.
- Distinguish where the data first becomes wrong from where the error finally surfaces.

## Phase 4: Hypothesize

Generate 3 to 5 ranked hypotheses before testing any one of them.

Each hypothesis must be falsifiable:

```text
If X is the cause, then changing or observing Y will make the bug disappear,
move, or become more severe.
```

Discard vague hypotheses that do not predict an observable result. Share the ranked list with the user when useful; proceed with the best ranking if the user is unavailable.

## Phase 5: Instrument and Test One Variable

Each probe must map to one hypothesis. Change one variable at a time.

Preferred probes:

1. Debugger or REPL inspection when available.
2. Targeted boundary logs or assertions.
3. Focused metrics, timings, profiler output, query plans, or traces for performance regressions.

Tag temporary logs with a unique prefix such as `[DEBUG-a4f2]` so cleanup is mechanical. Do not "log everything and grep".

If a hypothesis fails, use the new evidence to form the next hypothesis. Do not stack unrelated fixes.

If several fix attempts reveal different coupling or shared-state problems, stop and question the architecture before trying another patch.

## Phase 6: Fix and Regression-Test

Proceed when evidence connects the observed failure to a causal mechanism and supports it over plausible competing hypotheses. Documenting a missing reproduction alone does not satisfy this condition.

1. Build a regression test from the minimized reproduction or captured failure conditions when there is a correct seam.
2. Verify the regression test fails before the fix.
3. Apply the smallest fix that addresses the confirmed cause.
4. Verify the regression test passes.
5. Run the original unminimized scenario when available if it exercises behavior the regression test does not cover. If it remains unavailable, report that limit separately from the regression-test result.

A correct seam exercises the real bug pattern as it occurs at the call site. If regression coverage cannot be added, document the specific limitation and use an alternative check of the affected behavior. If no adequate check can run, report the candidate fix as unverified rather than declaring the issue fixed.

Use defensive validation only after the root cause is understood. Add it at boundaries where it preserves an invariant or improves failure clarity; do not hide the bug behind fallback behavior.

## Phase 7: Cleanup and Post-Mortem

Before declaring the issue fixed:

- Remove all temporary debug logs and probes; search for the debug prefix. Re-run affected checks if cleanup changes the tested behavior.
- Report the reproduction and regression-test results, or explain why regression coverage could not be added.
- Delete throwaway harnesses or move them to a clearly marked debug location if intentionally retained.
- State the confirmed cause and evidence in the commit or PR message.

Then ask what would have prevented the bug. If the answer is architectural, hand off the specific finding to an architecture/refactoring workflow after the fix is verified.
