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

### Licensing — affects what can be committed

This repo is public. The two datasets have incompatible terms:

- **CoMTA** (`COMTA_LICENSE.txt`, © Khan Academy) permits *internal, non-commercial model evaluation only* and **expressly prohibits redistribution or publication**, including of derivatives. It must not be committed here, and mastery trajectories derived from it are themselves derivatives. Keep it local and gitignored.
- **MathDial** is CC BY-SA 4.0 — redistributable with attribution, but ShareAlike propagates to derived data.

So: **hand-authored or synthetic dialogues are the safe default for anything committed.** Use the real datasets locally for validation. Confirm with Scott before committing any file derived from either.

## Architecture

Intended, not yet built.

- **The KT model is domain logic.** Its own module, a pure `update(state, turn) -> state`, no rendering imports, runnable headlessly over a dataset. The right pane is a view over its output.
- **Every update must be inspectable.** Show prior → evidence → posterior per KC, not just a final number. A black-box update defeats the purpose.
- **Deterministic and replayable.** Same dialogue in, same trajectory out.

**The central constraint: LLMKT cannot run in a browser.** It is a LoRA fine-tune of an 8B model. Any demo claiming to show LLMKT must therefore either (a) replay **precomputed** per-turn mastery values captured offline, or (b) run a lightweight model live and *label it as such*. Note that the reference repo ships **no** precomputed predictions — `results/` is empty, so option (a) requires actually training LLMKT on a GPU first. BKT is the only model here that is honestly implementable client-side; if that is what runs, the UI must say so rather than implying it is the paper's method. Do not let the demo blur this.

## Stack

Undecided — settle with Scott before scaffolding. The binding constraint is browser-shareability. Replace this section with real install / dev / test / lint commands once chosen, including how to run a single test.
