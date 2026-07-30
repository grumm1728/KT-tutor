# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Status

Greenfield. As of 2026-07-30 the repository contains only this file and a LICENSE — no source, no build tooling. Remote: https://github.com/grumm1728/KT-tutor

Nothing in the Architecture or Stack sections describes existing code; they describe intent. The Source Material and Data sections *are* verified fact — they were read off the paper and the released dataset. Delete this Status section once the repo has a working build.

## What this project is

A **demonstration** of an LLM-powered knowledge-tracing tutor. It is a visualization/teaching artifact, not a production tutoring system or a reimplementation of the paper's training pipeline. Two panes, side by side on one page:

- **Left — the student's view.** A tutor dialogue rendered inside a phone outline. This is what a learner would see.
- **Right — the model's view.** A live visualization of the knowledge-tracing state: per-KC mastery estimates and how each student turn updates them.

The user steps a dialogue forward and watches the belief state on the right move in response to the turn on the left. **The coupling between the two panes is the whole point.** A change that lets the phone advance without the KT panel visibly reflecting *why* has broken the demo, even if both panes still render.

## Source material

Scarlatos, Baker & Lan (2025), *Exploring Knowledge Tracing in Tutor-Student Dialogues using LLMs*, LAK 2025. arXiv:2409.16490. Reference implementation: https://github.com/umass-ml4ed/dialogue-kt (MIT).

### The dialogueKT formulation

Use the paper's vocabulary throughout — in code, in UI copy, in variable names.

- A **dialogue** is an alternating sequence `(s₀, t₁, s₁, …, t_M, s_M)` of tutor turns `t` and student turns `s`. `s₀` is present when the student opens.
- A **turn pair** `j` is one tutor turn plus the student turn responding to it. **This is the KT time step** — much more granular than the per-problem step in classic KT.
- **Correctness** `y_j ∈ {0,1}` labels whether `s_j` correctly answers `t_j`. It is `na` when correctness is not well-defined (tutor is off-topic, giving encouragement, etc.).
- **KCs** `C_j` are the knowledge components the tutor turn requires, drawn from Common Core math standards via the Achieve the Core (ATC) coherence map. `C_j = ∅` for irrelevant turns. A KC is identified by its **full standard text**, not a code.
- **Mastery** `z_jk ∈ {0,1}` is the latent binary mastery of KC `k` at turn `j`; `ẑ_jk = P(z_jk = 1)` is the model's estimate. This is the number the right-hand pane visualizes.
- **Correctness prediction is compensatory, not conjunctive**: `ŷ_j = (1/K) Σ_k ẑ_jk` — the *mean* of per-KC masteries, not the product. The paper tested both and averaging won. Do not "fix" this into a product.

Models in the paper: **LLMKT** (best; LoRA fine-tune of Llama-3.1-8B-Instruct, mastery read off the softmax over `True`/`False` token logits), **DKT-Sem** (S-BERT turn embeddings into an LSTM), and baselines (DKT, DKVMN, AKT, SAINT, simpleKT, BKT).

### Be honest about performance

Best result is LLMKT at **65.8% AUC on CoMTA, 76.7% on MathDial** — versus >80% for standard KT on conventional datasets. The paper is explicit that dialogueKT is hard and unsolved: dialogues are short, student behavior is unpredictable, and KCs often appear only once. **The demo must not present these estimates as reliable per-student judgments.** Framing it as "here is what a KT model believes and how it changes" is honest; "here is what this student knows" is not.

### Qualitative findings worth surfacing in the demo

These are the paper's most demo-able results:

- LLMKT's edge comes from reading the **text** of turns, not just correctness labels — it infers that a follow-up question is harder than the first even when both carry the same KC labels. A demo that only animates on correct/incorrect misses the paper's actual finding.
- Mastery is predicted **low when a KC first appears** mid-dialogue.
- Learning curves are mixed: of the 15 most frequent KCs, 5 trend up, 5 down, 5 flat. Upward trends need longer dialogues. Don't build a UI that assumes mastery climbs.
- **Table 3 (p. 255) is a ready-made demo script** — a coordinate-geometry dialogue where mastery drops from ~0.56/0.62 to ~0.32/0.35 after a wrong student turn.

## Data

The reference repo ships pre-annotated data in `data/annotated`; no OpenAI calls or GPU are needed to *use* it. Two datasets, same schema:

| File | Dialogues | Size |
|---|---|---|
| `comta_atc.csv` | 153 | 3.7 MB |
| `mathdial_{train,test}_atc.csv` | 2,823 | 122 MB |

Plus `kc_dict_{comta,mathdial}_atc.json`, mapping KC text → integer id.

**CSV schema** — the columns that matter are `dialogue`, `meta_data`, and `annotation`. All three are **Python literals, not JSON** (single quotes, `True`/`False`/`None`) — parse with `ast.literal_eval`, or convert once to real JSON at ingest. Prefer converting: shipping Python-literal blobs into a JS frontend is a trap.

