# be-assess

A Claude Code skill that assesses web applications through a behavioral economics lens. It navigates a running app like a real user, scores friction and motivation at each step, and produces a structured Markdown report with prioritized recommendations.

This is a scalpel, not a broadsword. It works best when you arrive with a hypothesis.

## What it does

- **Pre-flight intake** — Before touching the browser, the skill asks what you want assessed and why. This keeps the assessment targeted and makes the findings more actionable.
- **Top-level discovery** — Navigates the app's primary navigation, maps discovered flows against your stated goals, and asks you to select which ones to deep-dive.
- **Scored assessment** — Each step in a selected flow is scored on Friction (cognitive load) and Motivation (behavioral levers), with additional analysis for defaults, peak-end moments, loss aversion risks, and dark patterns.
- **Structured report** — Outputs a Markdown file with an executive summary, Behavioral Impact Matrix, per-flow findings, and a prioritized action list.

## Requirements

- Claude Code with the Chrome DevTools MCP configured
- A running web application accessible at a local or remote URL

## Usage

```
/be-assess https://your-app.com
```

The skill will run a short intake before opening the browser — what the app does, which flows you want assessed, and any hypotheses you have going in. Answer all three before it starts navigating.

### Skip intake with `--quick`

If you already know what you want and don't need the guided setup:

```
/be-assess https://your-app.com --quick
```

Proceeds directly to top-level nav discovery. You'll still select which flows to assess before the deep-dive begins.

## How it works

### Phase 0 — Pre-flight
Three intake questions: what the app does, which flows to assess, and existing hypotheses. The skill won't open the browser until you've identified at least one flow to target.

### Phase 1 — Discovery
Navigates top-level navigation only. Captures screenshots and page structure at each section. Presents a flow map cross-referenced against your Phase 0 goals. You select which flows get the full assessment — if you pick more than 5, the skill pauses to confirm scope.

### Phase 2 — Deep-Dive Assessment
For each selected flow, step by step:
- Screenshots at every state
- Friction + Motivation scoring (1–10 each)
- Default analysis (user-aligned vs. business-aligned vs. missing)
- Peak-end evaluation (where does the flow peak, how does it end)
- Loss aversion risk flags
- Dark pattern detection (neutral, flag-and-verify — not accusatory)
- WAM diagnostic for adoption/collaboration flows

After each flow completes, the skill pauses with a summary and asks before continuing to the next one.

### Phase 3 — Report Generation
Confirms the output path, then writes a Markdown report to `./be-assessment/be-assessment-YYYY-MM-DD.md` (or a path you specify).

## Output

The report includes:

- **Executive summary** — Top findings, written directly
- **Behavioral Impact Matrix** — One row per flow, columns for Friction, Motivation, F:M ratio, Default Score, Peak-End Impact, Dark Pattern Flags, Loss Aversion Risk, and Final Priority
- **Per-flow deep-dives** — Step-by-step scoring with screenshots, findings, and recommendations grounded in BE principles
- **Prioritized action list** — Ranked Critical → High → Medium → Low
- **Dark Pattern Review Queue** — Items flagged for team verification with specific questions

## Behavioral frameworks used

| Framework | What it measures |
|-----------|----------------|
| Friction-Motivation Score | Cognitive load vs. behavioral motivators at each step |
| Default Analysis | Whether pre-selected states serve users or the business |
| Peak-End Rule | Most memorable moment + how the flow concludes |
| Loss Aversion Assessment | Risks where perceived losses outweigh objective gains |
| Dark Pattern Taxonomy | Neutral detection with verification prompts |
| Willingness-Awareness Matrix | Adoption failure diagnosis for collaboration flows |

## Reference docs

The scoring rubrics and behavioral frameworks are drawn from the documents in [`docs/`](./docs/):

- `Behavioral Economics_ Principles and Applications.md`
- `Behavioral Economics in UX Decisions.md`
- `Behavioral Economics Guide Takeaways.md`
