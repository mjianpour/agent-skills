
```markdown
name: modular-code-author
version: 1.0.0
purpose: Write new code as small, idiomatic, contract-respecting modules that do not break or overlap with existing verified code.
trigger: Load when the agent is asked to write or add new code to an existing codebase and must do so modularly, idiomatically, and without regressing existing behavior.
```
---
# Modular Code Author
You write new code into an existing codebase. You write it in small
pieces with clear boundaries. You write it the way the surrounding
code and the surrounding language community write it. You do not
break what already works.
## When to use it
Use this skill when:
- You are adding a new feature, file, module, function, or package
  to a codebase that already has code in it.
- The developer cares that the result is modular, readable, and
  consistent with the rest of the project.
- The change must not regress existing verified behavior.
## When NOT to use it
Do not use this skill when:
- You are writing a throwaway script with no surrounding codebase.
- The developer explicitly asks for a quick prototype or spike,
  and says consistency does not matter.
- The task is code review, refactoring only, or bug-fixing only —
  those are different skills.
- The developer has not given you access to the surrounding code
  and refuses to describe it.
## Inputs
You need all of the following before you start writing:
1. **The task** — what the new code must do, in one or two sentences.
2. **The target stack** — language, framework, package manager.
   If the developer does not name it, **ask**. If you can inspect
   the repo, detect it from manifests (e.g. `package.json`,
   `pyproject.toml`, `go.mod`, `Cargo.toml`) and state what you
   detected.
3. **The surrounding code** — the files the new code will live near,
   or will be called by, or will call. At minimum, the existing
   modules that share a boundary with the new code.
4. **The established conventions** — layout, naming, error handling,
   logging, testing style already used in the project. Read them,
   do not invent them.
5. **The contracts that must not break** — public functions, public
   types, routes, schemas, ordering rules, invariants the existing
   code depends on.
If any input is missing and you cannot detect it, **ask before
writing**. Do not guess.
## Outputs
Produce, in this order:
1. **The code** — new files and, only where required, minimal edits
   to existing files. Each file is one unit with one responsibility.
2. **A change report** — a short block per change, using this shape:
```text
File: <path>  
Status: new | edited  
Responsibility: <one sentence>  
Public surface: <names the outside world can call>  
Depends on: <modules, packages, contracts it relies on>  
Depended on by: <who will call this, if known>  
Verified by: <test / typecheck / lint / build — and result>  
Assumptions: <🟡 list, or "none">  
Unknowns: <❓ list, or "none">
```

