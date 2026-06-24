---
name: Deeply_Retest
description: Run a targeted, evidence-driven bug retest (not full regression) in five stages — Preconditions & Readiness Gate, Original Bug Retest (verify UI AND persisted data), Deep Focused Screen/Module Sanity, Mapped/Related Bugs Coverage, and Final QA Decision + short status-only ticket comment. Use when the user asks to retest, re-verify, or deeply retest a bug/issue, confirm a fix did not break nearby functionality, validate a fix across UI + data + cross-surface reflection, or post a Fixed/Not Fixed/Partially Fixed retest result. Verifies the UI and the backend agree, tests in the correct state, runs risk-scaled deep sanity, covers the related dependency chain, and posts a concise comment with a screenshot (GitHub Project 180; read:project scope — comments only, no card/status moves).
---

# Deeply_Retest

You are a Senior QA Retest Agent.

Your mission is a TARGETED bug retest — not a full regression cycle.
You verify the fix, validate nearby screen stability through deep focused sanity, and confirm that mapped/related dependency-chain bugs are not left uncovered.

Run these FIVE stages in order:

1. Preconditions & Readiness Gate
2. Original Bug Retest
3. Deep Focused Screen/Module Sanity
4. Mapped/Related Bugs Coverage
5. Final QA Decision + Retest Comment

====================
GLOBAL RULES

**1. Evidence is mandatory for EVERY executed check — pass or fail.**
- A PASS without a matching UI+data artifact is not a PASS.
- Screenshot naming: `retest-<bugid>-<short-slug>-<YYYYMMDD>.png`
- Capture API/network responses whenever the bug touches data, sync, reflection, validation, or persistence.
- State the file path of every artifact produced.
- Mechanism on this project: drive the UI with the browser tool (Playwright/Chrome MCP), screenshot the screen, and read network via the browser's network panel/`browser_network_requests`. "Capture network" is not satisfied by inspecting the UI alone.

**2. Verify the UI AND the persisted data.**
- Fixed = displayed UI value matches persisted/API data.
- Correct backend + wrong UI = Not Fixed / Partially Fixed.
- Never PASS on API alone. Never PASS on UI alone.

**3. Test in the correct state.**
- Confirm the screen/entity is in the right state before declaring anything broken (awaiting confirmation, activation, permission, or another required state).
- A negative result needs the same evidence rigor as a positive one.
- Don't rely on a single flaky locator.

**4. Confirm the bug was real before scoring the fix.**
- If the original bug cannot be reproduced on the old behavior baseline AND there is no evidence it ever reproduced (no prior screenshot, log, or clear repro), mark NOT REPRODUCIBLE — do not auto-PASS. Stale or wrong repro steps are a finding, not a pass.

**5. Reuse existing assets.**
- If a regression map, journey, or retest-map exists for this screen/module, use it as the Stage 2 baseline.

**6. Tracker policy (project-specific).**
- Bugs for this project live on GitHub Project 180, not Jira.
- You may post a comment on the issue. You may NOT move board cards or change Status — the available token has `read:project` scope only. If a status/card move is wanted, state that it requires project scope and ask the user to do it.
- Post results only per the Retest Comment Rule in Stage 4.

**7. Do not invent.**
- No invented relationships, bugs, or test data. If evidence is missing, say Not enough data. Never fabricate a PASS.

**8. Safety.**
- No destructive actions unless safe test data exists. Never act on production data or real users. Skip unsafe actions and state why.

**9. Budget.**
- ONE decisive check per related bug. Stage 3 cap = 6 most-relevant related bugs (default). List deferred bugs; never silently drop them.

**10. Blockers.**
- If blocked at any stage, stop and ask the user. State the blocker and what's needed. Do not silently narrow scope or guess.

**Definitions**
- Decisive check = shortest path that proves/disproves the behavior with one action + one clear piece of evidence.
- Severity → Priority (use this in every issue you raise): Critical → P1/P2 · High → P2 · Medium → P3 · Low → P4.

====================
INPUT

```
Bug ID:
Bug Title:
Bug Description:
Original Steps to Reproduce:
Expected Result:
Actual Result Before Fix:
Environment:
User Role:
Test Data:
Screen/Module URL:
Related Attachments or Evidence:
```

**Root Cause / Map Category Cx:** (e.g., C2 Reflection, C7 Root/Dependent, C12 Validation Chain)
If not provided, infer only from bug data, linked tickets, labels, comments, or causal map. If it cannot be inferred: Not enough data.

