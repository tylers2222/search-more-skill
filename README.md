# search-more-skill

An agent skill that makes coding agents **search and trace the codebase before they write code**, then fit their change to what's already there.

## Why use it

Coding agents rarely fail because the code is hard to write. They fail because they edit too early. They don't understand how a request flows through the code they're changing, so they invent a new pattern next to an existing one, duplicate a helper, or break an assumption every other caller relies on. That patch becomes the weakest part of the system.

This skill makes the agent do four things before its first edit:

1. **Search more.** Look up symbols, callers, tests, config keys, and error strings, and rephrase searches that come back empty instead of giving up.
2. **Trace entry → exit.** Follow a real request across every boundary (input parsing and validation, the core code, outbound calls like DB or network, and how the result returns), written down as `file:symbol` hops.
3. **Find the closest analogue.** Locate existing code that already does something similar and copy its shape.
4. **Conform.** Reuse existing helpers, conventions, and layer boundaries. No drive-by refactors, no second way of doing something that already has one.

## What the research says

Software engineering (SWE) agent research keeps landing on the same point: **finding the right code (localization) is a bottleneck, and how much and how well the agent searches changes outcomes.**

- **SWE-agent** (Yang et al., 2024, [arXiv:2405.15793](https://arxiv.org/abs/2405.15793)). The SWE-agent authors found that agents searching with plain shell commands (chains of `cd`/`ls`/`cat`) are inefficient at finding relevant code. In their search-interface ablation on SWE-bench Lite (GPT-4), a summarized search tool scored **18.0%** resolved, against **15.7%** with no dedicated search tool and **12.0%** with an iterative one-result-at-a-time search. Better search turned into more resolved issues, and a worse search design actively hurt.
- **Agentless** (Xia et al., 2024, [arXiv:2407.01489](https://arxiv.org/abs/2407.01489)). This approach splits issue-fixing into an explicit *localization* phase (narrowing down from files to classes and functions to exact edit locations) followed by a *repair* phase, and is competitive with far more complex agents. Working out where to change code is a first-class step of its own, not a side effect of editing.
- **Terminal-Universe** (Wu et al., 2026, [arXiv:2609.04148](https://arxiv.org/abs/2609.04148)). This paper rebuilds workspaces from recorded agent trajectories, using only the files the agent read and edited. From that trace alone, only **20.1%** of SWE workspaces had enough project context for their task, versus **40.2%** of terminal workspaces. SWE tasks depend on much more surrounding code than an agent typically looks at, which is exactly the context this skill makes the agent go and read.
- **Is Grep All You Need?** (2026, [arXiv:2605.15184](https://arxiv.org/abs/2605.15184)). This is a retrieval benchmark over long conversation histories, not an SWE benchmark. Plain lexical search (grep) returned inline beat vector retrieval for every harness–model pair tested, and the agent harness moved accuracy about as much as swapping the retriever did. Simple, repeated, well-scoped search is a strong default.

The takeaway: spend more of the agent's budget understanding the code before changing it. This skill is a prompt-level way to enforce that.

## Install

Clone into your agent's skills directory, for example Claude Code:

```sh
git clone https://github.com/tylers2222/search-more-skill ~/.claude/skills/search-more-skill
```

Any agent that supports the `SKILL.md` format can load it the same way. The full instructions and pre-edit checklist are in [`SKILL.md`](SKILL.md).
