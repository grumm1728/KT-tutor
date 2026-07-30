# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Status

Greenfield. As of 2026-07-30 the repository contains documentation only — no source, no build tooling. Remote: https://github.com/grumm1728/KT-tutor

Nothing in the Architecture or Stack sections describes existing code; they describe intent, and the open decisions behind them live on the [wayfinder map](https://github.com/grumm1728/KT-tutor/issues/1). The Source Material and Data sections *are* verified fact — read off the paper and the released dataset. Delete this Status section once the repo has a working build.

## What this project is

A **demonstration** arguing that verbal explanation input, analyzed by an LLM, lets a tutor model **which conception** a student is working from — not merely whether they were right. It is a visualization artifact, not a production tutoring system or a reimplementation of the paper's training pipeline. The artifact is generic and names no company.

Scarlatos et al. (2025) is the **baseline it departs from**, not the thing it reproduces. The paper's own abstract says analyzing misconceptions in dialogue tutoring has been hard, then collapses every student turn to one bit of correctness. This project picks up the thread the paper names and drops.

Two panes, side by side:

- **Left — the student's view.** A tutor dialogue rendered inside a phone outline.
- **Right — the model's view.** The conception state: a distribution over named strategies, plus — when a correctly-stated strategy produced a wrong result — competing explanations of the gap, each tagged **conceptual** or **procedural**.

The user steps the dialogue forward and watches the state on the right move. **The coupling between the two panes is the whole point.** A change that lets the phone advance without the model panel visibly reflecting *why* has broken the demo, even if both panes still render.

Three legs, all in scope: the richer input channel (`8+5 = "13"` vs. `"I took 2 off to fit with 8, then 3 more, so 13"`); conception modeling; and analysis running a beat behind realtime, handing the agent **cards** — probes that disambiguate the live competing hypotheses.

**The spine.** `38 + 25` via split tens and ones: `30+20=50`, then `8+5=13`, then `50+13=63`. The make-ten slip lives in the ones-place step — the student re-adds the 2 they broke off instead of the 3 left over, producing 62. Strategy correct and explicitly stated; execution slipped. A correctness-based KT sees `y=0` and decrements mastery, which is exactly backwards. That single turn carries the whole argument.

### Two framings that carry weight

- **The bottleneck is the output representation, not perception.** LLMKT *does* read student text — that is the paper's headline finding. But its output is `ẑ_jk`, a scalar per KC; the explanation can inform that number and still be unable to survive it. Do not argue the baseline "can't see" the strategy — that argument loses. Steelman it instead: grant full text access and show the scalar still can't carry "strategy intact, execution slipped."
- **Conceptions are knowledge in transition, not bugs** (Smith, diSessa & Roschelle 1994). The right pane must never render a conception as a defect with a red ✗.

### Planning lives on the wayfinder map

Open decisions are tracked as decision tickets on [issue #1](https://github.com/grumm1728/KT-tutor/issues/1), labelled `wayfinder:map`, with native sub-issue and blocking edges. **Check the map before proposing design work** — the stack, the strategy vocabulary, the baseline pipeline, and the precompute format are all still open, and guessing at them duplicates a ticket.

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

**Decision: the demo ships hand-authored content. CoMTA is local-only and never trained on. MathDial is a fallback, not the plan.**

Amended 2026-07-30 after charting the wayfinder map. The demo's argument turns on *verbal strategy talk* — "I took 2 off to fit with 8" — and **no released dataset contains that input type.** MathDial is typed dialogue from GPT-3.5-simulated students and crowdworkers roleplaying on word problems; CoMTA is Khanmigo transcripts. Neither has authentic young-learner strategy explanation. So the demo content is authored from scratch, which also sidesteps both licenses entirely.

- Anything that ships — committed fixtures, the deployed page, screenshots in a talk — is **authored content**. If MathDial is ever used for a shipped fixture, attribute it and expect ShareAlike on derived data.
- CoMTA stays gitignored and is used only for internal sanity-checking: verifying ingest reproduces Table 1, eyeballing whether trajectories match the paper. That is evaluation, which the license permits.
- **Do not fit any model on CoMTA** — including BKT. Fitting per-KC prior/learn/guess/slip by EM is model training, which condition 1 names explicitly. Fit on MathDial or on authored content.

Two traps worth stating plainly, because both are easy to rationalize into:

1. **Repo visibility does not decide this.** The repo and the deployed demo are separate publication surfaces. Making the repo private and shipping a public demo containing CoMTA-derived content is the same violation with an extra step. Privacy only ever addresses the redistribution prong — never the training or purpose restrictions.
2. **"Same entity" is narrower than "private."** Sharing CoMTA with a collaborator at another institution is outside the grant even in a private repo.

MathDial is also the better substrate on the merits: 2,823 dialogues vs. 153, and the paper's models perform far better on it (76.7% vs. 65.8% AUC).

If CoMTA specifically is wanted later — its Table 3 dialogue is a good demo script — the clean route is requesting permission from Khan Academy, as the paper's authors did (see their acknowledgments). Do not route around this by paraphrasing CoMTA dialogues into "synthetic" ones; condition 4 covers derivatives.

## Architecture

Intended, not yet built.

- **The model is domain logic.** Its own module, no rendering imports, runnable headlessly. The right pane is a view over its output.
- **Every update must be inspectable.** Each hypothesis exposes the utterance fragment that triggered it. This is the demo's validation story: it claims *plausibility and inspectability*, not accuracy — there is no ground truth for "this utterance evidences component-swap." A black-box update defeats the purpose.
- **Deterministic and replayable.** Same path through the dialogue, same state.

**Nothing infers at runtime.** Because branching is scripted, the dialogue tree is finite: a build step runs the analysis over every node offline and ships the results as static data. No API key, no server, no non-determinism. Adding a branch means re-running the pass. The shipped artifact is the whole of what the right pane knows.

This also resolves what used to be the central constraint. LLMKT cannot run in a browser — it is a LoRA fine-tune of an 8B model, and the reference repo ships no precomputed predictions (`results/` is empty). That no longer blocks anything, because the demo precomputes its own analysis over authored content rather than replaying the paper's model. The **baseline** view is a separate matter — see the map's baseline ticket; it must be steelmanned, not strawmanned.

## Stack

Undecided — tracked as [issue #5](https://github.com/grumm1728/KT-tutor/issues/5) on the map. Constraints already fixed: static browser-shareable page, no runtime inference, no API key, values precomputed offline and shipped as data, with a build step baking them in. Replace this section with real install / dev / test / lint commands once chosen, including how to run a single test.

## Agent skills

### Issue tracker

Issues and PRDs live as GitHub issues in `grumm1728/KT-tutor`, managed via the `gh` CLI. See `docs/agents/issue-tracker.md`.

### Triage labels

The five canonical triage roles, using their default label strings. See `docs/agents/triage-labels.md`.

### Domain docs

Single-context — `CONTEXT.md` and `docs/adr/` at the repo root. See `docs/agents/domain.md`.
