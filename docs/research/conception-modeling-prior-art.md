# Prior Art: Modeling *Which Conception* a Student Holds

Research note for KT-tutor, issue #4.
Compiled 2026-07-30. All claims below were checked against primary sources unless marked otherwise.

**Verification legend**

- **[V]** — verified against the primary source (paper PDF/HTML read directly)
- **[V-meta]** — citation metadata verified against an authoritative index (ACL Anthology, publisher page, author's own publication list), but full text not read
- **[U]** — unverified; secondary source only. Treat as a lead, not a fact.

---

## 0. Executive summary

Three things you should act on:

1. **A public labeled corpus of student free-text explanations tagged with misconceptions exists, is large, and is downloadable.** The Kaggle **MAP — Charting Student Math Misunderstandings** dataset (Vanderbilt + The Learning Agency + Eedi, 2025) has ~36,700 rows with a `StudentExplanation` free-text field and `Category` / `Misconception` labels. This is a direct hit on the question "does a public labeled corpus of student verbal strategy explanations exist?" — the answer is **yes for misconceptions in written short-form explanations**, and **still no for spoken/dialogic strategy explanations**. See §4. **This should reopen the scoping decision.**

2. **The Siegler-vs-averaging connection is real but you are pointing it at the wrong target.** Siegler (1987) is not primarily about averaging *across children*; it is about averaging across *strategies*, including within a single child. That distinction makes the argument stronger in one direction and weaker in another (§2.1). And it does *not* cleanly indict the mean-over-KCs equation — the sharp citation for that is the compensatory-vs-conjunctive debate in cognitive diagnosis (§2.8). Siegler's real load-bearing contribution to your thesis is **methodological**: it is the canonical demonstration that per-trial verbal self-report recovers structure that aggregate performance data actively hides. Lead with that, not with the averaging analogy.

3. **The list is missing the most direct competitor.** Berthon & van der Schaar, *Language Bottleneck Models for Qualitative Knowledge State Modeling* (arXiv:2506.16982, rev. Feb 2026) replaces the scalar mastery vector with an LLM-generated **natural-language knowledge-state summary**, explicitly on the grounds that KT/CD models "cannot capture" misconceptions. That is adjacent enough to KT-tutor's thesis that the demo needs to say how it differs. Also missing: **Ghosh, Raspat & Lan's Option Tracing (AIED 2021)** — same lab as the source paper, and the *actual* in-lab precedent for "predict which wrong thing, not whether wrong." See §5.

---

## 1. The baseline: what Scarlatos, Baker & Lan (2025) actually does

**[V]** Scarlatos, A., Baker, R. S., & Lan, A. *Exploring Knowledge Tracing in Tutor-Student Dialogues using LLMs.* LAK 2025. arXiv:2409.16490.

Verified from the arXiv HTML full text:

- **The mean-over-KCs claim in the ticket is correct.** LLMKT defines "the student's likelihood of responding correctly to a tutor-posed task to be their average mastery of all KCs involved in the turn," i.e.
  `P(y_j = 1) = (1/K) · Σ_k P(z_jk = 1)`.
  This is explicitly **compensatory** — high mastery on one KC offsets low mastery on another — and is not a product/conjunctive form.
- **Datasets:** CoMTA (153 dialogues, 623 labeled turn pairs after Calculus removal; 188 originally) and MathDial (2,823 dialogues, 13,200 labeled turn pairs).
- **Stated limitations:** dataset scale smaller than standard KT work; time steps are single student turns (very granular); math-only; CoMTA contains truncated dialogues; cannot track students across dialogues.
- **Correction to the ticket's framing of the reference list.** The paper **does** cite Makatchev & VanLehn (2007) and VanLehn et al.'s Why2-Atlas (2002) — in the related-work section, describing Why2-Atlas as converting a student response to a proof and analyzing "the proof for errors represented as lack of knowledge," later using Bayesian networks. It also cites AutoTutor's LSA-based misconception detection. It **does not** cite Siegler, diSessa/Smith, Brown & Burton, Ohlsson, DINA, or DiVERT.

**Why this matters for positioning:** the two dialogue-conception leads (Makatchev, Why2-Atlas) are already *in* the baseline's related work. You are not surfacing forgotten prior art there — you are arguing the authors cited that lineage and then didn't build on it. That is still a fine argument, but it is a different argument, and reviewers who have read the paper will notice if you claim otherwise.

Note also: "errors represented as **lack of knowledge**" is precisely the framing Smith/diSessa/Roschelle attack (§2.2). That is the cleanest single sentence connecting your critique to the baseline's own text.

---

## 2. Lead-by-lead verification

### 2.1 Siegler (1987) — verified, but the ticket's gloss is wrong in an important way

**[V]** Siegler, R. S. (1987). *The Perils of Averaging Data Over Strategies: An Example From Children's Addition.* Journal of Experimental Psychology: General, **116**(3), 250–264. (Read from the author's archived PDF; title, journal, volume, issue, pages, and summary text all confirmed.)

