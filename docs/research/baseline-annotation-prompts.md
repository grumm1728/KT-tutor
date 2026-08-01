# The baseline's annotation and mastery prompts

Retrieved for [#2](https://github.com/grumm1728/KT-tutor/issues/2). This is the asset [#9 — Decide the baseline pipeline](https://github.com/grumm1728/KT-tutor/issues/9) depends on.

## Where the prompts actually live

Two sources, and they do not contain the same thing.

| Source | Contents | Licence |
|---|---|---|
| OSF `873ms` — *dialogue KT prompts.pdf* (60,392 B, SHA-256 `25060520…e44c`, modified 2024-09-19) | **5** prompts, each with system message, a worked user message, and the model's output | ACM/author supplementary material |
| OSF `wrkej` — *annotation instructions.pdf* (28,524 B, SHA-256 `4e67f112…0989`, modified 2024-09-22) | The **human**-evaluation rubric given to expert annotators | as above |
| [`umass-ml4ed/dialogue-kt`](https://github.com/umass-ml4ed/dialogue-kt) → `dialogue_kt/prompting.py` | **6** prompts plus the DKT-Sem turn-embedding template, as executable templates | **MIT** |

The PDFs are not committed here — they are third-party publication assets and this repo is public. The prompt *text* below is taken from the MIT-licensed `prompting.py`, which is reusable with attribution and is the authoritative form anyway (the PDF is a rendering of it).

Both OSF files sit on project `7g58j` and are reachable with the `view_only` token given in footnotes 2 and 4 of the paper.

**The PDF omits one prompt.** `ANNO_BASE_SYSTEM_PROMPT` — the non-ATC, free-text KC tagger — is in the code but not in the supplement. See "The free-text KC path" below; it matters to our argument.

## The pipeline the prompts sit in

`annotate.py` runs one of two tagging modes over a whole dialogue at a time. Both are **dialogue-level** calls, not per-turn.

**ATC mode** (`--tag_src atc`, the one that produced the shipped `*_atc.csv`) is a three-stage recursive narrowing, plus a fourth independent call:

1. **Domain** — pick from all 11 Common Core domain groups, whole-dialogue, returns `result = [...]`.
2. **Cluster** — options are the children of the chosen domains; whole-dialogue, returns a flat list of cluster IDs.
3. **Standard** — options are the children of the chosen clusters; **per-turn**, returns `{"turn 1": [ids], …}`.
4. **Correctness** — a separate call on the same dialogue, per-turn, `{"turn 1": "true"/"false"/"na", …}`.

Stages 2 and 3 drop any dialogue whose parent stage failed to parse; `combine_kcs_and_correctness` then rejects a dialogue outright if the KC and correctness turn counts disagree, or if either disagrees with the source turn count. Failures are recorded as `{"error": …}` rather than retried. Standard IDs are swapped for their **full descriptions** at this step — which is why a KC is identified by standard text and not by code.

Every call: `gpt-4o`, `max_tokens=4000`, batch size 10, **temperature 0**. `extract_result` strips `\(`/`\)`/`\pi` before JSON parsing, because LaTeX in the dialogue breaks it.

**The ATC map is not in the repo.** `load_atc()` reads `data/src/ATC/domain_groups.json` and `data/src/ATC/standards.jsonl`, and `data/src/` ships empty. The README says the HuggingFace copy was broken at release. **It is working now**: [`allenai/achieve-the-core`](https://huggingface.co/datasets/allenai/achieve-the-core) is public and ungated, both files present, ~348 KB total, licensed **ODC-BY** — attribution only, no non-commercial or share-alike clause. Nothing blocks using it in a public repo or a deployed page.

## The prompts

`{desc}` is the only dataset-specific slot in any system message:

- CoMTA — *"the student is learning about math concepts."*
- MathDial — *"the student is attempting to solve a math problem. You are also given this problem, its correct solution, and the incorrect solution the student initially gave."*

### Correctness

> You are an experienced math teacher and education expert. You are given a dialogue between a student and teacher where {desc} Your job is to determine if the student has correctly answered each of the teacher's questions in the dialogue. Please follow these instructions carefully when making your prediction:
> - The prediction should be "na" if the teacher asks a question that 1) does not attempt to assess the student's knowledge, or 2) does not necessarily have a right or wrong answer.
> - If the question 1) does assess student knowledge and has a correct answer, and 2) the student does not respond directly to the question, then the prediction should be "false".
> - Do not give a prediction for turn 0 since the teacher does not ask a question.
> - Before giving your final response, write a short summary of each turn, including the intended learning objectives, and explain why the student's response is right or wrong.
> - Your final prediction should be a JSON object using the template: result = {"turn 1": "true/false/na", "turn 2": ...}

Note the fourth bullet: the model **does** produce a written rationale for each turn — and then discards it into one of three tokens. This is the project's thesis stated in the baseline's own prompt.

### KC tagging — domain stage

> …Your job is to list the common core domains that can be used to classify the learning objectives in this dialogue…
> - You will be given a list of common core domains to choose from. When choosing them, write their names exactly as they appear. Do not use any domains that are not in this list.
> - Before giving your final response, write a short summary of the dialogue.
> - Your final response should be a list using the template: result = ["domain 1 name", "domain 2 name", ...]

### KC tagging — cluster stage

Same shape, over "common core math concepts/skills", with two added instructions: teacher turns are usually questions, so choose the skills *the student needs in order to answer*; and list **all** candidate skills per turn before answering, including duplicate-description IDs.

### KC tagging — standard stage

Same again, over standards, plus: no standards for turn 0, and the result is a per-turn JSON object. The user message appends `There should be exactly N turns in your final result.`

