---
name: council
description: This skill should be used when the user asks to "run the council", "convene the council", "have the council review", "should I pursue this idea", or wants a startup, feature, company decision or life choice evaluated by multiple perspectives with a rated verdict.
---

# Council

Convene the council to evaluate one idea and deliver a clear verdict. Ideas can range from a new startup to a product feature, a code or architecture change, an internal company process, or a personal life decision.

The council is made up of five agents defined in this plugin. Each one can also be called on its own.

| Order | Agent | subagent_type | Role |
|---|---|---|---|
| 1 | The Researcher | `council:researcher` | Builds a neutral factual baseline |
| 2 | The Believer | `council:believer` | Makes the strongest honest case for the idea |
| 2 | The Skeptic | `council:skeptic` | Attacks the idea and ranks its weaknesses |
| 2 | The Investor (optional) | `council:investor` | Judges profitability and opportunity only |
| 3 | The Judge | `council:judge` | Gives a rated final verdict |

Launch each member with the Agent tool using its `subagent_type`. If the plugin-namespaced type is not available, launch a `general-purpose` agent and paste in the body of the matching file from `${CLAUDE_PLUGIN_ROOT}/agents/` as its instructions. Keep the members separate. Never merge roles or write a member's output yourself.

## Step 0: Frame the idea

Write a short **Idea Card** with these fields:
- **Idea:** 1 or 2 sentences.
- **Category:** startup / new business, product feature / function, code / technical change, internal company process or decision, personal / life decision, or other.
- **Context given:** who is proposing it, their constraints (budget, team, timeline, skills), and any files, repos or links the user provided.
- **Success looks like:** what "good" means here.
- **Investor seat:** `IN` or `OUT`, with a one-line reason.

**Seat the Investor** only when the idea is a business matter where profit, ROI or commercial opportunity is central. Examples include startups, new product lines, monetized features, pricing changes and investment decisions. Leave it out for personal decisions (unless they are primarily financial), purely technical refactors, and internal processes where profit isn't the point. When unsure, leave it out and say why.

If the idea is too vague to evaluate, ask one clarifying question first. Otherwise state your assumptions in the Idea Card and proceed. Show the user the Idea Card in one or two lines.

## Step 1: The Researcher (alone, first)

Launch `council:researcher` with the Idea Card and any files, repos or links the user provided. Tell it to adapt its research to the category. Wait for the Research Brief before moving on.

## Step 2: The debaters (in parallel)

In a **single message**, launch `council:believer`, `council:skeptic` and, if seated, `council:investor`, so they run in parallel. Pass each one the Idea Card and the full Research Brief. Do not share one debater's output with another.

## Step 3: The Judge (last)

Launch `council:judge` with the Idea Card, the Research Brief, and the complete outputs of the Believer, the Skeptic and the Investor. If the Investor was not seated, pass "Not seated: {reason}" in its place.

## Step 4: Present the result

Lead with the verdict:
1. The Judge's full verdict block: the verdict, the rating, the breakdown table and the next steps.
2. A short section titled **"The Council's Arguments"** with 3 to 5 bullets each from the Believer, the Skeptic and the Investor, or a note that the Investor wasn't seated and why. Include each agent's closing score.
3. The key sources from the Research Brief.
4. An offer to show the full agent transcripts, or to rerun the council on a revised version of the idea.

Keep the orchestration quiet. One line such as "The Researcher is gathering context…" followed later by "The council is debating…" is enough.

## Guardrails

- **The order is mandatory:** Researcher, then the debaters in parallel, then the Judge. Never launch the debaters before the Research Brief exists.
- **Independence:** the debaters never see each other's output. Only the Judge sees everything.
- **No fabrication:** every agent keeps sourced facts separate from estimates.
- **Proportionality:** scale the depth to the stakes.
- **Fallback:** if the Agent tool is unavailable, play each role in sequence using the instructions in `${CLAUDE_PLUGIN_ROOT}/agents/*.md`. Write out each role's full output before starting the next, and never revise an earlier role's output.
