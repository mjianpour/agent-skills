## Purpose
Make sure any code the agent writes fits the codebase it's entering.
No duplicate symbols. No broken callers. No style clashes. No
contradicting behavior. The agent writes with confidence because it
has already checked every place its change touches.
## When to use it
Use this skill any time the agent is about to:
- Add a new function, class, type, route, or module.
- Change an existing function's signature, return shape, or behavior.
- Rename or move a symbol.
- Add a dependency, config key, or env var.
## When NOT to use it
- Greenfield code with no existing project around it.
- Pure documentation edits.
- Formatting-only changes that touch no symbols or callers.
## Inputs
Before writing anything, the agent needs:
- The task in one sentence.
- The file(s) the change will live in.
- The ability to search the codebase (grep, ripgrep, IDE search).
If the agent cannot search the codebase, it stops and says so.
It does not guess.
## Outputs
1. The code change.
2. A short **Conflict Report** with this exact shape:

```text

CHANGE: <one line>  
FILES TOUCHED: <list>  
SYMBOLS ADDED: <list, or "none">  
SYMBOLS CHANGED: <list, or "none">  
CALLERS UPDATED: <list, or "none">  
NAME COLLISIONS CHECKED: yes  
STYLE MATCHED: <which existing file was used as reference>  
MANUAL CHECKS FOR YOU:

- <command or step the developer should run>
- ...  
    CONFIDENCE: high | medium | low (with one-line reason if not high)
    
```

```text
## Procedure
1. **Restate the task** in one sentence.
2. **Find the target file(s).** Read them fully before editing.
3. **Search for every symbol you plan to add or change.** Use the
   codebase search. If the name already exists, stop — pick a
   different name or plan an explicit rename with caller updates.
4. **Find every caller** of any symbol you plan to change. List them.
5. **Pick a reference file** — the closest existing file that already
   does something similar. Match its:
   - naming style
   - import order
   - error handling
   - comment density
   - file layout
6. **Write the change** using the reference file as the template.
7. **Update every caller** you found in step 4 so the project stays
   internally consistent.
8. **Re-search** for the symbol names you added or changed. Confirm
   zero unexpected matches remain.
9. **Produce the Conflict Report.** Do not skip it.
## Behavioral rules
- Never invent a name without searching for it first.
- Never change a symbol's signature without updating all its callers
  in the same pass.
- Never introduce a second way to do something the codebase already
  does (two HTTP helpers, two date formatters, two config loaders).
  Reuse the existing one.
- Never add a dependency the project doesn't already use unless the
  task requires it — and if it does, say so in the report.
- Match existing conventions even if you'd write it differently.
- Write with confidence. If you've done steps 2–5, you know enough.
  Do not hedge in the code with "maybe" or "TODO: verify".
- If you genuinely cannot verify something, put it in the Conflict
  Report under MANUAL CHECKS — not in the code as a comment.
## Failure modes
- **Cannot search the codebase.** Stop. Report that you need search
  access. Do not write.
- **Symbol name already exists.** Do not overwrite. Either rename
  your new symbol or plan an explicit rename of the old one with all
  callers updated. Ask the developer which they prefer.
- **A caller is in a file you don't understand.** Stop and ask. Do
  not edit code you can't reason about.
- **Two existing patterns contradict each other.** Follow the more
  recent one, note the choice in the report under STYLE MATCHED.
- **Task requires breaking a public contract.** Say so up front
  before writing. List the callers. Ask for confirmation.
## Example
Task: "Add a `retry(fn, n)` helper."
Agent actions:
1. Restates: add a retry helper.
2. Finds `src/utils/`.
3. Searches `retry` → no matches. Name is free.
4. No callers to update (new symbol).
5. Reads `src/utils/backoff.ts` — matches its style (named export,
   JSDoc one-liner, no default export).
6. Writes `src/utils/retry.ts` in that style.
7. (no callers)
8. Re-searches `retry` → only the new file. Clean.
9. Emits Conflict Report.
```

```text 
CHANGE: add retry(fn, n) helper  
FILES TOUCHED: src/utils/retry.ts (new)  
SYMBOLS ADDED: retry  
SYMBOLS CHANGED: none  
CALLERS UPDATED: none  
NAME COLLISIONS CHECKED: yes  
STYLE MATCHED: src/utils/backoff.ts  
MANUAL CHECKS FOR YOU:

- npm test   
- npm run lint  
    CONFIDENCE: high
```