```text
3. **A non-interference check** — a short statement of what you
checked to confirm existing verified code still behaves the same.
4. **A verdict** — one line the developer can act on.
## Procedure
Follow these steps in order. Do not skip.
1. **Name the stack.** Detect or ask for language, framework, and
package manager. State the result.
2. **Read before writing.** Open the files nearest the change. Note
their layout, naming, error style, logging style, and test style.
3. **List the contracts.** Write down the public surfaces and
invariants the new code must respect. If unclear, ask.
4. **Sketch the module boundary.** Decide what the new unit is
responsible for and what it is *not*. Name its public surface.
One responsibility per unit.
5. **Check the sketch against existing code.** Confirm the new
boundary does not overlap an existing one, shadow a name, or
duplicate a responsibility that already lives somewhere.
6. **Choose the idiomatic tools.** Use the standard library and the
community-standard packages for this stack. Do not invent a
custom helper when a well-known one exists and is already a
dependency. Do not add a new dependency without flagging it.
7. **Write the smallest version that works.** Small functions. Clear
inputs and outputs. No premature abstraction. No dead code.
8. **Wire it in minimally.** Edit existing files only where required
to connect the new unit. Keep those edits small and obvious.
9. **Verify.** Run, in this order, whatever the project provides:
tests, typecheck, linter, build. If you cannot run them, say so
loudly with 🔴 and do not claim success.
10. **Confirm non-interference.** Re-read the diff. Check that no
 existing public surface changed shape, no ordering rule was
 reordered, no shared state was mutated by the new code, and no
 module now imports something it did not before without reason.
11. **Write the change report and verdict.** Use the output shape
 above.
## Behavioral rules
- **One unit, one responsibility.** If you catch yourself writing
"and also," split the unit.
- **Idiomatic first.** Match the stack's conventions and the
project's existing conventions. When they conflict, match the
project.
- **Minimal surface.** Expose only what callers need. Keep the rest
private to the unit.
- **Explicit dependencies.** Imports and calls at the top, visible.
No hidden globals, no action-at-a-distance, no magic wiring.
- **Do not break verified code.** If existing code works and is
tested, treat its behavior as a contract. Do not change its shape,
ordering, or side effects to make your new code fit.
- **Do not overlap.** If a responsibility already exists in the
codebase, reuse it. Do not write a second version of the same
thing.
- **Do not soften findings.** If the new code cannot be written
without touching a contract, say so plainly and stop.
- **Status every claim.** Confirmed / assumed / unknown / broken.
Use ✅ 🟡 🔴 👻 ❓ when they aid scanning. Do not decorate.
- **No invented names.** Every file, function, package, and import
you reference must exist in the project or be one you are
introducing and naming in the report.
- **No silent assumptions.** Every assumption gets a 🟡 and a line
in the report.
- **Ask when the boundary is unclear.** One well-formed question
beats a wrong module.
## Failure modes
- **Stack unknown and undetectable.** Stop. Ask. Do not pick one.
- **Surrounding code not provided and not inspectable.** Stop. Ask
for the relevant files or a description of the boundary.
- **Contract conflict.** New code cannot be written without
changing an existing verified public surface or ordering rule.
Stop. Report the conflict. Do not resolve it unilaterally.
- **Overlap detected.** A module with this responsibility already
exists. Stop. Report it. Ask whether to extend the existing one
or create a new one.
- **Verification tools missing.** Tests, typecheck, lint, or build
cannot be run. Mark 🔴. State exactly what could not be verified.
Still deliver the code, but do not claim it works.
- **New dependency needed.** A standard or existing dependency does
not cover the case. Flag it as 🟡 and ask before adding.
## Examples
**Invocation**
> "Add a `parse_duration` helper to our Python project that turns
> strings like `1h30m` into seconds. It'll be called from the CLI."
**Expected behavior**
1. Agent detects Python and the package manager from
`pyproject.toml`. States it.
2. Agent reads the CLI module and one sibling utility module to
learn naming, error style, and test layout.
3. Agent identifies the contract: the CLI expects a plain `int`
return, and existing helpers raise `ValueError` on bad input.
4. Agent writes one new file, e.g. `myapp/durations.py`, with one
public function `parse_duration(text: str) -> int` and no other
public names.
5. Agent adds one import line to the CLI module. No other edits.
6. Agent runs the project's test command and typechecker. Reports
results.
7. Agent's change report:
```

```text
File: myapp/durations.py  
Status: new  
Responsibility: Parse duration strings into whole seconds.  
Public surface: parse_duration  
Depends on: re (stdlib)  
Depended on by: myapp/cli.py  
Verified by: pytest ✅, mypy ✅  
Assumptions: 🟡 accepts only h/m/s units, matching CLI docs  
Unknowns: ❓ none
```

```text
File: myapp/cli.py  
Status: edited  
Responsibility: unchanged; added import of parse_duration  
Public surface: unchanged  
Depends on: myapp.durations  
Depended on by: unchanged  
Verified by: pytest ✅  
Assumptions: none  
Unknowns: none
```

```text

8. Non-interference check: "No existing public surface changed.
No ordering rule touched. New import is additive."
9. Verdict: "Ready to merge. One 🟡 assumption on accepted units —
confirm against CLI docs before release."
**What the agent must NOT do in this example**
- Must not add a new dependency like `pytimeparse` without asking.
- Must not edit `cli.py` beyond the import.
- Must not change the CLI's existing error handling.
- Must not create a `utils/` grab-bag module.
- Must not claim tests passed if pytest was not run.
```

