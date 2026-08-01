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
- **Right — the model's view.** The conception state: **component ideas with polarity** — revealed present, revealed not-yet, or ambiguous — each carrying the utterance fragment that evidences it, grouped into six clusters.

The user steps the dialogue forward and watches the state on the right move. **The coupling between the two panes is the whole point.** A change that lets the phone advance without the model panel visibly reflecting *why* has broken the demo, even if both panes still render.

Three legs, all in scope: the richer input channel (`8+5 = "13"` vs. `"I took 2 off to fit with 8, then 3 more, so 13"`); conception modeling; and analysis running a beat behind realtime, handing the agent **cards** — probes that disambiguate the live competing hypotheses.

**The genre is a Number Talk.** Not a tutoring dialogue that happens to contain strategy talk — a *digital Number Talk* (Parrish; popularized by Boaler). An established classroom routine, inherently spoken, inherently multi-strategy, where the teacher's job is exactly what the right pane does: hear a strategy and represent it. This is what grounds the spoken commitment, not device affordances.

**The spine.**

> **17 + 8** — *"that's 24 because 4 on this side and 4 on that side."*
> Teacher: *"show me the 4?"*
> *"up to 20 I mean 25. 25 because 3 on this side and 5 on that side."*

The strategy — bridge to a landmark — is correct. The error is the **distance**: 17 to 20 is 3, not 4, so 20+4=24. The probe triggers self-repair.

It carries the card mechanic natively (the probe disambiguates *miscounted distance* from *doubles-pull*, since 8 splits naturally into 4+4), contains authentic self-repair speech, demonstrates Carpenter et al.'s finding that bugs are "often eliminated by simple counter suggestion," and diagnoses *"distance to the nearest ten"* — a concept the project's own Cantor writing names as first-class. A correctness-based KT sees `y=0` on the first turn and decrements mastery, which is exactly backwards.

**Two stress cases** that must not break the model:

- `24+26` — *"two 26s are 52 and then you take 2 away."* Correct answer, **composite** strategy (doubles then compensating). The strategy-behind-a-correct-answer case.
- `37+15` — *"it ends in a 2 and 42 is too small."* Not a computation strategy at all — reasons about properties of the answer. Justifies the escape hatch below.

**What the analyzer emits:** free-text description as the primary output, aligned onto the component set for visualization, with the alignment visible and an explicit **"not in this set"** escape hatch. A closed-set-only output would reintroduce multiple choice at the model layer — against the project's own claim.

### The state model

Settled in [#6](https://github.com/grumm1728/KT-tutor/issues/6). The traced state is **component ideas**, not strategies — strategy names live only in the free-text description.

- **The grain is Minstrell's *facets*** — clusters of 2–6 pieces around one situation, derived from what students actually say, each coded by how problematic it is. Operationalized as DIAGNOSER in the late 1980s; descended from diSessa's knowledge-in-pieces, which this project already cites. **Position the work as populating an established layer for early arithmetic, not as inventing a unit of analysis** — the latter is unfalsifiable and loses.
- **Six clusters, fifteen components, provenance marked** per component (cited / adapted / reused / coined). Four are coined and the artifact must say so, on the same logic that exposes the triggering fragment.
- **Independent confidences, not a distribution.** A simplex renders `24+26`'s genuine doubles-*and*-compensating as *uncertainty between* them. Independent values also match the baseline's `ẑ_jk`, so the toggle compares like with like.
- **No ordinal levels.** Representational progressions (counting-order → discrete/path → continuous/length) are *sibling components in a cluster*, because the research shows they conflict rather than nest (Solomon et al. 2015).
- **No gap-explanation layer and no conceptual/procedural tag.** Both withdrawn. Rival components inside a cluster carry that meaning, and the tag actively mislabelled the spine's error as a procedural slip when it is "hatch mark counting" (Kwon et al. 2024) — a documented stage signature persisting into middle school.

Detail in `docs/research/component-grain-frameworks.md`.

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
- **Provenance of the example utterances:** paraphrases of aggregate children from Scott's professional experience, plus loose quotes from his own children with consent. No third-party identifiable data. The artifact must nonetheless **disclose that they are composites, not recordings** — the demo argues about the authenticity of spoken input, so letting a viewer assume verbatim transcript would mislead in exactly the dimension under argument.
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

Settled in [#5](https://github.com/grumm1728/KT-tutor/issues/5). **TypeScript end-to-end**, npm, Vite + React, plain CSS, Vitest, ESLint 9 flat config with `typescript-eslint` — the same shape as `algebuds`, `splitcoins`, and `apps/v-game-app` in `scottfarrar-site`. No Tailwind.

**One language because one schema.** The component-state schema lives in `src/model/types.ts` and is imported by both the offline pass and the page. A second language would define it twice and let it drift, and drift here shows up as the right pane rendering something the analysis did not mean — the failure this project calls a broken demo.

```bash
npm install          # install
npm run dev          # vite dev server
npm run build        # tsc -b && vite build  → dist/
npm test             # vitest run
npm run lint         # eslint .
npm run precompute   # node scripts/precompute.ts — the offline analysis pass
```

Run a single test file:

```bash
npx vitest run src/model/invariants.test.ts
```

**Layout**, enforcing the architecture constraint that the model is domain logic:

- `src/model/` — headless, runs under Node, **no imports from `src/ui/`**
- `src/model/types.ts` — the single schema
- `src/model/invariants.test.ts` — walks the committed data and asserts what the artifact claims: provenance on every component, a triggering fragment on every hypothesis, confidences independent rather than summing to 1, no component rendered as a defect
- `src/ui/` — the two panes
- `scripts/precompute.ts` — the offline pass
- `data/` — its committed output (shape is [#10](https://github.com/grumm1728/KT-tutor/issues/10)'s call)
- `local/` — **gitignored**: CoMTA, plus one throwaway Python script for the Table 1 sanity check

**Node 22.18 strips TypeScript natively**, so `scripts/precompute.ts` runs under plain `node` with no `tsx` dependency. Stripping erases types without checking them — `tsc` in `npm run build` is what type-checks the pass. Imports need explicit `.ts` extensions.

**Python is not a toolchain here.** No `requirements.txt`, nothing in CI. It exists only as that one gitignored script, because CoMTA's CSVs are Python literals and `ast.literal_eval` is one line against a hand-written parser in TS — for data that never ships.

**Deploy:** its own Vercel project, static `dist/`, `base: './'`, no router. Built subpath-safe so it can later be vendored into `scottfarrar-site` the way `apps/v-game-app` is, without a rewrite.

## Agent skills

### Issue tracker

Issues and PRDs live as GitHub issues in `grumm1728/KT-tutor`, managed via the `gh` CLI. See `docs/agents/issue-tracker.md`.

### Triage labels

The five canonical triage roles, using their default label strings. See `docs/agents/triage-labels.md`.

### Domain docs

Single-context — `CONTEXT.md` and `docs/adr/` at the repo root. See `docs/agents/domain.md`.
