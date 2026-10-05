---
name: search-more-skill
description: Search the codebase much more before writing code. Trace the request through the code being changed from entry to exit across every boundary, find the closest existing analogue, and conform to what the surrounding code asks for. Use before any non-trivial edit, feature, or bug fix in an existing codebase.
---

# Search More

Agents that search more before editing resolve more tasks. Most failed changes do not fail because the code is hard to write. They fail because the agent did not understand where the change sits, so it invents a new pattern and becomes the weakest part of the system.

**Rule: do not write code until you can explain how a request flows through the area you are changing, and which existing code your change should look like.**

## 1. Search before you write

- Search first, then read. Use grep, glob, symbol search, and LSP references and definitions.
- Search for the symbol, its callers, its tests, its config keys, its error messages, and its log strings.
- If a search returns nothing, assume your query was wrong, not that the thing doesn't exist. Rephrase it: try synonyms, casing variants, a partial name, or the type it returns.
- Read whole sections, not single matching lines. Most of the context is in the surrounding 50 lines.
- Spend about 40% of your actions on searching and reading before your first edit. If your first edit comes within your first few actions, you started too early.

## 2. Trace the request from entry to exit

Find the code you are about to change, then follow a real request through it across every boundary:

1. **Entry:** where does the request come in? Look at the route, CLI command, event handler, job, exported API, or test harness.
2. **Inbound boundary:** how is input parsed, validated, authenticated, and converted into internal types?
3. **Core path:** follow each call down to the code you are changing. Note the types, invariants, and assumptions at every hop.
4. **Outbound boundaries:** what does it call out to (DB, network, filesystem, queue, other modules)? How are errors, retries, and transactions handled there?
5. **Exit:** how does the result get back to the caller? Look at the serialization, status codes, return types, and emitted events.

Write the trace down as a short list of `file:symbol` hops. If you can't fill in a hop, keep searching. Gaps in the trace are where bugs come from.

## 3. Find something similar

Before designing anything, find the closest existing analogue:

- Is there another endpoint, command, handler, or module that already does something like this?
- How does it validate input, name things, structure files, handle errors, log, and test?
- Search for its pattern, not just its name. If there are two or three analogues, use the one most recently or most consistently used.

Copy the shape of the analogue. Your change should look like it was written by the people who wrote the code around it.

## 4. Ask what the codebase is asking for

The surrounding code already shows you what fits:

- **Conventions:** naming, file layout, import style, error types, result and option patterns.
- **Existing helpers:** utilities, wrappers, clients, and test fixtures you should reuse instead of reimplementing.
- **Boundaries:** which layer owns validation, persistence, and formatting. Don't move responsibility across layers.
- **Tests:** where tests for this area live and what style they use.

If you are about to add a dependency, a new abstraction, a second way of doing something that already has one way, or a helper that duplicates an existing one, stop and search again.

## 5. Don't become the weak point

Code that ignores local conventions or injects unrelated changes is fragile: reviewers miss it, future readers misread it, and it breaks under assumptions everyone else follows.

- Change only what the task requires. No drive-by refactors, renames, or reformatting.
- Don't invent new patterns when the codebase already has one.
- Every new line should be explainable through your trace (step 2) and your analogue (step 3).

## Checklist before the first edit

- [ ] I searched for the target, its callers, its tests, and related strings, and rephrased any searches that came back empty.
- [ ] I can list the entry → boundaries → core → exit trace as `file:symbol` hops.
- [ ] I found the closest existing analogue and know which parts of it I'm copying.
- [ ] I know which existing helpers, types, and test fixtures I'll reuse.
- [ ] My planned change touches only what the task requires and follows local conventions.