**What it actually claims** (from the paper's own Summary and body):

- Prior chronometric models concluded that young children *always* use the **min** strategy (count up from the larger addend), because mean solution times were best predicted by the size of the smaller addend (Groen & Parkman 1972: ~70% of variance; Ashcraft 1982 replicated).
- Siegler collected **per-trial verbal reports** of strategy alongside solution times. "When solution times on all trials were analyzed together, as in earlier studies, the results were entirely consistent with the view that children always use the min strategy. However, the verbal reports suggested a quite different picture."
- **The min strategy was one of five approaches children reported.** Most children reported using at least three. "Neither the min strategy nor any other approach was used on as many as 40% of trials."
- Validation: on trials where children *reported* min, the min model predicted solution times **better** than in past studies or in the pooled data. On trials with other reported strategies, "the min model was never a good predictor of performance, either in absolute terms or relative to other predictors."
- Three named mechanisms by which averaging misleads: (a) **relative frequency** of each strategy, (b) **relative variability** of the dependent measure produced by each strategy, (c) **independent–dependent variable relations across and within strategies**. He demonstrates each on synthetic data in an appendix.
- Generality: "There is no reason to think that variability of strategy use within a single person is limited to children or to arithmetic" — cites spelling, telling time, mental rotation, series completion, missionaries-and-cannibals.

**Where the ticket's framing is off.** The ticket says: *"Averaging across children using different strategies yields a curve matching no actual child."* That is not what the paper shows, and the paper contains a sentence that directly contradicts it: on p. 252 Siegler notes that "the min model fits **individual** children's solution time patterns as well as group averages" (citing Kaye et al. 1986; Groen & Resnick 1977; Svenson et al. 1976). The aggregate curve *did* match actual children — that was exactly the trap.

The correct statement is: **averaging over strategies yields a curve matching no actual strategy, and per-child aggregation does not rescue you, because a single child mixes strategies trial to trial.** He opens by explicitly extending Estes (1956) — "just as data aggregated over people may not accurately reflect the behavior of any person… so data aggregated over strategies may not accurately reflect the characteristics of any strategy."

Fix the framing before it goes in a paper. As written it is a smaller and more familiar claim (the classic aggregation artifact); as Siegler actually states it, it is a sharper and less familiar one, and it forecloses the obvious rebuttal "fine, we'll fit per-student models."

**Does it land on the mean-over-KC-masteries equation? Partially — and you should say "partially."**

- **Honest reading:** Siegler's target is averaging a *dependent measure* (solution time, error rate) over *trials* generated by different latent processes. LLMKT's `(1/K)Σ_k P(z_jk)` averages *latent mastery estimates* over *knowledge components* within a single turn. These are different axes. Calling them the same thing is a stretch, and a knowledgeable reader will call it out.
- **What genuinely does transfer:** the single scalar `P(z_jk = 1)` — "mastery of KC k" — is itself an average over whatever strategies the student might deploy on KC k. If a student reliably succeeds on KC k via strategy A and reliably fails via strategy B, and picks between them by problem features, then `P(z_jk)` is a mixing proportion masquerading as a competence. That *is* Siegler's point, applied to KT's latent variable rather than to KT's aggregation step. This is the defensible version. Make it in exactly these terms.
- **The compensatory mean specifically** is better attacked with the CDM literature (§2.8), where "should skills combine conjunctively or compensatorily?" is a settled research question with formal models on both sides, not an analogy.

**Verdict: keep Siegler, demote the averaging analogy, promote the methodology.** Siegler 1987 is your strongest single citation for the proposition that *asking the student to say how they did it recovers structure that outcome data cannot*, and that the recovered structure is validated (strategy-conditional models fit better than pooled models). That is the KT-tutor thesis, demonstrated in 1987 without an LLM. Do not bury it under a weaker argument about means.

Related Siegler work worth citing alongside: Siegler & Shrager (1984); the **overlapping waves** framing (Siegler, *Emerging Minds*, 1996) **[U]** — that children hold multiple strategies simultaneously with shifting frequencies, rather than passing through discrete stages.

### 2.2 Smith, diSessa & Roschelle (1994) — verified, correctly characterized, and the right theoretical spine

**[V-meta]** Smith, J. P. III, diSessa, A. A., & Roschelle, J. (1994). *Misconceptions Reconceived: A Constructivist Analysis of Knowledge in Transition.* Journal of the Learning Sciences, **3**(2), 115–163. (Metadata confirmed via Taylor & Francis and the publisher record; full PDF fetch failed twice, so treat detailed claims as [U] until you read it. It is widely available; get the PDF.)

**[U]** Substance, from the abstract and secondary accounts: the paper critiques the misconceptions research program for treating student conceptions as **defects to be confronted and replaced**, and argues instead that student conceptions play **productive roles** in acquiring expertise — that expert knowledge is built by refinement and re-contextualization of intuitive resources, not by their eradication. Companion reading: diSessa's **p-prims** / knowledge-in-pieces (diSessa 1993, *Toward an epistemology of physics*) **[U]**, and the diSessa & Sherin / Smith exchange in JLS 5(2) 1996 **[U]**.

**Bearing on KT-tutor:** this is your theoretical justification for why "which conception" is the right target rather than "which bug." It gives you a principled reason to prefer *identifying and building on* a student's conception over *detecting and correcting* an error. It also gives you a live tension to acknowledge: knowledge-in-pieces argues conceptions are **fine-grained, context-sensitive, and not stable discrete entities** — which cuts against any modeling approach that assigns a student one label from a fixed taxonomy. If your demo outputs "student holds misconception #17," you are doing the thing Smith/diSessa/Roschelle criticize, just with an LLM. Address this head-on; it is the sharpest available critique of your own design.

**Strength of connection: strong theoretically, but it is a position paper, not a method.** It gives you framing and a stick to hit "errors as lack of knowledge" with. It gives you nothing computational.

### 2.3 DiVERT (EMNLP 2024) — verified, same lab, genuinely the closest in-lab precedent for "represent the error as text"

**[V-meta]** Fernandez, N., Scarlatos, A., Feng, W., Woodhead, S., & Lan, A. (2024). *DiVERT: Distractor Generation with Variational Errors Represented as Text for Math Multiple-choice Questions.* EMNLP 2024 (Main), pp. 9063–9081 region. arXiv:2406.19356. ACL Anthology 2024.emnlp-main.512.

**[V-meta]** Verified content: a **variational** approach that learns an **interpretable, textual** representation of the error behind each distractor. Evaluated on a real-world math MCQ dataset of **1,434 questions** used by hundreds of thousands of students (Eedi-derived). A 7B open-source base model outperforms GPT-4o-based approaches on downstream distractor generation. Human evaluation with math educators found DiVERT's **error labels comparable in quality to human-authored ones**.

**Bearing:** this is the single most useful lead on the list for arguing "the same lab already accepts that errors should be represented as *text*, learned, and evaluated against expert labels." Your move is: DiVERT does this for **item authoring** from **multiple-choice distractors**; KT-tutor proposes it for **student modeling** from **free-text explanation**. That is a clean, honest delta, and it makes your contribution look like a natural extension of their own program rather than an outside critique.

**Caveat, and it is a real one:** DiVERT's latent variable is per-*distractor* (a property of the item), not per-*student*. It does not track a student. Do not describe it as conception tracing.

### 2.4 Makatchev & VanLehn (2007) — verified, and weaker than the ticket implies

**[V]** Makatchev, M., & VanLehn, K. (2007). *Combining Bayesian Networks and Formal Reasoning for Semantic Classification of Student Utterances.* Proceedings of AIED 2007, Los Angeles, pp. 307–314. (Read the full PDF from the author's CMU page.)

What it actually does:

- Maps NL sentences from student physics explanations into **first-order predicate representations**, then matches them against the automatically generated **deductive closure** of hand-encoded problem givens, buggy assumptions, and domain rules. Adds conditional probabilities to the closure graph to form a Bayesian network, so the BN *structure* is derived formally rather than learned (to cope with sparse training data). Parameters estimated by EM.
- Scale: **one physics problem** (the "Pumpkin" problem). ~20 correct and 4 incorrect belief classes relevant to it; investigation limited to the **16 classes** coverable with reasonable knowledge-engineering effort. Manual encoding of **46 problem-specific givens and 18 domain rules**. Deductive closure to depth 6 → 159 fact nodes; full BN 282 boolean nodes.
- Corpus: **293 labeled NL utterances** from a Spring/Summer 2005 study. 35.5% carry the empty label.
- Results (10-fold CV, F-measure): majority baseline 0.390; direct graph matching 0.468; radius-0 closure matching 0.478; radius-1 0.476; untrained BN 0.128; **trained BN 0.497** — the best result, and the paper's own conclusion is that "these results… indicate just how hard this particular classification problem is."

**The important correction — this lead is mis-framed in the ticket.** The paper explicitly *excludes* misconception classification from its evaluation:

> "In this paper we will consider an evaluation of the classifying of facts, which is a part of the measure of *completeness*, as opposed to classifying of misconceptions, which we referred to as *correctness*."

So it is **not** a demonstration of diagnosing which misconception a student holds from an utterance. It is a demonstration of detecting which *correct* domain facts a student's utterance covers. The misconception-classification half is treated as a separate diagnosis problem and handled in the companion work (Makatchev & VanLehn, AIED 2005, *Analyzing completeness and correctness of utterances using an ATMS*) **[U]** and Makatchev, Jordan & VanLehn (2004), *Abductive theorem proving for analyzing student explanations*, J. Automated Reasoning 32:187–226 **[V-meta, from this paper's own reference list]**. **If you want the misconception-diagnosis-from-utterance claim, cite the 2004 JAR paper or the 2005 AIED paper, not this one.**

**Bearing on KT-tutor — and this is the good news.** As the strongest pre-LLM attempt at this exact task, its numbers are your best argument that the problem was *intractable before LLMs*: one hand-engineered physics problem, 293 utterances, ~50% F on 16 classes, and the authors saying it is hard. That is a far more compelling "why now" than any rhetorical framing. Use the numbers.

### 2.5 Why2-Atlas (ITS 2002) — verified metadata, correct in spirit, thin as evidence

**[V-meta]** VanLehn, K., Jordan, P. W., Rosé, C. P., Bhembe, D., et al. (2002). *The Architecture of Why2-Atlas: A Coach for Qualitative Physics Essay Writing.* Intelligent Tutoring Systems (ITS 2002), LNCS 2363, pp. 158–167. Springer. (Author list and page range confirmed from Makatchev's CMU publication list; PDF fetched but OCR-limited.)

**[V, from Makatchev & VanLehn 2007's own description]** Why2-Atlas presents a qualitative physics problem, asks the student to type an **answer plus an explanation**, then "analyzes it for errors and missing statements and starts a dialogue that attempts to remediate misconceptions and elicit missing facts." The design rationale stated there is explicitly the one KT-tutor is reviving: "allowing students to provide unrestricted input to a system would trigger meta-cognitive processes that support learning (i.e. self-explaining) and help expose misconceptions."

**Bearing:** the strongest **prior-intent** citation you have — the exact hypothesis, stated in 2002, that free-form explanation exposes conceptions that constrained input cannot. Cite it as intellectual precedent. Do **not** cite it as an existence proof of a working conception model at scale; the system was a hand-authored, single-domain, essay-based ITS. Also note: this is written essay input, not dialogue turns.

### 2.6 Pardos, Farrar, Kolb, Peh & Lee (ICLS 2018) — verified, and the ticket's framing is accurate

**[V-meta]** Pardos, Z. A., Farrar, S., Kolb, J., Peh, G. X., & Lee, J. H. (2018). *Distributed Representation of Misconceptions.* Proceedings of the 13th International Conference of the Learning Sciences (ICLS 2018), pp. 1791–1798. (Confirmed via the University of Minnesota research portal, which lists the full author set, venue, year, and pages.)

**[V-meta]** What it does: learns **high-dimensional vector representations of incorrect student answers** from answer-sequence data across three Khan Academy exercises, using a distributed-representation (word2vec-style) model borrowed from NLP. Clusters wrong answers in the learned space and uses the clusters as the unit of misconception analysis, with a **quantitative comparison against manual coding** and qualitative discussion. Framed as bridging tutoring-system big data to learning-sciences pedagogy "via a distributed, connectionist model of student concept formation."

**Ticket framing check: accurate.** "Misconception structure learned from wrong-answer data at scale rather than hand-authored" is exactly right.

**Bearing:** this is your precedent for *learning* the conception space instead of enumerating it — which is also the answer to the Smith/diSessa objection in §2.2 (don't impose a fixed taxonomy; induce a continuous space). Note the limitation you'll want to name: the input is **the wrong answer itself**, a short symbolic string. It has no access to *why* the student produced it. That is the gap KT-tutor claims to fill. Nice, clean setup.

*Disclosure note:* the second author shares a surname with the repo owner's email. If that is a personal connection, disclose it in any writeup that cites this.

### 2.7 Brown & Burton / BUGGY & DEBUGGY, and Repair Theory — confirmed

**[V-meta]** Brown, J. S., & Burton, R. R. (1978). *Diagnostic Models for Procedural Bugs in Basic Mathematical Skills.* Cognitive Science, **2**(2), 155–192.
**[V-meta]** Brown, J. S., & VanLehn, K. (1980). *Repair Theory: A Generative Theory of Bugs in Procedural Skills.* Cognitive Science, **4**(4), 379–426.
**[U]** Burton, R. R. (1982). *Diagnosing Bugs in a Simple Procedural Skill* (DEBUGGY), in Sleeman & Brown (eds.), *Intelligent Tutoring Systems*.

**[U]** Substance: a student's errors are symptoms of a **bug** — a discrete modification to the correct procedure that reproduces the student's behavior exactly. BUGGY/DEBUGGY analyzed thousands of students' place-value subtraction work and produced an extensive catalogue of precisely defined systematic errors. Repair Theory then argued the bug catalogue is *generative* — bugs arise from constrained problem-solving ("repairs") applied when an incomplete procedure reaches an impasse — and predicted bug frequency, bug instability, and latency signatures.

**Bearing:** this is the **origin** of "model which wrong thing, not whether wrong," and the single best historical anchor for KT-tutor's framing. Two points to make honestly:

1. It is **procedural** (subtraction algorithms), not **conceptual**. Bugs are perturbed procedures; conceptions in the Smith/diSessa sense are something else. Don't conflate them.
2. Its input is **the written answer/worked solution**, and its diagnosis is by **model tracing over a hand-built bug library** — it does not read explanations. And the field's own retrospective is that bug libraries proved brittle and unstable across students and time, which is exactly why Ohlsson proposed constraints (§2.7b) and why Pardos et al. proposed learning them (§2.6). That arc — hand-authored → constraint-based → learned → learned-from-language — is a good narrative spine for your related-work section.

### 2.7b Ohlsson's constraint-based modeling — confirmed

**[V-meta]** Ohlsson, S. (1994). *Constraint-Based Student Modeling*, in Greer & McCalla (eds.), *Student Modelling: The Key to Individualized Knowledge-Based Instruction*, Springer-Verlag, pp. 167–189.
**[V-meta]** Ohlsson, S. (1996). *Learning from Performance Errors.* Psychological Review, **103**(2), 241–262.
**[V-meta]** Ohlsson, S. (2016). *Constraint-Based Modeling: From Cognitive Theory to Computer Tutoring — and Back Again.* IJAIED, 26(1). Mitrović's SQL-Tutor line is the main implementation.

**[U]** Substance: CBM was proposed to escape the intractability of bug-library student modeling. Instead of enumerating wrong procedures, you enumerate **constraints** — relevance condition plus satisfaction condition — and diagnose by which constraints a solution violates. Any solution violating no constraint is acceptable, regardless of path.

**Bearing — and be blunt here: this is the lead most in tension with KT-tutor's thesis.** CBM's whole design premise is that you **do not need to know the student's strategy or reasoning path** to tutor effectively; it deliberately discards path information as intractable and unnecessary. Mitrović's line reports CBM tutors producing strong learning gains anyway. That is the strongest available "so what?" rebuttal to your demo, and a reviewer will raise it. You need an answer: presumably that constraint violation tells you *that* something is wrong and *what property* is violated, but not *what the student believes*, and therefore cannot select between remediations that address different underlying conceptions. Have that answer ready and evidenced.

### 2.8 Cognitive diagnosis models (DINA, DINO, Q-matrix, de la Torre) — confirmed, and more directly relevant than the ticket suggests

**[V-meta]** Junker, B. W., & Sijtsma, K. (2001). *Cognitive assessment models with few assumptions, and connections with nonparametric item response theory.* Applied Psychological Measurement, **25**(3), 258–272. (Standard reference for DINA and DINO.)
**[V-meta]** de la Torre, J. (2011). *The Generalized DINA Model Framework.* Psychometrika, **76**(2), 179–199.
**[V-meta]** de la Torre, J. (2009). *A cognitive diagnosis model for cognitively-based multiple-choice options* (**MC-DINA**). Applied Psychological Measurement, **33**(3), 163–183.

Substance **[V-meta]**: the **Q-matrix** specifies which latent attributes each item requires. **DINA** is **conjunctive/non-compensatory** — you need *all* required attributes to have a high success probability. **DINO** is disjunctive. **G-DINA** generalizes across the compensatory/non-compensatory spectrum.

**This is where the mean-over-KC critique should actually be grounded.** LLMKT's `(1/K)Σ_k P(z_jk)` is a fully compensatory, equal-weight aggregation. The CDM literature has spent 25 years establishing that (a) the conjunctive/compensatory choice is an empirical question, (b) it is item-dependent, and (c) getting it wrong systematically distorts attribute-mastery estimates. For most math tasks where all listed KCs are genuinely required, the conjunctive form is the theory-preferred one, and averaging is the *most* forgiving possible aggregator — it lets a student who has mastered 3 of 4 required KCs be predicted correct with p = 0.75 on a task they cannot actually complete. **That is a concrete, quantifiable, citable objection to the baseline, and it is much harder to argue with than the Siegler analogy.** Lead the methodological critique with this and use Siegler for the conceptual critique.

**MC-DINA is the second key item and the ticket underweights it.** de la Torre (2009) extends CDM so that latent attributes govern not only the correct response but **which distractor** an examinee chooses — i.e. the response is nominal, not dichotomous, and each coded option maps to an attribute pattern. This is "model which conception, not whether right" in the psychometrics tradition, from 2009, with identifiability theory attached. Any claim that the field has only modeled correctness is false and MC-DINA is the counterexample. **[U]** Known constraint: in de la Torre's original formulation the distractors' attribute vectors are nested within the key and within each other, which limits the conception structures it can express — later work reparameterizes this (see *On a Reparameterization of the MC-DINA Model*, Psychometrika-adjacent, PMC11897991 **[U]**).

---

## 3. Open problems — what this literature has *not* solved

Framed as the honest gap statement for issue #4.

1. **Conception modeling from open-ended language has never been done at scale in dialogue.** The pre-LLM systems that read explanations (Why2-Atlas, Makatchev & VanLehn, AutoTutor) were **single-domain, single-problem, hand-engineered**, and their numbers were poor: 293 utterances, one physics problem, F ≈ 0.50 on 16 classes with a 0.39 majority baseline. The systems that scaled (BUGGY, CBM, CDM/MC-DINA, DINA, Pardos et al., DiVERT, Eedi) all read **structured responses** — answers, distractors, option selections — not explanations. **[Now heavily qualified by §4: the MAP dataset is exactly the missing corpus for the *written short-explanation* case, and models on it reach MAP@3 > 0.94. The unclaimed territory is narrower than it was 18 months ago.]**

2. **Nobody has a conception representation that survives the knowledge-in-pieces objection.** Every scalable approach either enumerates a fixed taxonomy (Eedi's 2.5k misconceptions; MC-DINA's coded options; bug libraries) or learns an opaque embedding space (Pardos et al.). Neither is a **fine-grained, context-sensitive, compositional** conception representation of the kind Smith/diSessa/Roschelle argue is actually needed. DiVERT's *learned textual* error representation is the nearest thing, and it is per-item, not per-student.

3. **Conception is not tracked over time.** BUGGY diagnoses at a point in time. CDM estimates a static attribute profile. DiVERT labels items. Pardos et al. cluster answers. KT tracks a *scalar per KC* over time. **There is no established model that tracks which conception a student holds and how it evolves across turns.** This is the genuine, defensible white space, and it is where KT-tutor should plant its flag — not on "conceptions matter" (settled since 1978) or "explanations are informative" (settled since 1987/2002).

4. **Strategy ≠ misconception, and almost everything modern conflates them.** Siegler's five addition strategies are all *legitimate*; the diagnostic value is in knowing which one, not in it being wrong. The entire misconception literature (Eedi, MAP, MC-DINA, BUGGY, DiVERT, MISTAKE) is conditioned on the student being **incorrect**. **No public corpus labels the strategy behind *correct* answers.** If KT-tutor's claim is "which conception, not whether right," then the correct-answer case is the part that is genuinely unserved — a correct answer via a fragile procedure and a correct answer via robust understanding are indistinguishable to every model surveyed here. This is probably the sharpest framing available to you, and §4 does not undercut it.

5. **Verbal-report validity at LLM scale is unestablished.** Siegler (1987) validated per-trial verbal reports convergently against chronometric data. Ericsson & Simon's protocol-analysis framework **[V-meta:** *Protocol Analysis: Verbal Reports as Data*, rev. ed., MIT Press, 1993**]** and Chi's coding methodology **[V-meta:** Chi, M. T. H. (1997), *Quantifying Qualitative Analyses of Verbal Data: A Practical Guide*, JLS **6**(3), 271–315**]** govern when verbal reports are trustworthy — notably, concurrent reports are more reliable than retrospective ones. **Nobody has re-established these validity conditions for LLM-elicited, dialogue-embedded, retrospective explanations.** An LLM asking "how did you get that?" mid-tutoring is neither a think-aloud protocol nor an immediately-post-trial report. If KT-tutor's whole premise is that the explanation is informative, the validity question is load-bearing and currently unanswered. Chi (1997) also gives you your annotation methodology if you build a corpus.

---

## 4. Does a public labeled corpus of student verbal explanations exist?

**Yes for written explanations labeled with misconceptions. No for spoken/dialogic explanations labeled with strategy.** Details below — this section should drive a scoping conversation.

### 4.1 The direct hit: MAP — Charting Student Math Misunderstandings (Kaggle, 2025)

**[V-meta]** Kaggle competition `map-charting-student-math-misunderstandings`, hosted by **Vanderbilt University**, **The Learning Agency**, and Kaggle, using **Eedi** Diagnostic Question data.

Verified facts:

- **Fields** (confirmed from a competitor's published solution repo): `QuestionId`, `QuestionText`, `MC_Answer`, **`StudentExplanation`**, `Category`, `Misconception`. Labels are commonly recombined as `Category + ":" + Misconception`.
- **Content:** students answered Eedi multiple-choice diagnostic questions (one key, three distractors) and **optionally wrote a free-text explanation of their reasoning**. Grades **4–8**.
- **Taxonomy:** developed by content experts at **Vanderbilt**, distinguishing correct explanations, procedural errors, conceptual errors, and specific misconception types. `Category` uses `True_*` / `False_*` prefixes (matching correctness of the MC answer); a `Neither` category captures vague or irrelevant reasoning.
- **Size: ~36,695 samples.** **[U — this figure comes from a paper that uses the dataset (arXiv:2605.14752), not from the Kaggle data page, which I could not read directly. Verify before quoting.]**
- **Competition scale:** 1,850+ teams, ~40,000 submissions; top MAP@3 above **0.94**. **[U]**
- **Availability:** publicly released. The Learning Agency describes it as "a meaningful contribution to open data." **[V-meta]** **Confirm the exact license and any competition-rules restriction on downstream use before building on it — I could not read the Kaggle rules page.**

**Why this reopens scoping.** This is a large, public, expert-labeled corpus of *student free-text explanations of their own reasoning*, tagged with the conception behind them. It removes the "no such data exists" premise from any argument that KT-tutor must collect its own corpus, and it provides a ready-made evaluation set. It also raises the bar: if top solutions already hit MAP@3 > 0.94 on misconception-from-explanation classification, a demo that only shows "an LLM can read an explanation and name the conception" is showing something already demonstrated by ~1,850 Kaggle teams.

**What it does *not* do — and these are your remaining openings:**

- The explanations are **short, written, one-shot, and tied to a fixed MCQ**. Not multi-turn dialogue, not spoken, not elicited adaptively by a tutor.
- Labels are **misconceptions**, not **strategies**. Correct answers get `Correct:NA` — **the strategy behind a correct answer is not labeled.** Open problem #4 stands untouched.
- It is **cross-sectional, not longitudinal**. One row per response; no student trajectory, no tracing. Open problem #3 stands untouched.
- Modeling is **single-turn classification into a fixed taxonomy** — exactly the design Smith/diSessa/Roschelle argue against. Open problem #2 stands untouched.

### 4.2 The near-miss datasets, and precisely why each misses

| Dataset | Has student explanations? | Labeled with conception/strategy? | Verdict |
|---|---|---|---|
| **MAP / Eedi (2025)** | **Yes — free text** | **Yes — misconception taxonomy** | **The one that qualifies** |
| **Eedi — Mining Misconceptions (Kaggle 2024)** **[V-meta]** | No — distractor selection only | Yes — 2.5k+ expert misconceptions over 1,857 K-12 questions | Distractors, not language. This is DiVERT's data lineage. |
| **MathDial** **[V-meta]** — Macina et al., Findings of EMNLP 2023, pp. 5602–5621; CC BY-SA 4.0; ~2,848 dialogues on GSM8k problems | Yes — dialogue turns | **No.** Annotated for **teacher moves**, plus problem difficulty, careless-error flags, confusion severity, engagement, guidance. No student-conception labels. Also: the "student" is an **LLM prompted to exhibit common errors**, not a real student. | Right modality, wrong labels, synthetic student |
| **CoMTA** **[U]** — Miller & DiCerbo 2024, real Khan Academy tutoring dialogues; 188 dialogues | Yes — real student dialogue | No conception labels natively; Scarlatos et al. added **LLM-generated** KC + binary correctness labels for 623 turn pairs | Real and dialogic, but tiny, and the labels are the ones you're critiquing |
| **NCTE Transcripts** **[V-meta]** — Demszky & Hill; 1,660 4th/5th-grade math lessons, 317 teachers, 2010–2013; github.com/ddemszky/classroom-transcript-analysis | Yes — real spoken classroom talk | **No.** Turn-level **dialogic discourse move** annotations, observation scores, demographics, test scores. Discourse form, not conceptual content. | Closest to true *verbal* data; wrong label type entirely |
| **TalkMoves** **[V-meta]** — Suresh et al., LREC 2022, arXiv:2204.09652; 567 human-annotated K-12 math lesson transcripts | Yes — spoken, transcribed | **No.** Sentence-level **accountable-talk discursive moves** (10 categories) + Switchboard dialogue acts. | Same miss as NCTE |
| **PSLC DataShop / LearnLab** **[V-meta]** — 1,466 datasets, 705k+ student-hours, 358k students; free account required | Some — e.g. "Joint Explanation — Electric Fields — Pitt — Spring 2007," and the Hausmann self-explanation studies; the IWT English Articles self-explanation studies | **Unverified.** Self-explanation *text* is present in several studies, but whether any carries **conception/strategy codes** could not be confirmed without an account. **[U]** | **Worth 30 minutes with a free account.** The Hausmann/VanLehn self-explanation corpora are the most likely place a strategy-coded explanation set is hiding. |
| **Daheim et al. 2024** **[V-meta]** — arXiv:2407.09136, github.com/eth-lre/verify-then-generate, CC BY 4.0 | Solution steps, not explanations of reasoning | **Error *location* only** — 1K stepwise math reasoning chains with the **first error step** annotated by teachers. No error *type* or conception label. | "Where," not "which conception" |
| **MalruleLib** **[U]** — Chen, Liu & Sonkar, arXiv:2601.03217 (Jan 2026), github.com/luffycodes/malrulelib | No — synthetic step traces | Yes — executable "malrules" paired with step traces | Model-generated, not student language. Possibly useful as a **label ontology**. |

### 4.3 Bottom line on data

- If the project scopes to **misconception identification from written explanation**: the data exists, it is large, it is public, and the task is largely solved by existing Kaggle solutions. Rescope or differentiate.
- If the project scopes to **strategy behind correct answers**, or **conception tracked across dialogue turns**, or **spoken explanation**: no public labeled corpus exists, and you would be building one. MAP could seed the label ontology; NCTE/TalkMoves could supply the spoken modality but would need new annotation; CoMTA supplies real dialogue at very small scale.
- **Check PSLC DataShop before concluding.** It is the single most likely place a pre-existing strategy-coded explanation corpus is sitting, and it requires only a free account.

---

## 5. What the lead list misses

### 5.1 Option Tracing (Ghosh, Raspat & Lan, AIED 2021) — the most conspicuous omission

**[V-meta]** Ghosh, A., Raspat, J., & Lan, A. S. (2021). *Option Tracing: Beyond Correctness Analysis in Knowledge Tracing.* AIED 2021. Code: github.com/arghosh/OptionTracing. Follow-ups: *No Task Left Behind: Multi-Task Learning of Knowledge Tracing and Option Tracing* (AAAI 2022); *Integrating Option Tracing into Knowledge Tracing* (Journal of Learning Analytics).

Extends KT from predicting *correctness* to predicting **which exact option** a student selects, evaluated on two large-scale response datasets. **This is Andrew Lan's own lab, four years before the paper you're departing from, doing "model which wrong thing, not whether wrong" inside the KT framework.** Any critique of Scarlatos, Baker & Lan (2025) that presents "beyond correctness" as a novel move without citing Option Tracing will be dismissed by anyone who knows the lab. Cite it, and position KT-tutor as: Option Tracing did this over **discrete option spaces**; KT-tutor proposes it over **open language**.

### 5.2 Language Bottleneck Models — the closest live competitor

**[V-meta]** Berthon, A., & van der Schaar, M. *Language Bottleneck Models for Qualitative Knowledge State Modeling.* arXiv:2506.16982 (submitted June 2025, revised February 2026).

An **encoder LLM** writes a **textual summary of the student's knowledge state**; a **decoder LLM** predicts future performance from that text alone. The paper's explicit motivation is that a natural-language state can express "nuanced insights — such as misconceptions — that CD and KT models cannot capture." Claims competitive accuracy with better sample efficiency and interpretability, on synthetic and real data.

This is the closest published thing to "replace the mastery scalar with something that says *what the student thinks*." **KT-tutor must state how it differs.** The most likely differentiators: LBM's input is still interaction/response data rather than the student's own explanation; and the bottleneck is a free-form summary rather than a structured conception attribution. Verify both by reading the paper before asserting them.

### 5.3 AutoTutor / LSA-based explanation analysis

**[V-meta, from Makatchev & VanLehn's reference list]** Graesser, A. C., Wiemer-Hastings, P., Wiemer-Hastings, K., Harter, D., Person, N., & the TRG (2000). *Using latent semantic analysis to evaluate the contributions of students in AutoTutor.* Interactive Learning Environments, **8**, 129–148.

Cited by Scarlatos et al. as inferring "whether a student response was correct, and when incorrect, whether misconceptions were involved." The largest-deployment pre-LLM system that read student explanations. Belongs in the related-work arc; its LSA-based approach is also the clearest illustration of why this was hard before contextual language models.

### 5.4 Protocol-analysis methodology — needed to defend the premise

**[V-meta]** Ericsson, K. A., & Simon, H. A. (1993). *Protocol Analysis: Verbal Reports as Data* (revised ed.). MIT Press.
**[V-meta]** Chi, M. T. H. (1997). *Quantifying Qualitative Analyses of Verbal Data: A Practical Guide.* Journal of the Learning Sciences, **6**(3), 271–315.
**[V-meta, from Makatchev's reference list]** Chi, M. T. H., de Leeuw, N., Chiu, M.-H., & LaVancher, C. (1994). *Eliciting self-explanations improves understanding.* Cognitive Science, **18**, 439–477.

The first two establish when verbal reports are valid data and how to code them reliably — the methodological foundation the project is standing on, and the source of your annotation protocol if you build a corpus. The third is the reason to elicit explanations *even if* the model learns nothing: self-explanation improves learning independently of any diagnostic use. That is a strong secondary argument for the design and it costs nothing to make.

### 5.5 Other work worth a look

- **[U]** Ross, A., & Andreas, J. (2025). *Learning to Make MISTAKEs: Modeling Incorrect Student Thinking And Key Errors.* arXiv:2510.11502. Generates synthetic reasoning errors via cycle consistency between wrong answers and misconceptions; trains student simulation, misconception classification, and distractor generation. Appears to be answer-level rather than explanation-level — verify.
- **[U]** Rafferty, Brunskill, Griffiths & Shafto's line on **inferring learner knowledge and misconceptions from actions via POMDP/Bayesian inverse planning** — relevant precedent for treating diagnosis as inference over latent generative models of the student. Not verified in this pass; worth a search.
- **[U]** Feng, Scarlatos, Lan et al. have several 2024–2025 papers on distractor generation and error modeling (e.g. arXiv:2505.01903 on consistent distractor generation). Sweep Lan's publication page (people.umass.edu/~andrewlan/publications.html) directly — several in-lab precedents will surface.
- **[V-meta]** Miller & DiCerbo (2024) for CoMTA's own paper — I did not locate it in this pass; find the primary citation before relying on CoMTA claims.

---

## 6. Recommendations for the project

1. **Rewrite the Siegler argument.** Averaging is over *strategies*, not children; per-child modeling does not fix it; the aggregate curve *did* match individual children, which is what made it dangerous. And make the KT connection at the level of the **scalar `P(z_jk)` being a strategy mixture**, not at the level of the mean over `k`.
2. **Move the mean-over-KCs critique to the CDM literature.** DINA's conjunctivity and G-DINA's spectrum give you a formal, quantitative objection: an equal-weight compensatory mean predicts p = 0.75 for a student missing one of four genuinely required KCs.
3. **Fix two citations before they propagate.** Makatchev & VanLehn 2007 evaluates **fact coverage, not misconception classification** — cite Makatchev, Jordan & VanLehn (2004, JAR) or Makatchev & VanLehn (2005, AIED) for the misconception claim. And Scarlatos et al. **do** cite Makatchev and Why2-Atlas; adjust any "they ignored this lineage" phrasing to "they cite it and then don't build on it."
4. **Resolve the MAP dataset question before writing more scoping docs.** Download it, verify the row count and license, and decide: differentiate (correct-answer strategy, longitudinal tracing, dialogic elicitation) or rescope.
5. **Cite Option Tracing.** Same lab, 2021, "beyond correctness." Not citing it is a credibility hole.
6. **Read Language Bottleneck Models and state the delta.** It is the nearest competitor and it is very recent.
7. **Spend 30 minutes in PSLC DataShop** with a free account, looking specifically at the Hausmann/VanLehn self-explanation studies and "Joint Explanation — Electric Fields — Pitt — Spring 2007," for pre-existing strategy-coded explanation data.
8. **Plant the flag on the unclaimed ground:** *tracking which conception a student holds, over time, across dialogue turns, including for correct answers.* Every other framing has substantial prior art or a public dataset that has already largely solved it.

---

## Appendix: consolidated citations

**Confirmed leads**

- Siegler, R. S. (1987). The perils of averaging data over strategies: An example from children's addition. *Journal of Experimental Psychology: General*, 116(3), 250–264. **[V]**
- Smith, J. P. III, diSessa, A. A., & Roschelle, J. (1994). Misconceptions reconceived: A constructivist analysis of knowledge in transition. *Journal of the Learning Sciences*, 3(2), 115–163. **[V-meta]**
- Fernandez, N., Scarlatos, A., Feng, W., Woodhead, S., & Lan, A. (2024). DiVERT: Distractor generation with variational errors represented as text for math multiple-choice questions. *EMNLP 2024*. arXiv:2406.19356; ACL Anthology 2024.emnlp-main.512. **[V-meta]**
- Makatchev, M., & VanLehn, K. (2007). Combining Bayesian networks and formal reasoning for semantic classification of student utterances. *AIED 2007*, 307–314. **[V]**
- VanLehn, K., Jordan, P. W., Rosé, C. P., Bhembe, D., et al. (2002). The architecture of Why2-Atlas: A coach for qualitative physics essay writing. *ITS 2002*, LNCS 2363, 158–167. **[V-meta]**
- Pardos, Z. A., Farrar, S., Kolb, J., Peh, G. X., & Lee, J. H. (2018). Distributed representation of misconceptions. *ICLS 2018*, 1791–1798. **[V-meta]**
- Brown, J. S., & Burton, R. R. (1978). Diagnostic models for procedural bugs in basic mathematical skills. *Cognitive Science*, 2(2), 155–192. **[V-meta]**
- Brown, J. S., & VanLehn, K. (1980). Repair theory: A generative theory of bugs in procedural skills. *Cognitive Science*, 4(4), 379–426. **[V-meta]**
- Ohlsson, S. (1994). Constraint-based student modeling. In Greer & McCalla (eds.), *Student Modelling*, Springer, 167–189. **[V-meta]**
- Ohlsson, S. (1996). Learning from performance errors. *Psychological Review*, 103(2), 241–262. **[V-meta]**
- Junker, B. W., & Sijtsma, K. (2001). Cognitive assessment models with few assumptions… *Applied Psychological Measurement*, 25(3), 258–272. **[V-meta]**
- de la Torre, J. (2009). A cognitive diagnosis model for cognitively-based multiple-choice options. *Applied Psychological Measurement*, 33(3), 163–183. **[V-meta]**
- de la Torre, J. (2011). The generalized DINA model framework. *Psychometrika*, 76(2), 179–199. **[V-meta]**

**Additions**

- Ghosh, A., Raspat, J., & Lan, A. S. (2021). Option tracing: Beyond correctness analysis in knowledge tracing. *AIED 2021*. **[V-meta]**
- Berthon, A., & van der Schaar, M. (2025/2026). Language bottleneck models for qualitative knowledge state modeling. arXiv:2506.16982. **[V-meta]**
- Graesser, A. C., et al. (2000). Using latent semantic analysis to evaluate the contributions of students in AutoTutor. *Interactive Learning Environments*, 8, 129–148. **[V-meta]**
- Makatchev, M., Jordan, P. W., & VanLehn, K. (2004). Abductive theorem proving for analyzing student explanations to guide feedback in intelligent tutoring systems. *Journal of Automated Reasoning*, 32, 187–226. **[V-meta]**
- Makatchev, M., & VanLehn, K. (2005). Analyzing completeness and correctness of utterances using an ATMS. *AIED 2005*, IOS Press. **[V-meta]**
- Ericsson, K. A., & Simon, H. A. (1993). *Protocol Analysis: Verbal Reports as Data* (rev. ed.). MIT Press. **[V-meta]**
- Chi, M. T. H. (1997). Quantifying qualitative analyses of verbal data: A practical guide. *Journal of the Learning Sciences*, 6(3), 271–315. **[V-meta]**
- Chi, M. T. H., de Leeuw, N., Chiu, M.-H., & LaVancher, C. (1994). Eliciting self-explanations improves understanding. *Cognitive Science*, 18, 439–477. **[V-meta]**
- Daheim, N., Macina, J., Kapur, M., Gurevych, I., & Sachan, M. (2024). Stepwise verification and remediation of student reasoning errors with large language model tutors. arXiv:2407.09136. **[V-meta]**
- Macina, J., Daheim, N., et al. (2023). MathDial: A dialogue tutoring dataset with rich pedagogical properties grounded in math reasoning problems. *Findings of EMNLP 2023*, 5602–5621. **[V-meta]**
- Suresh, A., Jacobs, J., et al. (2022). The TalkMoves dataset: K-12 mathematics lesson transcripts annotated for teacher and student discursive moves. *LREC 2022*. arXiv:2204.09652. **[V-meta]**
- Demszky, D., & Hill, H. (2023). The NCTE transcripts: A dataset of elementary math classroom transcripts. arXiv:2211.11772. **[V-meta]**
- Ross, A., & Andreas, J. (2025). Learning to make MISTAKEs: Modeling incorrect student thinking and key errors. arXiv:2510.11502. **[U]**
- Chen, X., Liu, N., & Sonkar, S. (2026). MalruleLib: Large-scale executable misconception reasoning with step traces. arXiv:2601.03217. **[U]**

**Datasets**

- MAP — Charting Student Math Misunderstandings. Kaggle, 2025. Vanderbilt / The Learning Agency / Eedi. `kaggle.com/competitions/map-charting-student-math-misunderstandings` **[V-meta]**
- Eedi — Mining Misconceptions in Mathematics. Kaggle, 2024. `kaggle.com/competitions/eedi-mining-misconceptions-in-mathematics` **[V-meta]**
- PSLC DataShop / LearnLab. `pslcdatashop.web.cmu.edu` **[V-meta]**
- NCTE Transcripts. `github.com/ddemszky/classroom-transcript-analysis` **[V-meta]**
- MathDial. `github.com/eth-nlped/mathdial`, CC BY-SA 4.0 **[V-meta]**