### LLMKT mastery

> You are an experienced math teacher. You are given a dialogue between a student and teacher where {desc} Your job is to predict if the student has a particular knowledge component at the current point in the dialogue. Please follow these instructions carefully when making your prediction:
> - The student will need to possess this knowledge component in order to respond correctly to the teacher's most recent question.
> - Use previous information in the dialogue to determine if the student has this knowledge component or not.
> - Only respond with a single word, "True" or "False".

The user message is the dialogue **truncated after the teacher's turn `j`**, then `Knowledge Component: <full standard text>`. `ẑ` is the softmax over the `True`/`False` token logits.

### DKT-Sem turn embedding

A flat template — teacher turn, student turn, KC list, `Student Correct: true/false/na` — S-BERT-embedded and fed to the LSTM. Note what it keeps of the student's utterance: the text, yes, but the correctness label is appended as a separate literal.

## Two things in the code that change how we should build the baseline

**1. At the moment of prediction, LLMKT has not seen the utterance being scored.** `get_dialogue_text(..., turn_idx=j)` stops after teacher turn `j`, with the comment that including the student utterance *"would leak the correctness label in KT objective."* That is correct KT practice — mastery at turn `j` predicts response `j` — but it means the released baseline reads a student's explanation only on **subsequent** turns.

So the map's steelman ("grant the baseline full text access") has to be stated precisely, or it collapses into the argument the map already ruled out. The honest form: the baseline *does* read the strategy talk, one turn later, and it still has nowhere to put it except a scalar per standard. On our spine, that is the sharp version — the `17+8` explanation lands in context for the *"show me the 4?"* turn and the only thing it can move is `ẑ`.

**2. Zero-shot LLMKT does not need the fine-tune.** `model_type: lmkt` with no trained checkpoint is zero-shot; the LoRA is what `train` produces. A build-time zero-shot LLMKT pass over authored content is therefore available without a GPU — which keeps "training LLMKT" out of scope while still allowing a faithful, not strawmanned, baseline pane. Accuracy will not match the paper's 65.8%, and the artifact must not imply it does.

## The free-text KC path

`ANNO_BASE_SYSTEM_PROMPT` (code only, absent from the supplement) asks for KCs as free text rather than from the ATC list:

> - Each math concept/skill should be short description of a single learning objective. They should be generic enough so that they can be applied across dialogues and educational settings.
> - When applicable, math/concepts skills should be repeated across turns.

This is prior art *inside the baseline* for emitting free text rather than picking from a closed set, and [#13](https://github.com/grumm1728/KT-tutor/issues/13) should acknowledge it rather than be surprised by it. It does not dent the claim: the output is still a **label list naming the objective of the teacher's question**, still per-turn, and each label still collapses to one scalar `ẑ`. Nothing in it describes *how the student got there*.

## Can we run this faithfully on authored content?

Yes, with no blocker found. What it takes:

- **A third `{desc}`.** `get_dataset_desc` raises on an unknown dataset; our Number Talk needs its own one-line framing. Trivial, and worth writing deliberately — it is the only place the prompts learn what kind of conversation they are reading.
- **A loader branch.** `load_src_data` and `load_annotated_data` both hard-raise on unknown datasets. Authored dialogues need to arrive as `[{'turn', 'teacher', 'student'}]`; `process_dialogue` merges consecutive same-role utterances and numbers a student-opening dialogue from turn 0. Roughly 15 lines.
- **The ATC map**, from HuggingFace as above. Our content is addition within 100, so the domain stage should land on *Operations & Algebra* and/or *Number & Operations in Base Ten*.
- **An OpenAI key at build time** — annotation only, never at runtime, consistent with the precompute architecture.

Caveats rather than blockers:

- **`temperature=0` is not bit-determinism** with gpt-4o. Determinism holds because the outputs are committed, not because the pass is reproducible. Re-running the pass may shift a label.
- **The pipeline is all-or-nothing per dialogue.** A turn-count mismatch voids the whole dialogue. With one deep branched dialogue rather than 153 short ones, each branch path has to be annotated and validated on its own.
- **Correctness on our spine is the demonstration.** Turn 1 of `17+8` — *"that's 24 because 4 on this side and 4 on that side"* — gets `"false"`, and the prompt will have written out the reason before throwing it away. Worth capturing the discarded rationale verbatim when we run it: it is the most direct evidence the artifact can show.
- **CoMTA-derived material stays out.** The supplement's worked example is a CoMTA dialogue, so it is described here, never quoted.

## The human-evaluation rubric (OSF `wrkej`)

Not needed to run anything, but it fixes what the paper's expert agreement numbers mean. Experts rated 30 dialogues, ~2 hours, on two axes:

- **Correctness** — binary: is the AI's `true`/`false`/`na` accurate? The instructions warn annotators not to trust the tutor's own next-turn reaction (*"Excellent!"*), because tutors misjudge.
- **Standards** — a 1–4 Likert on whether the selected standards *describe* and *completely cover* the learning objectives of the **tutor's question only**, explicitly not how the student responded. A 1 is also mandated when the list should have been empty and wasn't, or vice versa.

The framing is worth noting: the standards axis judges the labels against the *teacher's question*, never against the student's thinking. There is no axis on which a rubric like this could reward or penalise reading a strategy — a rubric-level echo of the output-representation bottleneck.

---

*Sources: Scarlatos, Baker & Lan (2025), arXiv:2409.16490, supplementary material on OSF project `7g58j`; `umass-ml4ed/dialogue-kt` (MIT); `allenai/achieve-the-core` (ODC-BY).*