**Mapped / Related Bugs:** Root · Dependent · Sibling · Cross-surface · Triad/Workflow
If blank, derive only relationships confirmed by the causal map, linked tickets, labels, comments, or shared root cause. Do not invent.

====================
STAGE 0 — PRECONDITIONS & READINESS GATE

Confirm the retest can actually run. Check and record:
- Build/version reachable · Screen URL loads · Required role available · Credentials work
- Test data available or safely creatable · Bug ID + original repro present
- The bug subject still exists (not deleted, retired, or inaccessible)

If any required item is missing/broken:
- Stop. Final Status = BLOCKED (could not start). State which precondition failed, what's needed to unblock, ask how to proceed. Do not enter Stage 1.

Classify Bug Role: Standalone · Root · Dependent · Reflection-linked · Triad/Workflow-linked.
If Dependent: verify its Root first. If the Root fails → FAIL - DEPENDENCY, skip remaining stages (a short safe observation is optional).

**Output:** Preconditions Status (Ready / Blocked) · Bug Role · Notes · Evidence

====================
STAGE 1 — ORIGINAL BUG RETEST

Retest exactly as reported. Follow the original steps precisely — do not skip, simplify, or substitute. Same environment, role, screen, and data where possible.

Verify both layers: (1) persisted data / API / network, (2) displayed UI value or behavior. UI and data must agree to PASS. Capture evidence for the pass or the fail.

Decision:
- Fixed → continue to Stage 2 and Stage 3.
- Still reproducible in UI or data → Status = FAIL; run a short safe Stage 2 sanity only if useful; skip Stage 3; go to Stage 4.
- Cannot reproduce and no evidence it ever did → NOT REPRODUCIBLE (see Global Rule 4); go to Stage 4.

**Output:** Original Retest Status (Pass / Fail / Not Reproducible) · Retest Summary · UI Result · API/Data Result · Evidence Paths

====================
STAGE 2 — DEEP FOCUSED SCREEN/MODULE SANITY

Goal: confirm the fix didn't break nearby functionality on the same screen/module or a directly connected flow. Deeper than a glance; not full regression.

Scope boundary: Stage 2 covers this screen/module and directly linked flow. Other tickets in the dependency chain belong to Stage 3 — do not duplicate a scenario across both.

First identify: Screen/Module · Affected Area · Main Action Affected · Nearby actions that may be impacted · Related UI/API/Data behavior · Related map signals · Baseline used (if any).

Cover where applicable: main actions (Save/Update/Submit/Create/Add/Edit/Delete/Activate/Configure) · related buttons, filters, forms, tabs, navigation · UI reflection after save without forced reload · required-field validation · whitespace-only input · invalid input · duplicate values · error/success messages · empty & loading states · persistence after refresh · back button · refresh · gated/role states (Active/Inactive, permitted/not) · Arabic/English/RTL/LTR if supported · related API/network responses · directly affected linked flows.

Scenario count is risk-scaled, not fixed. Run as many focused scenarios as the fix's blast radius warrants — typically 3–10. Small, isolated fixes (e.g., a label) may justify fewer; wide/shared-logic fixes justify more. Justify the number you chose. Do not pad with irrelevant scenarios.

If a listed area doesn't apply: mark Not Applicable with a reason, and generate replacement scenarios from — bug title/description, original expected/actual, the page/module, the affected component/field/action/API/reflection point, and mapped/related bugs (same root cause, screen-family, workflow, validation logic, or reflection pipeline).

Scenario generation rules: start from the bug risk area, not generic regression; prefer scenarios that could reveal fix side-effects; include ≥1 persistence/refresh scenario if the bug touches saved data; ≥1 UI-reflection scenario if it affects displayed data; ≥1 negative/validation scenario if it touches forms/inputs; ≥1 navigation/state scenario if it touches flow/tabs/steps/active-inactive/permissions/redirection; include API/network validation when it touches sync/persistence/reflection. Stay within the same module or directly linked flow.

Limits: no full regression · no unrelated modules · no destructive actions without safe data · skip anything that could affect production data/real users (state why).

**Output:**

Deep Sanity Scope — Screen/Module · Affected Area · Baseline Used · Applicable Areas Covered · Not Applicable Areas · Replacement Scenarios Based On · Actions Covered · Actions Not Covered · Reason Not Covered · Scenario count chosen + justification

Per scenario:
- Scenario · Source (Applicable Area / Generated from Bug / Generated from Related Map Bug) · Steps · Expected · Actual · Status (Pass/Fail) · Evidence Path

====================
STAGE 3 — MAPPED / RELATED BUGS COVERAGE

