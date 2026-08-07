# opulent-targeting

**Opulent-Targeting** — point it at a company, walk away, come back to a campaign sitting at the send gate.

It runs all ten phases unattended. No interview, no clarifying questions, no setup modals, no "what would you like me to do next". A company name is a complete input.

It is three existing skills fused into a single sequential contract, with nothing dropped and nothing invented:

| Source | What it contributed |
|---|---|
| [`customer-research`](https://github.com/vercel-labs/marketing-team-eve-template/blob/6ebc3d9164a07f31a9a6c34dd56b3d8f145a7349/agent/subagents/product-marketer/skills/customer-research/SKILL.md) (vercel-labs / marketing-team-eve-template) | Interviewing the user, mining the language customers already wrote, telling a real finding from a polite one, and what to hand back. Its question sets are `references/interview-questions.md`. |
| [`last30days`](https://github.com/mvanhorn/last30days-skill) (mvanhorn) | The 30-day multi-source evidence engine and its whole operating contract: the eleven output LAWs, setup, pre-flight resolution, the query plan you write yourself, the discovery protocol, synthesis templates, and the self-checks. |
| [`email`](https://github.com/vercel-labs/marketing-team-eve-template/tree/6ebc3d9164a07f31a9a6c34dd56b3d8f145a7349/agent/subagents/email) subagent (vercel-labs / marketing-team-eve-template) | Adapting copy into an email, house voice and format specs, building in Resend, deliverability and the law, and the approval gate at the send. |

## Why they combine

All three are the same discipline applied at different stages: **report what you verified, name what you could not check, and never let the first become the second.**

- `customer-research` says two independent sources unprompted is a pattern and one is an anecdote worth quoting.
- `last30days` says the same thing mechanically, tagging clusters `single-source` and `thin-evidence` and marking a source `no-results` (it was quiet) apart from `partial` / `timeout` / `auth-failed` (you don't know whether it was quiet).
- `deliverability` says a clean domain record is not evidence that mail reached an inbox.

The seam is that the language a customer used in Phase 2, the cluster that survived Phase 4, and the segment picked in Phase 7 are the same evidence carried forward, and the open questions from Phase 1 are still visible in the handback at Phase 9.

## The ten phases

| Phase | What it settles |
|---|---|
| 0. The contract | Voice, the eleven LAWs, and which contract governs which surface |
| 1. Resolve the target | Who receives this and what you want them to do, from public evidence |
| 2. Read what customers wrote | The language, verbatim, with sources |
| 3. Run the evidence engine | 30 days of what people actually said, across every platform |
| 4. Judge what came back | Finding, single data point, or open question |
| 5. Hand back the findings | The research deliverable, or the input to Phase 6 |
| 6. Make it work as email | Structure, scope, voice, subject and preview |
| 7. Build it and target it | Broadcast, segment, from address |
| 8. Deliverability, law, the gate | What lands, what is lawful, what needs approval |
| 9. Hand back the campaign | Link, state, what a human should check |

Phases 1 and 2 overlap freely. Phase 3 depends on Phase 1 having resolved the entity, because the engine's targeting flags are only as good as the resolution behind them. **Phases 6 to 9 fire only when the brief names an email deliverable** — when the ask was research, the run ends at Phase 5 and hands back the brief.

## Files

| File | What it is |
|---|---|
| `SKILL.md` | The spine. Contract and LAWs at the top, then the ten phases in order. Everything a phase needs that is short enough to inline is inline. |
| `references/evidence-engine.md` | The autonomous-run override table, then the full `last30days` operating contract for Phase 3: stale-clone check, fast paths, setup wizard, runtime preflight, intent parsing, query-quality pre-flight, pre-flight resolution, agent / comparison / competitor / hiring-signals modes, pre-research intelligence, query planning, invocation, web supplements, the saved-file appendix, and security and permissions. |
| `references/synthesis-and-handback.md` | The judge-agent contract for Phases 4 and 5: cluster-first reading, audience registers, source weighting, per-query-type templates, citation priority, the pre-present self-check, and the follow-up handlers for if the user comes back. |
| `references/save-html-brief.md` | The shareable HTML brief flow, including opt-in hosted publishing. |
| `references/interview-questions.md` | Phase 1's question bank, grouped by what you're trying to learn. |
| `references/email-patterns.md` | Phase 6 worked shapes per email type, plus the evidence on personalization and segmentation. |
| `references/email-best-practices.md` | Why the Phase 6 rules are what they are, with sources, plus the recurring failure modes. |
| `references/email-format-specs.md` | Sourced truncation and rendering limits, each figure dated to the page it came from. |
| `references/banned-words.json` | The `lint_against_style` copy-quality list. Run the tool rather than reading the file. |
| `references/resend-build.md` | Phase 7 build order and the quiet failures. |
| `references/deliverability-checklist.md` | Phase 8 checks in priority order, each marked with the tool that verifies it or `none`, plus attributed thresholds and the legal requirements. |

## Install

This repo carries the skill: the contract, the phases, and the references. It does **not** vendor the research engine.

Phase 3 shells out to `scripts/last30days.py`, which lives in [mvanhorn/last30days-skill](https://github.com/mvanhorn/last30days-skill) and is MIT licensed. Install that skill first, then drop this one alongside it:

```bash
# 1. the engine (provides scripts/last30days.py)
npx skills add mvanhorn/last30days-skill

# 2. this skill
git clone https://github.com/Git-Godssoldier/opulent-targeting.git \
  ~/.claude/skills/opulent-targeting
```

`SKILL_DIR` resolution in `references/evidence-engine.md` expects `scripts/last30days.py` to be a direct child of the directory holding the `SKILL.md` it loaded, so if you keep the two skills separate, point the engine invocations at the last30days install directory rather than this one. Everything else in the workflow — Phases 1, 2, and 4 through 9 — runs without it.

The Resend tooling in Phases 7 and 8 is discovered at runtime through `connection_search`; there is no vendored client here either.

## Runtime

- **Tools**: `Bash`, `Read`, `Write`, `AskUserQuestion`, `WebSearch`, plus `get_brand_context`, `read_artifact`, `save_artifact`, `lint_against_style`, and Resend's tools via `connection_search`.
- **Engine**: `scripts/last30days.py` and Python 3.12+. The runtime preflight in `references/evidence-engine.md` resolves the interpreter and falls back to a `uv`-managed CPython 3.12 where one exists.
- **Credentials**: none required, and an unattended run never asks for one. Reddit, Hacker News, Polymarket, GitHub, and web work with no setup. X, YouTube, TikTok, Instagram and the rest activate from credentials already on disk; absent those, the lane is thinner and the handback says so.
- **Writes**: research briefings save to `LAST30DAYS_MEMORY_DIR` (default `~/Documents/Last30Days`). Nothing publishes, sends, or leaves the machine without an explicit approval.

## How it runs unattended

Every decision the three source skills would have put to a human now has a resolution path, and `references/evidence-engine.md` opens with the point-by-point table. The order of preference never changes:

1. **Resolve it from evidence.** Phase 1's resolution map takes each question the interview would have asked and names the lookup that answers it: positioning from the first-party site, the real alternative from competitor discovery plus what customers compare it to unprompted, the objections from the complaint clusters that recur, the segment from who is actually posting, the direction from the company's own ATS board.
2. **Take the documented default.** Almost every "ask the user" in the sources sits next to its own fallback — the keyword-trap reframes, the setup wizard's skip branch, "record it as an open question."
3. **Carry it forward as a named open question.** Never a guess. An open question in the handback costs ten seconds; a blocked run costs the run.

For a company target that means one invocation carrying `--plan`, `--x-handle`, `--x-related`, `--subreddits`, `--dedicated-subreddits`, `--github-repo`, `--trustpilot-domain` and the TikTok/Instagram flags, plus two scoped passes — `--competitors` for the real alternative and `--hiring-signals` for where they're leaning. All optional upstream; all mandatory here, because each one is a row in the resolution map.

## Gates

Autonomy is about questions, not consequences. Three things still stop:

1. **The send.** Sending a broadcast, an email, or a batch pauses for approval, and so do deletes and changes to a contact's topic subscriptions. Mail cannot be recalled. Say what the approval covers — segment, contact count, timing — because that sentence is what the user is agreeing to. Denied means stop and ask what to change, not retry. Everything before this point is preparation, and `create-broadcast` does not send, so preparation runs unattended all the way to the edge.
2. **Publishing.** The local HTML brief always saves first and its path is always shown. Hosted publishing happens only after the user picks it, and public pages are named as public before the choice.
3. **Accounts and private credentials.** The ScrapeCreators GitHub signup creates an account, so an unattended run skips it and proceeds on the free keyless sources. Browser-cookie extraction for X runs only when `BROWSER_CONSENT=true` is already on disk from a prior consented session; otherwise the wizard's `FROM_BROWSER=off` branch installs every free CLI without touching a cookie. A thinner X lane is a coverage note, not a prompt.

The handback names every decision the run made on a human's behalf — derived recipient and action, placeholder segments, query reframes, thin source lanes, and the consent and jurisdiction questions only the user can answer — in one short list directly above the approval. One decision to make, with everything needed to make it in view.
