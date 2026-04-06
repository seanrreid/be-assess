# PRD: `be-assess` — Behavioral Economics Assessment Skill

## One-Line Description

A targeted Claude Code skill that assesses specific, user-identified flows in a running web application and produces a scored behavioral economics report with prioritized recommendations. This is a scalpel, not a broadsword.

## Problem & Audience

Product teams and developers ship features optimized for functionality and aesthetics but rarely audit the *behavioral environment* — how defaults, friction, cognitive load, and choice architecture actually shape user decisions. Existing UX review tools focus on accessibility or usability heuristics, not on the behavioral economics principles that drive adoption, engagement, and retention.

This skill is for **developers and product leads** who want a structured, repeatable behavioral audit they can run against any web application — and get back a Markdown artifact they can share with stakeholders or feed back into Claude for implementation work.

## Core Features (Ranked by Priority)

1. **Pre-flight Intake + Targeted Discovery** — Before touching the browser, the skill runs a structured intake: what does the app do, which flows should be assessed, and what are the user's hypotheses? Only then does it navigate — starting from the root URL but mapping *top-level navigation only*, not crawling the full app. Discovery is oriented by the user's stated goals, not open-ended reconnaissance. A `--quick` flag skips intake and proceeds with defaults for repeat users. This is the foundation everything else depends on.

2. **Friction-Motivation Scoring (1-10)** — Each step in a flow gets scored on Friction (cognitive load, System 2 demands, form complexity, ambiguous navigation) and Motivation (social proof, endowed progress, reward clarity). The ratio drives prioritization: high friction + low motivation = the biggest opportunities.

3. **Default Analysis** — The skill identifies every pre-selected state, opt-in/opt-out pattern, and default configuration in each flow. Defaults get weighted multipliers in the scoring because they disproportionately drive behavior (60-80% engagement lift per the research).

4. **Peak-End Evaluation** — Each flow is scored on where the "peak" moment falls (delight or pain) and how the flow *ends*. Flows that end on ambiguity, dead ends, or anti-climax get flagged. Recommendations prioritize improving endings over middles.

5. **Loss Aversion Risk Flags** — Any flow that removes, resets, or overrides user-generated progress or customization gets a negative penalty score with an explanation of why users will perceive the loss as ~2x the objective cost.

6. **Dark Pattern Detection (Flag & Verify)** — The skill flags any persuasion mechanism that *could* be a dark pattern — scarcity messaging, pre-checked upsells, confirm-shaming, hidden costs, roach motels — with a neutral description and a prompt for the team to verify intent. Not accusatory; investigative.

7. **Behavioral Impact Matrix & Report Generation** — The final output is a Markdown file containing:
   - **Executive summary** with the Behavioral Impact Matrix table (rows per feature/flow, columns for Friction, Motivation, Default Potential, Peak-End Impact, Dark Pattern Flags, Final Behavioral Priority)
   - **Per-flow deep-dive sections** with screenshots, step-by-step scoring, narrative findings, and specific recommendations
   - **Prioritized action list** sorted by behavioral impact score
   - **WAM diagnostic** where adoption/cooperation issues are identified (classifying into the four quadrants with matched interventions)

## Non-Goals

- **Not a code review tool.** The skill assesses the *experienced product*, not the implementation. It doesn't read source files to infer UX.
- **Not an accessibility audit.** A11y is important but is a different lens with different frameworks. This is purely behavioral economics.
- **Not a performance tool.** Load times affect friction, but the skill isn't measuring Core Web Vitals or backend latency.
- **Not prescriptive about design.** The skill identifies behavioral issues and opportunities. It doesn't generate mockups or redesigns.
- **Does not implement fixes.** The report is the handoff point. Implementation is a separate step (though the Markdown artifact is designed to feed back into Claude for that work).

## Technical Considerations & Suggested Stack

- **Chrome DevTools MCP** — The primary interaction mechanism. The skill uses `navigate_page`, `take_screenshot`, `click`, `fill`, `evaluate_script`, and `wait_for` to walk the app like a user.
- **Skill format** — A Claude Code custom skill (Markdown prompt file) stored in the project's skill directory. No external dependencies or build step.
- **`--quick` flag** — Skips the Phase 0 intake and proceeds directly to top-level nav discovery with defaults. For experienced users who already know what they want to assess.
- **Screenshot capture** — Screenshots at each step serve as evidence in the report and help the skill "see" what a user sees. These get referenced in the Markdown output.
- **Output location** — The skill confirms the output path with the user before writing. Default is `./be-assessment/be-assessment-YYYY-MM-DD.md` in the project root.
- **Behavioral frameworks** — The scoring criteria, WAM matrix, and dark pattern taxonomy are embedded in the skill prompt itself, drawn from the three reference documents in `docs/`.

## Milestones for V1

### Milestone 1: Pre-flight + Discovery Engine

The skill runs the intake questionnaire, accepts a URL, navigates top-level navigation via Chrome DevTools, maps flows against the user's stated goals, and presents them for selection with a scope-confirmation gate for large assessments. Includes the `--quick` flag for experienced users. Also covers the full error handling layer — auth wall detection, script failures, navigation failures, per-tool fallbacks. No scoring yet — just reliable, targeted navigation and cataloging.

### Milestone 2: Scoring Framework

Implement the Friction-Motivation scoring (1-10), Default Analysis, and Peak-End Evaluation against selected flows. Each step in a flow gets scored and the skill produces per-flow findings. Output is rough but functional Markdown.

### Milestone 3: Risk Detection & Report Polish

Add Loss Aversion risk flags and Dark Pattern detection (flag & verify). Build the full Behavioral Impact Matrix. Polish the Markdown report format with executive summary, per-flow sections, and prioritized action list. Add the helpfully-critical tone calibration — never sycophantic, always finding something to improve.

### Milestone 4: WAM Diagnostic & Flow Intelligence

Add the Willingness-Awareness Matrix diagnostic for flows where adoption/cooperation issues surface. Improve the discovery engine's ability to identify meaningful user journeys (not just page links) and handle authenticated flows.

## Open Questions

- **Authentication** — Many apps require login. Should the skill accept credentials as input to test authenticated flows? Or should the user manually log in first and hand off the session? Security implications either way.
- **Dynamic/SPA behavior** — Because discovery is now top-level nav only (not full crawling), SPA routing is less of a problem for discovery. However, within a selected flow, JS-driven navigation that doesn't produce a URL change can still confuse step-tracking. V1 handles this with a logged warning and manual continuation; V2 could detect route changes via `history` API monitoring.
- **Baseline comparisons** — Should the skill support diffing two assessments over time (before/after a redesign)? This would be powerful but adds complexity. Probably a V2 feature.
- **Multiplayer flows** — Some behavioral patterns only surface in multi-user contexts (e.g., social proof, collaborative features). V1 likely stays single-user, but worth flagging.
- **Report length management** — A full-app assessment could produce a very long document. Should there be a summary-only mode alongside the full report?
