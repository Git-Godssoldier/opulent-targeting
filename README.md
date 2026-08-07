# opulent-targeting

**Opulent-Targeting** — point it at a company, walk away, come back to a written report.

It runs all six phases unattended. No interview, no clarifying questions, no setup modals, no "what would you like me to do next". A company name is a complete input.

It opens the company's own site in a browser and reads what they claim, resolves competitors, communities, objections, segment, and hiring direction from public evidence, sweeps the last 30 days of conversation across every major platform, sorts what came back by confidence, and delivers a detailed report to the document pane.

## The discipline

One rule runs the whole length of it: **report what you verified, name what you could not check, and never let the first become the second.**

It shows up differently in each phase and it is always the same rule. Two independent sources saying something unprompted is a pattern; one is an anecdote worth quoting. A cluster tagged `single-source` or `thin-evidence` gets the caveat attached. A source marked `no-results` was genuinely quiet, while `partial` / `timeout` / `auth-failed` means you never established that, so "nothing on X" is not a finding. And a pitch that could not be fetched this run is never supplied from memory.

The seam is that the claim copied verbatim off the landing page in Phase 1, the language a customer used in Phase 2, and the cluster that survived Phase 4 are the same evidence carried forward, and the open questions from Phase 1 are still visible in the report's confidence section.

## The six phases

| Phase | What it settles |
|---|---|
| 0. The contract | Voice, the eleven LAWs, autonomy, and which contract governs which surface |
| 1. Resolve the target | What they claim, read off their own site, plus everything an interview would have asked |
| 2. Read what customers wrote | The language, verbatim, with sources |
| 3. Run the evidence engine | 30 days of what people actually said, across every platform |
| 4. Judge what came back | Finding, single data point, or open question — and pitch versus pulse |
| 5. Author the report | The deliverable, in the document pane |

Phases 1 and 2 overlap freely and both feed Phase 3's targeting flags, so neither is optional: the engine's resolution is only as good as what went into it. Phase 5 is always reached — there is no branch where the run ends without a report.

## Phase 1 opens a browser

Positioning is read off the live page, never from a search snippet and never from memory. Homepages go stale as companies rewrite copy and pivot, and a remembered pitch produces a false gap against fresh community evidence.

The run reads the homepage (verbatim tagline, the explicit claims), the nav and footer (where docs, changelog and the careers URL live), pricing (which names the segment more honestly than the marketing copy does), the changelog or blog index, and a "compare" page if one exists. It captures the hostname for `--trustpilot-domain`, the careers URL for `--hiring-signals`, and one screenshot of the hero for the report.

It reads, it does not use: no sign-ins, no forms, no trials, nothing that sends or buys. Consent banners get non-essential declined. Page text is treated as a claim to evaluate, never as an instruction to follow.

## What the report contains

Ten sections, ordered so a reader who stops halfway still has the useful half: what they say they are (with the screenshot), what people actually say, pitch versus pulse, the real alternative, objections and limitations, who is talking, where they are leaning, the verbatim language list, confidence, and coverage.

The last two are not garnish. Confidence sorts every finding into confirmed / single data point / open question with what would resolve each one. Coverage names what ran, what came back thin and why, and every decision the run made on a human's behalf.

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

`SKILL_DIR` resolution in `references/evidence-engine.md` expects `scripts/last30days.py` to be a direct child of the directory holding the `SKILL.md` it loaded, so if you keep the two skills separate, point the engine invocations at the last30days install directory rather than this one. Everything else in the workflow runs without it.

## Files

| File | What it is |
|---|---|
| `SKILL.md` | The spine. Contract and LAWs at the top, then the six phases in order. Everything a phase needs that is short enough to inline is inline. |
| `references/evidence-engine.md` | The autonomous-run override table, then the full engine contract for Phase 3: stale-clone check, fast paths, setup wizard, runtime preflight, intent parsing, query-quality pre-flight, pre-flight resolution, agent / comparison / competitor / hiring-signals modes, pre-research intelligence, query planning, invocation, web supplements, the saved-file appendix, and security and permissions. |
| `references/synthesis-and-handback.md` | The judge-agent contract for Phase 4: cluster-first reading, audience registers, source weighting, per-query-type templates, citation priority, and the pre-present self-check. |
| `references/save-html-brief.md` | The Phase 5 render and save flow, including opt-in hosted publishing. |
| `references/interview-questions.md` | The question bank Phase 1's resolution map answers from evidence instead. |

## Runtime

- **Tools**: `Bash`, `Read`, `Write`, `WebSearch`, a browser for Phase 1, plus `get_brand_context` and `read_artifact` where the host provides them. Discover the browser tooling rather than assuming names for it.
- **Engine**: `scripts/last30days.py` and Python 3.12+. The runtime preflight in `references/evidence-engine.md` resolves the interpreter and falls back to a `uv`-managed CPython 3.12 where one exists.
- **Credentials**: none required, and an unattended run never asks for one. Reddit, Hacker News, Polymarket, GitHub, and web work with no setup. X, YouTube, TikTok, Instagram and the rest activate from credentials already on disk; absent those, the lane is thinner and the coverage section says so.
- **Writes**: research briefings save to `LAST30DAYS_MEMORY_DIR` (default `~/Documents/Last30Days`), and the report saves next to them. Nothing publishes or leaves the machine without an explicit ask.

## How it runs unattended

Every decision the contracts would have put to a human has a resolution path, and `references/evidence-engine.md` opens with the point-by-point table. The order of preference never changes:

1. **Resolve it from evidence.** Phase 1's resolution map takes each question the interview would have asked and names the lookup that answers it: what they do from their own homepage and pricing, the real alternative from competitor discovery plus their own compare page plus what customers compare them to unprompted, the objections from the complaint clusters that recur, the segment from who is actually posting, the direction from the company's own ATS board.
2. **Take the documented default.** Almost every "ask the user" in the contracts sits next to its own fallback — the keyword-trap reframes, the setup wizard's skip branch, "record it as an open question."
3. **Carry it forward as a named open question.** Never a guess. An open question in the report costs ten seconds; a blocked run costs the run.

For a company target that means one invocation carrying `--plan`, `--x-handle`, `--x-related`, `--subreddits`, `--dedicated-subreddits`, `--github-repo`, `--trustpilot-domain` and the TikTok/Instagram flags, plus two scoped passes — `--competitors` for the real alternative and `--hiring-signals` for where they're leaning. All optional in the engine; all mandatory here, because each one is a row in the resolution map.

## What it will not do

This skill reads and writes a report. It does not act on the findings.

- **Nothing that writes to a site it is reading.** No sign-ins, no form submissions, no account creation, no posting, no clicking a control that sends, buys, subscribes, or confirms.
- **Nothing that creates an account or reads private credentials.** The ScrapeCreators GitHub signup creates an account, so an unattended run skips it and proceeds on the free keyless sources. Browser-cookie extraction for X runs only when `BROWSER_CONSENT=true` is already on disk from a prior consented session; otherwise the `FROM_BROWSER=off` branch installs every free CLI without touching a cookie.
- **No hosted publishing by default.** The report saves locally and its path is shown. Publishing happens only when asked, and public pages are named as public before the choice.