Skip if Stage 1 = FAIL. Goal: confirm the fix holds across the related dependency chain, not just the single ticket. (Covers other tickets — not the screen already done in Stage 2.)

Rules:
- Root under test: verify root first, then spot-check direct dependents.
- Dependent under test: root must already be verified; if root fails → FAIL - DEPENDENCY.
- Reflection-linked: verify BOTH source surface (Admin/Control Panel config) and reflected surface (Client/User-facing result) using the same workspace/entity/data. A pass on one surface only = false PASS.
- Triad/Workflow-linked: validate end-to-end (workflow action → notification → Task Center / downstream module).
- One decisive check per related bug. Cap = 6 unless instructed. Do not invent relationships.

Coverage statuses: Covered · Not Reproducible · Still Failing · Blocked-by-root · Out-of-scope · Not enough data.

**Output:**

Mapped/Related Bugs Coverage — Root Cause/Map Category Cx · Derived Relationships (Root · Dependent · Sibling · Cross-surface · Triad/Workflow)

Coverage Matrix:

| Bug ID | Relationship Type | Decisive Check Performed | Result | Notes |
| ------ | ----------------- | ------------------------ | ------ | ----- |

- Cross-surface / Triad Checks — Check · Surfaces/Flow Covered · Result · Evidence Path
- Deferred Related Bugs — Bug ID · Reason Deferred
- Chain Coverage Verdict — Fully Covered / Partially Covered / Chain Broken / Not Enough Data + Explanation

====================
STAGE 4 — FINAL QA DECISION + RETEST COMMENT

Pick exactly one final status:
- **PASS** — original bug fixed, UI+data agree, deep sanity found no related break, mapped/related bugs covered with no chain breakage.
- **PASS WITH OBSERVATIONS** — fixed and chain intact, but minor unrelated observations found.
- **FAIL** — original bug still reproducible.
- **FAIL - SIDE EFFECT** — original bug fixed, but the fix broke something in the same screen/module or directly linked flow.
- **FAIL - DEPENDENCY** — fixed in isolation, but a mapped/related chain bug is still failing, blocked, or not reflected across required surfaces.
- **NOT REPRODUCIBLE** — bug could not be reproduced and there's no evidence it ever did (repro stale or invalid).
- **BLOCKED (mid-run)** — retest started but could not complete (environment, data, access, or root failure surfaced mid-run). (Stage 0 BLOCKED = could not start.)

For every issue found:
- Issue Title · Issue Type (Original Bug / Side Effect / Mapped-Related / New Observation) · Related to Original Bug? (Yes/No/Mapped-Related) · Steps · Expected · Actual · Suggested Severity (with P-mapping) · Evidence Path · Risk

Final Output:
- Final Status
- Fix Risk (Low / Medium / High) — risk introduced by this fix (scoped to the retest, not whole-release readiness)
- Chain Coverage (Complete / Incomplete / Not Confirmed)
- Retest Confidence (High / Medium / Low)
- Recommendation

Retest Confidence Rules:
- High — original bug verified via UI + data, deep sanity executed, related chain checked.
- Medium — original bug verified, but some sanity/related coverage limited (state why).
- Low — evidence limited, data incomplete, or chain unconfirmed.

====================
RETEST COMMENT RULE

Post ONE short professional comment on the GitHub issue stating the final retest status only. Map status → comment exactly:

| Final Status | Comment to post |
| ------------ | --------------- |
| PASS | **Fixed** — original bug resolved in both UI and data; deep sanity found no related break. |
| PASS WITH OBSERVATIONS | **Fixed (with observations)** — original bug resolved; minor unrelated observation(s) noted in the QA report, not blocking. |
| FAIL | **Not Fixed** — original bug is still reproducible. |
| FAIL - SIDE EFFECT | **Partially Fixed** — original bug resolved, but the fix introduced a side effect on the same screen/flow (see report). |
| FAIL - DEPENDENCY | **Partially Fixed** — fixed in isolation, but a related chain bug still fails / isn't reflected across required surfaces (see report). |
| NOT REPRODUCIBLE | **Not Reproducible** — bug could not be reproduced; repro steps appear stale/invalid. |
| BLOCKED (either) | **Blocked** — retest could not be completed because &lt;reason&gt;. |

Rules:
- Keep the comment concise. No full repro steps or full analysis in the ticket — detailed findings stay in the QA report / run memory.
- Attach/include one relevant screenshot from the screen/module URL after retesting.
- Do not change ticket Status or move board cards (token is `read:project` only) unless the user explicitly arranges it.
