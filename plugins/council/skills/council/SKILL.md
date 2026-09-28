---
name: council
description: This skill should be used when the user asks to "run the council", "convene the council", "have the council review", "should I pursue this idea", or wants a startup, feature, company decision or life choice evaluated by multiple perspectives with a rated verdict.
---

# Council

Convene the council to evaluate one idea and deliver a clear verdict. Ideas can range from a new startup to a product feature, a code or architecture change, an internal company process, or a personal life decision.

The council is made up of seven agents defined in this plugin, plus an advisory panel of three simulated industry figures picked for each idea. Each agent can also be called on its own.

| Order | Agent | subagent_type | Role |
|---|---|---|---|
| 1 | The Researcher | `council:researcher` | Builds a neutral factual baseline |
| 1 | The Recruiter | `council:recruiter` | Picks 3 real industry figures for the idea and writes a Persona Brief for each |
| 2 | The Believer | `council:believer` | Makes the strongest honest case for the idea |
| 2 | The Skeptic | `council:skeptic` | Attacks the idea and ranks its weaknesses |
| 2 | The Investor (optional) | `council:investor` | Judges profitability and opportunity only |
| 2 | The Advisory Panel (×3) | `council:advisor` | Each gives one panelist's simulated perspective from their Persona Brief |
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

## Step 1: The Researcher and the Recruiter (in parallel, first)

In a **single message**, launch both:
- `council:researcher` with the Idea Card and any files, repos or links the user provided. Tell it to adapt its research to the category.
- `council:recruiter` with the Idea Card. It picks three real industry figures (an Operator, a Capital & Strategy voice, and a Domain Expert & Contrarian) and returns an Advisory Panel with three Persona Briefs.

Wait for both the Research Brief and the Advisory Panel before moving on. Show the user the three panelists' names and roles in one line.

## Step 2: The debaters and the advisory panel (in parallel)

In a **single message**, launch all of these so they run in parallel:
- `council:believer`, `council:skeptic` and, if seated, `council:investor`. Pass each one the Idea Card and the full Research Brief.
- `council:advisor` **three times**, once per panelist. Pass each instance the Idea Card, the full Research Brief and **only its own** Persona Brief.

Do not share one member's output with another.

## Step 3: The Judge (last)

Launch `council:judge` with the Idea Card, the Research Brief, the complete outputs of the Believer, the Skeptic and the Investor, and the Advisory Panel (the Recruiter's output plus all three Advisor outputs). If the Investor was not seated, pass "Not seated: {reason}" in its place. Tell the Judge to treat the panel as simulated expert perspectives: weigh their reasoning, not their names.

## Step 4: Present the result

Lead with the verdict:
1. The Judge's full verdict block: the verdict, the rating, the breakdown table and the next steps.
2. A short section titled **"The Council's Arguments"** with 3 to 5 bullets each from the Believer, the Skeptic and the Investor, or a note that the Investor wasn't seated and why. Include each agent's closing score.
3. A section titled **"The Advisory Panel"** with, for each panelist: their name, role and seat, 2 or 3 bullets of their take, the hardest question they asked, and their score. Put this line under the heading: *Simulated perspectives based on public statements and track records. These are not the real people's views, and they have not reviewed this idea.*
4. The key sources from the Research Brief.
5. An offer to show the full agent transcripts, or to rerun the council on a revised version of the idea.

Keep the orchestration quiet. One line such as "The Researcher is gathering context and the Recruiter is seating the panel…" followed later by "The council is debating…" is enough.

## Guardrails

- **The order is mandatory:** Researcher and Recruiter, then the debaters and advisors in parallel, then the Judge. Never launch the debaters or advisors before the Research Brief and Advisory Panel exist.
- **Independence:** the debaters and advisors never see each other's output, and each advisor gets only its own Persona Brief. Only the Judge sees everything.
- **Simulated personas:** the advisory panel represents real people only as clearly labeled simulations grounded in their public record. Never present invented words as real quotes, and never imply the real person endorsed or reviewed the idea.
- **No fabrication:** every agent keeps sourced facts separate from estimates.
- **Proportionality:** scale the depth to the stakes.
- **Fallback:** if the Agent tool is unavailable, play each role in sequence using the instructions in `${CLAUDE_PLUGIN_ROOT}/agents/*.md`. Write out each role's full output before starting the next, and never revise an earlier role's output.
