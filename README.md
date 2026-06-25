# Deeply_Retest — Senior QA Retest Skill

A Claude Code / Codex **skill** for running a **targeted, evidence-driven bug retest** (not a full regression cycle). It verifies a fix the right way — UI *and* persisted data must agree — runs a risk-scaled deep sanity pass around the same screen/module, checks the related dependency chain, and posts one concise status-only comment on the issue.

Built for Sara, WE WILL's QA agent.

## Why this skill

Most "retests" stop at the happy path or trust the API response. This one is built around the failure modes that produce **false PASSes**:

- **Backend-fixed != user-fixed** — a correct API payload with a wrong UI value is *Not Fixed / Partially Fixed*, never a PASS.
- **State changes the test** — confirm the screen/entity is in the right state (awaiting confirmation, activation, permission...) before declaring anything broken.
- **Prove the negative** — a "still broken" verdict needs the same evidence rigor as a PASS.
- **Walk reflection cross-surface** — for Admin->Client (config->reflection) bugs, verify *both* surfaces with the same data, not just the config side.
- **Deep sanity, not a glance** — risk-scaled scenarios covering main actions, related buttons/filters/forms/tabs/navigation, UI reflection after save, validation, persistence after refresh, gated role/states, and linked flows.

## Two modes (you pick at startup)

When invoked, the skill first asks which mode to run:

- **A) Quick Retest** — retest the reported bug only, update its GitHub status, and add a short status comment.
- **B) Deep Retest** — the full five-stage, evidence-driven flow.

After the mode is chosen and **before retesting**, the skill clears **cache + cookies** and opens a **fresh browser session**, so the retest reflects the real current build (not a cached page or stale login).

In **Deep Retest**, the status comment + GitHub status update happen **once, right after the original-bug retest (Stage 1)**; Stages 2–4 then run without posting their results to the ticket.

**Status update from the retest result:**
- **Fixed** -> status **Done**
- **Not Fixed** -> status **TODO** + add the **`Reopened`** label

(If the token lacks project write scope, the skill posts the comment and asks you to apply the status/label change.)

## The five stages

| Stage | What it does |
|------|--------------|
| **0 — Preconditions & Readiness Gate** | Build reachable, screen loads, role/creds, test data, subject still exists. BLOCKED early beats fabricated output. Classifies the bug role. |
| **1 — Original Bug Retest** | Reproduce exactly. Verify **UI + persisted data**. Pass / Fail / Not Reproducible. |
| **2 — Deep Focused Screen/Module Sanity** | Risk-scaled (typically 3-10) focused scenarios around the fix; replacement scenarios generated from the bug + related map bugs. |
| **3 — Mapped/Related Bugs Coverage** | One decisive check per related bug (cap 6); cross-surface and triad/workflow checks; chain-coverage verdict. **If a related-bug check looks blocked, it asks you first before marking it blocked.** |
| **4 — Final QA Decision + Retest Comment** | One final status + a short, professional, status-only ticket comment with a screenshot. |

Final statuses: `PASS` / `PASS WITH OBSERVATIONS` / `FAIL` / `FAIL - SIDE EFFECT` / `FAIL - DEPENDENCY` / `NOT REPRODUCIBLE` / `BLOCKED`.

## Install

**Claude Code:**
```bash
git clone https://github.com/Mai-Ziada/Deeply_Retest_Skill.git
cp -r Deeply_Retest_Skill/Deeply_Retest ~/.claude/skills/
```

**Codex:**
```bash
cp -r Deeply_Retest_Skill/Deeply_Retest ~/.codex/skills/
```

Then invoke it with `/Deeply_Retest` and provide a Bug ID (or the INPUT block from `SKILL.md`).

## Notes

- Tracker details in `SKILL.md` (e.g. the GitHub Project number) are project-specific — adjust them to your own environment.
- The skill posts a short status comment **and** updates the bug's GitHub status from the retest result (Fixed -> Done; Not Fixed -> TODO + `Reopened` label). If the token lacks project write scope, it posts the comment and asks you to apply the status/label change.
- During mapped/related-bug checks, if something appears blocked it **asks you before** recording it as blocked.

## License

MIT — see [LICENSE](LICENSE).