- `dialogue` — `[{'turn': int, 'teacher': str, 'student': str}, …]`. Math is LaTeX in `\( \)`.
- `annotation` — `{'turn N': {'correct': bool|None, 'kcs': [str, …]}, …}`, keyed from `turn 1`.
- `meta_data` — CoMTA: `{'expected_result': …, 'math_level': …}`.

**Two normalization rules, both load-bearing** (from `kt_data_loading.py:apply_annotations`). Get these wrong and your counts silently disagree with the paper:

1. A turn with no KCs has `correct` forced to `None`; a turn with `correct: None` has its KCs cleared. Turns still `None` after that are **excluded from KT entirely**.
2. The **final** turn's correctness is overridden by human ground truth from `meta_data` — for CoMTA, `expected_result == 'Answer Accepted'`.

Applying rule 1 to `comta_atc.csv` reproduces the paper's Table 1 exactly: 623 labeled turn pairs, 164 unique KCs, 1.94 KCs/turn, 58.75% of turns with >1 KC. (Correctness comes out at 56.0% vs. the paper's 57.78% because rule 2 has not been applied.) **Use this as a regression test on any ingest code you write** — it is a precise, cheap check that the pipeline is faithful.

Dialogues are short: 2–22 turns, mean 5.9.

### Licensing — decided 2026-07-30

The two datasets have incompatible terms, and this determines which one the demo is built on.

**CoMTA** (`COMTA_LICENSE.txt`, © Khan Academy) grants use *internally, solely for evaluating AI models*, by authorized users *within the same entity*. Condition 1 prohibits redistribution, publication, **and model training**; condition 4 makes derivatives inherit all of it.

**MathDial** is CC BY-SA 4.0 — training, deriving, publishing, and public deployment are all fine with attribution, with ShareAlike propagating to derived data.

**Decision: MathDial is the demo's data. CoMTA is local-only and never trained on.**

- Anything that ships — committed fixtures, the deployed page, screenshots in a talk — uses MathDial or hand-authored dialogues. Attribute MathDial; expect ShareAlike on derived trajectories.
- CoMTA stays gitignored and is used only for internal sanity-checking: verifying ingest reproduces Table 1, eyeballing whether trajectories match the paper. That is evaluation, which the license permits.
- **Do not fit any model on CoMTA** — including BKT. Fitting per-KC prior/learn/guess/slip by EM is model training, which condition 1 names explicitly. Fit on MathDial.

Two traps worth stating plainly, because both are easy to rationalize into:

1. **Repo visibility does not decide this.** The repo and the deployed demo are separate publication surfaces. Making the repo private and shipping a public demo containing CoMTA-derived content is the same violation with an extra step. Privacy only ever addresses the redistribution prong — never the training or purpose restrictions.
2. **"Same entity" is narrower than "private."** Sharing CoMTA with a collaborator at another institution is outside the grant even in a private repo.

MathDial is also the better substrate on the merits: 2,823 dialogues vs. 153, and the paper's models perform far better on it (76.7% vs. 65.8% AUC).

If CoMTA specifically is wanted later — its Table 3 dialogue is a good demo script — the clean route is requesting permission from Khan Academy, as the paper's authors did (see their acknowledgments). Do not route around this by paraphrasing CoMTA dialogues into "synthetic" ones; condition 4 covers derivatives.

## Architecture

Intended, not yet built.

- **The KT model is domain logic.** Its own module, a pure `update(state, turn) -> state`, no rendering imports, runnable headlessly over a dataset. The right pane is a view over its output.
- **Every update must be inspectable.** Show prior → evidence → posterior per KC, not just a final number. A black-box update defeats the purpose.
- **Deterministic and replayable.** Same dialogue in, same trajectory out.

**The central constraint: LLMKT cannot run in a browser.** It is a LoRA fine-tune of an 8B model. Any demo claiming to show LLMKT must therefore either (a) replay **precomputed** per-turn mastery values captured offline, or (b) run a lightweight model live and *label it as such*. Note that the reference repo ships **no** precomputed predictions — `results/` is empty, so option (a) requires actually training LLMKT on a GPU first. BKT is the only model here that is honestly implementable client-side; if that is what runs, the UI must say so rather than implying it is the paper's method. Do not let the demo blur this. Note that both routes are constrained by licensing — see below: fit BKT on MathDial, never on CoMTA.

## Stack

Undecided — settle with Scott before scaffolding. The binding constraint is browser-shareability. Replace this section with real install / dev / test / lint commands once chosen, including how to run a single test.

## Agent skills

### Issue tracker

Issues and PRDs live as GitHub issues in `grumm1728/KT-tutor`, managed via the `gh` CLI. See `docs/agents/issue-tracker.md`.

### Triage labels

The five canonical triage roles, using their default label strings. See `docs/agents/triage-labels.md`.

### Domain docs

Single-context — `CONTEXT.md` and `docs/adr/` at the repo root. See `docs/agents/domain.md`.
