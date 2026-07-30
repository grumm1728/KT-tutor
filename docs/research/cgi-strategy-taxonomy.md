# CGI strategy vocabulary for multi-digit addition within 100

Research note for issue #3. Written 2026-07-30.

**Bottom line up front.** The taxonomy is real but **smaller, messier, and less settled than the
ticket assumes**. Three findings should change how this project names things:

1. **There is no single canonical name set.** The two foundational sources use *different names for
   the same three strategies*, and the CGI trade book uses a third set. Any vocabulary we ship is a
   choice among competing labels, not a transcription of an established standard. Pick one, cite it,
   and record the aliases.
2. **The developmental ordering the ticket assumes does not exist in the evidence.** Carpenter et al.
   (1998) explicitly tested whether incrementing precedes splitting and found **no sequence**. This
   is a direct, quotable negative result. A UI that presents these as ordered levels would be
   misrepresenting the primary source.
3. **The make-ten step nests inside *incrementing*, not inside the split.** The ticket's guess (#3)
   is backwards relative to how both primary sources lay it out. Describing the split for 38+25 as
   containing a make-ten step would be our invention.

Also: **one of the ticket's three leads contains no strategy taxonomy at all** (Carpenter et al.
1989), and **one major body of work is missing from the leads entirely** (Beishuizen's N10/1010).

---

## Sources and what I could actually verify

| Source | Access | Confidence |
|---|---|---|
| Fuson, Wearne, Hiebert, Murray, Human, Olivier, Carpenter & Fennema (1997), *JRME* 28(2), 130–162 | **Full text read** (author-hosted PDF) | High — quoted directly |
| Carpenter, Franke, Jacobs, Fennema & Empson (1998), *JRME* 29(1), 3–20 | **Full text read** (JSTOR scan) | High — quoted directly |
| Carpenter, Fennema, Peterson, Chiang & Loef (1989), *AERJ* 26(4), 499–531 | Abstract/summaries only | High enough for a negative finding |
| Carpenter et al. (1994), *Teaching Mathematics for Learning with Understanding in the Primary Grades*, ERIC ED373971 | **Full text read** | High — AERA precursor to Fuson 1997, same author team |
| Fuson & Kwon (1992), *JRME* 23(2), 148–165 | **Full text read** (author-hosted PDF) | High |
| **Carpenter et al., *Children's Mathematics* (Heinemann, 1999 / 2015)** | **NOT read — book, not online** | **Medium — via three independent secondary sources that cite it** |
| Beishuizen (1993), *JRME* 24(4), 294–323 | Abstract/summaries only (paywalled) | Medium |

**The single biggest gap: I could not read *Children's Mathematics* itself.** It is the ticket's
designated primary source and it is a print book. Everything below about the book's own wording is
inferred from three independent secondary sources that cite it, which agree with each other. Someone
with the book should verify Ch. 6–7 before we freeze UI labels. See "What to verify in the book."

---

## 1. Canonical strategy names — three competing sets

### 1a. Fuson et al. (1997) — the original analysis, **four** categories

This is the source Carpenter et al. (1998) credits as "a comprehensive analysis of invented
strategies." Verbatim from p. 149:

> We have classified the methods children used into four kinds: methods that begin with one number
> and move up or down the sequence by tens and by ones, decompose-tens-and-ones methods in which the
> tens and the ones are added or subtracted separately from each other, mixed methods in which the
> tens are added or subtracted and then a sequence number is made with the original ones and a
> sequence method is used to add or subtract the other ones, and methods in which both numbers are
> changed to make easier numbers.

As table headings in their Table 1 (p. 149–150), the four names are:

| Fuson et al. (1997) name | Gloss |
|---|---|
| **Begin-With-One-Number Methods** | Begin with one number and move up or down by tens and ones |
| **Decompose-Tens-and-Ones Methods** | Add or subtract tens and ones separately, then regroup |
| **Mixed Methods** | Add/subtract tens, make a sequence number with the *original* ones, then add/subtract the other ones |
| **Change-Both-Numbers Methods** | Change both numbers to make easier numbers |

Note Fuson's Table 1 uses **38 + 26** as its worked example — near-identical to the ticket's 38 + 25.

### 1b. Carpenter et al. (1998) — **renamed and collapsed to three**

Carpenter et al. (1998, p. 4) present the same distinctions under *different names* and drop "mixed."
Verbatim, with their own protocols for 38 + 26:

> *Sequential:* "Thirty and twenty is fifty, and the eight makes fifty-eight. Then six more is
> sixty-four."
> *Combining units separately:* "Thirty and twenty is fifty, and eight and six is fourteen. The ten
> from the fourteen makes sixty, so it's sixty-four."
> *Compensating:* "That's like forty and twenty-four, and that's sixty-four."

Their actual coding categories (p. 9), verbatim:

> *incorrect, modeling or counting by ones, modeling with tens materials, sequential invented
> strategies, combining-units invented strategies, compensating invented strategies, other invented
> strategies, algorithms,* and *buggy algorithms*.

So the primary-source phrase for "direct modeling with tens" is **"modeling with tens materials"**,
and the bottom category is **"modeling or counting by ones"**.

### 1c. *Children's Mathematics* (the book) — a third set, and the one the ticket recalled

The ticket's recall (*incrementing*, *combining tens and ones*, *compensating*) is **the book's
vocabulary, not the journals'**. I could not read the book, but three independent secondary sources
citing it agree:

- A CGI professional-development handout citing **Carpenter et al. (2015), 2nd ed.** lists
  "Decade Counting/**Incrementing**", "**Compensating**", "**Combining 10s and 1s**" with worked
  examples for 58 + 47.
- A blog citing **Carpenter (1999), p. 70** lists "Counting single units, Direct modeling with tens,
  **Incrementing**, **Combining tens and ones**, **Compensating**." *(This source garbles the
  definitions — it attaches the definition of combining-tens-and-ones to incrementing — so treat its
  page cite as a lead, not as verified content.)*
- Brickwedde (Project for Elementary Mathematics, 2005/2012), citing Carpenter et al. 1998/1999,
  uses "**Tens & Ones Strategy**", "**Incremental Strategy**", "**Compensation Strategy**."

**A documented naming migration.** Brickwedde's footnote 1 states that the original researchers were
shifting the label for the split strategy to **"Combining Like Units"**:

> The original researchers (Carpenter, et al., 1999) are shifting to the label **Combining Like
> Units** in order to capture the commutative property aspects of arriving at the same answer whether
> or not one starts adding the tens first then the ones or adding the ones first then the tens.

I could **not** independently confirm this relabel in the book itself. Flagging it because if true it
means even the book's own preferred term moved.

### 1d. Synthesis — the alias table

| Concept | Fuson 1997 | Carpenter 1998 | *Children's Mathematics* | Ticket's recall |
|---|---|---|---|---|
| Count/add on by tens then ones | Begin-with-one-number | **Sequential** | **Incrementing** | "counting by tens and ones / incrementing" ✅ |
| Add tens, add ones, combine | Decompose-tens-and-ones | **Combining units separately** | **Combining tens and ones** (poss. "Combining Like Units") | "combining tens and ones / split" ✅ |
| Adjust numbers to make them easy | Change-both-numbers | **Compensating** | **Compensating** | "compensating" ✅ |
| Tens first, then re-enter via original ones | **Mixed methods** | *(absent)* | *(absent)* | *(absent)* ❌ |
| Model with base-ten materials | *(unitary/Level 1 methods)* | **Modeling with tens materials** | "Direct modeling with tens" | "direct modeling with tens and ones" ✅ |
| Standard algorithm | *(traditional algorithm)* | **Algorithms** / **buggy algorithms** | Standard algorithm | "the standard algorithm" ✅ |

**Verdict on the ticket's recall:** substantially correct as a rendering of *the book's* vocabulary.
The "split" gloss is the ticket-writer's, not CGI's — the word "split" is Beishuizen-adjacent
(see §5), not CGI. And the recall is **missing Fuson's "mixed methods"**, which matters because that
is where the best-documented error lives (§4).

---

## 2. Developmental ordering — the evidence is weaker than expected

There are two orderings in play, and they have very different evidential status.

### The coarse ordering is well supported
Modeling/counting by ones → modeling with tens materials → abstract invented strategies →
standard algorithm. This is asserted consistently across sources. Carpenter et al. (1998, p. 4):

> Initially, children may use individual counters to model addition or subtraction operations. As
> they gain understanding of base-ten numbers, they begin to use various materials representing tens
> rather than individual counters. Over time, many children construct procedures for adding and
> subtracting multidigit numbers without using physical materials of any kind.

Table 1 (p. 10) gives cumulative percentages using an invented **addition** strategy: Grade 1 = 29%,
Grade 2 fall = 65%, Grade 2 spring = 74%, Grade 3 fall = 82%, Grade 3 spring = 88%.

### The fine ordering among invented strategies **does not exist**
This is the load-bearing finding. Carpenter et al. (1998, p. 16), verbatim:

> Our data suggest that there is no explicit sequence in which invented strategies develop for
> addition problems, and most children tended to use them somewhat interchangeably. Of the 72
> students who used invented addition strategies, 10 students used only sequential strategies, 15
> used only combining-units-separately strategies, and 47 students used both types of strategies at
> one time or another. Fourteen of these 47 students used a sequential strategy before they combined
> units separately, 22 combined units separately before they used a sequential strategy, and 11 used
> both strategies for the first time during the same interview.

14 vs. 22 vs. 11 is as close to "no order" as data gets. The authors also disclaim generality
(p. 18):

> We are not proposing that this study maps out an invariant pattern of development.

Two further ordering facts worth keeping:

- **Compensating is rare and largely elicited, not spontaneous.** Only 10 students used compensating
  for subtraction spontaneously; when *shown* a compensating strategy and asked to reuse it, 40 did
  (p. 16). So compensating's position in any progression reflects prompting as much as development.
- **Subtraction lags addition substantially.** 32% of students never used an invented subtraction
  strategy in any interview (p. 10).

**Implication for the project:** if the UI presents these as a ladder, it asserts something the
primary source specifically looked for and failed to find. A *repertoire* or *unordered set* model is
what the evidence supports. Direct modeling → invented → algorithm is safe; incrementing → split is
not.

---

## 3. Nesting — the make-ten step lives in incrementing, not in the split

**Direct answer to the ticket's question: describing the split for 38 + 25 as containing a make-ten
step would be our invention.** Both primary sources place make-ten in the *other* family.

### Evidence A — Fuson et al. (1997) Table 1 names it as a row inside begin-with-one-number
Under **Begin-With-One-Number Methods**, for 38 + 26, there is a named method:

> **Count on/add on to make a ten**, count on/add on tens, then rest of ones
> 38, 39, 40, 50, 60, 61, 62, 63, 64
> 38 + 2 → 40 + 20 → 60 + 4 → 64

The make-ten step (38 + 2 → 40) is *the defining move of that row*, and the row sits in the
incrementing family.

### Evidence B — the split protocols contain no make-ten step
Carpenter et al.'s (1998) combining-units-separately protocol handles the ones as a **retrieved
fact**, not a decomposition:

> "Thirty and twenty is fifty, and eight and six is fourteen. The ten from the fourteen makes sixty,
> so it's sixty-four."

"eight and six is fourteen" is stated flat. The only decomposition afterwards is *regrouping the
result* (pulling the ten out of 14) — which is a different operation from make-ten. Same in the
2015-sourced PD handout: "58 + 47 → 50 + 40 = 90, 8 + 7 = 15, 90 + 15 = 105." No make-ten.

Fuson's own commentary confirms regrouping is what the split requires (p. 152–153):

> The methods in which the tens and ones are decomposed and then operated on separately must deal
> explicitly with regrouping: in adding, a unit of ten must be made from ten ones... In the
> begin-with-one-number methods, this could be dealt with implicitly by counting up or down (or
> adding or subtracting) over a ten.

That is the cleanest statement of the contrast: **the split makes a ten *out of the answer*; the
increment goes *over* a ten *during* the move.**

### Evidence C — the one place nesting *is* asserted is Fuson's STST instruction, and it is general
Carpenter et al. (1994, ERIC ED373971, p. 7) — and note this is a claim about *instructional design*,
not about observed CGI children:

> STST instruction also supports specific solutions of single-digit addition and subtraction that
> involve grouping by ten. Sums and differences are chunked to make a ten and some ones (e.g. 8 + 5 =
> 8 + 2 + 3 = 10 + 3). ... they are easily integrated into children's solutions of multidigit
> addition and subtraction problems.

Uses the ticket's exact 8 + 5. But "easily integrated into multidigit solutions" is generic — it does
not locate make-ten inside the split specifically.

### Evidence D — a *secondary* source does describe make-ten inside the split
Brickwedde describes advanced users of the Tens & Ones strategy decomposing the ones:

> In adding on the ones, advanced users of this strategy decompose the remaining single digit number
> into a combination to make a ten (4 = 3 + 1; 7 + 3 = 10), then join the unused portion to the newly
> made decade number.

So the framing is **not unheard of** — but it is one practitioner author's elaboration, not the
primary taxonomy, and note that what he describes is arguably a *hybrid* drifting toward Fuson's
"mixed methods."

### The single-digit strategies that do nest (Fuson 1997, p. 145)
The three-level single-digit progression:

- **Level 1** — direct modeling with objects; count all / take away.
- **Level 2** — counting on / counting up / counting back, with keeping-track.
- **Level 3** — **"derived fact"** solutions; recomposing a triplet into a known triplet.

Critically, on which derived facts US children actually use:

> These "derived fact" solutions commonly use doubles (a + a) in the United States. For example,
> 7 + 6 = 6 + 6 + 1 = 12 + 1 = 13. In Asian countries children learn to recompose numbers into
> ten-structured triplets. ... Such ten-structured methods ... are used much less frequently in the
> United States, though some children do use them, especially for an addend of 9.

**This is a finding the project should absorb: in this literature, doubles/near-doubles is the
*typical* US derived-fact route and make-ten is the *atypical* one.** The ticket lists them as
co-equal. Also note "known facts" is deliberately *not* a level: "use of known facts occurs at all
three levels" (p. 145).

Fuson & Kwon (1992) name the make-ten variants for the Korean children who do use them:
**"up-over-ten"** (addition), **"down-over-ten"** and **"subtract-from-ten"** (subtraction). Those are
the most precise names available for these moves. For 36 Korean first-graders at midyear, up-over-ten
accounted for 39% (mental) + 6% (finger) of solutions to sums over ten.

---

## 4. Errors — the specific one has no established name

### Direct answer to the ticket's error question
**I found no established name in this literature for "correctly states make-ten, then re-adds the
piece broken off instead of the remainder" (8 + 5 → broke off 2 → 10 → +2 → 12).** I searched the CGI
corpus, the Fuson corpus including the two papers most likely to contain it (Fuson et al. 1997's
per-method error notes and Fuson & Kwon 1992's error analysis of ten-structured methods), and the
general arithmetic-error literature. Fuson & Kwon report error *rates* for up-over-ten but no error
*typology*.

**If we need a label, we are coining it.** That is a legitimate thing to do, but the docs should say
so rather than implying provenance. I would not present it in the UI as a term of art.

### On the competing explanations the ticket floats
- **Doubles-fact interference** — I found no source proposing this for this error. It is also weak on
  the arithmetic: 12 is not a doubles or near-doubles answer for 8 + 5 in any natural way. Treat as
  unsupported.
- **Decomposition-component confusion** — no named construct, but this is the explanation the
  literature's *structure* points at. See the close analogue below.

### The closest documented analogue — and it is genuinely close
Fuson et al. (1997) footnote b to Table 1, on **mixed methods**:

> Forgetting to add back in the original ones (the 4 from 64 or the 8 in 38) or subtracting them are
> (in a subtraction problem) frequent errors. The ones from the 26 sometimes are subtracted first and
> then the ones from the 64 are added back in; forgetting to add the 4 or subtracting it are also
> frequent errors.

And their mechanism account (p. 152):

> The step of making the sequence number by adding in the original ones comes in the middle of the
> method, so it is especially confusing.

This is structurally the same failure the ticket describes — **the child re-enters the wrong
component of a decomposition mid-procedure** — attributed to a mid-procedure subgoal being
confusable. It is documented at the two-digit level for mixed methods, not at the single-digit
make-ten level. This is the nearest thing to a citation we have, and it also happens to be the reason
Fuson's dropped fourth category ("mixed methods") is worth keeping in our vocabulary.

### Other documented error patterns worth encoding
From Fuson et al. (1997):

- **Split-subtraction smaller-from-larger** (p. 153–154): "The common error is to switch the order of
  the ones subtraction because it 'doesn't make sense' the other way ... and find 4 − 8 or 8 − 4 to
  equal 4, so 30 + 4 = 34 is given as the answer instead of 30 + −4 = 26." Table 1 marks "4 − 6 = 2
  so ... 42 is a typical error."
- **Compensation direction error** (p. 153, and Table 1 footnote d): children "confus[e] what must be
  kept constant in addition (the total) and in subtraction (the difference)" — they subtract from
  the second number as in addition. Brickwedde independently describes the same: children add 3 to 37
  then *also* subtract 3 from the 13.
- **Over-generalizing addition to subtraction** (p. 152): "because 'in adding you added everything,
  so in subtraction you subtract everything'." Notably, PCMP found this error "increased considerably
  if teachers gave only addition problems before they gave any subtraction problems, even for as
  short a period as 2 weeks."
- **Counting-down errors** (p. 152): e.g. "43, 42, 41, 40, 30, 39, 38".
- **Concatenated single-digit conception** (p. 137ff) — treating a multi-digit numeral as unrelated
  digits. Fuson names this as a distinct erroneous *conception*, not a slip, and calls it "error
  prone."

From Carpenter et al. (1998), on **buggy algorithms** (p. 14–15, Table 4):

- Addition bugs 30–38% across groups; subtraction bugs far more frequent and persistent. No student
  used a buggy addition algorithm in more than one interview; over 30% did for subtraction.
- The finding worth quoting: bugs and understanding coexist. "In many cases bugs appeared in the same
  interview in which students used invented strategies on similar problems." Also (p. 18): the bugs
  of students with reasonable understanding "were not robust and often could be eliminated by simple
  counter suggestion."

**Implication for a knowledge-tracing demo:** that last point is directly relevant. The primary
source says a bug in one turn does not imply absence of understanding, and is often erased by a
single prompt. A KT visualization that drives mastery sharply down on one buggy turn is asserting
something this literature contradicts.

---

## 5. What the leads miss

### 5a. Carpenter et al. (1989) contains no strategy taxonomy
The AERJ paper is a randomized teacher-PD experiment: 20 experimental / 20 control first-grade
teachers, month-long workshop, outcomes about teacher practice and student achievement. Its
research-based content is the **word-problem type** taxonomy (Join/Separate/Part-Part-Whole/Compare
× unknown position) and single-digit strategies for Grade 1 — **not** multi-digit strategies, which
were not yet analyzed in 1989. Drop it from this ticket's source list. It is the right citation for
"CGI works," the wrong one for "what the strategies are called."

### 5b. Beishuizen's N10 / 1010 — a whole parallel vocabulary the ticket omits
Beishuizen (1993), *JRME* 24(4), 294–323, "Mental strategies and materials or models for addition and
subtraction up to 100 in Dutch second grades," established the notation that dominates the European
literature on exactly this content:

- **N10** — keep the first number whole, jump by tens then ones (= incrementing / sequential /
  begin-with-one-number). Also called *jumping* or *stringing*.
- **1010** — split both numbers into tens and ones, operate separately (= combining tens and ones /
  split). Also called *splitting*. **This is where the word "split" in the ticket comes from — it is
  Dutch-tradition vocabulary, not CGI vocabulary.**
- **N10C** — the compensation variant.
- **u-N10 / u-1010** — indirect/unknown-addend variants.

Two reasons this matters for us:

1. If the project ever ingests strategy labels from outside sources, or from an LLM's own priors,
   **"split" and "jump" will show up** and they are N10/1010 vocabulary. Our alias table should carry
   them.
2. Beishuizen's study "compared the strategies N10 and 1010 on procedural effectiveness and **error
   types**." That is the study most likely to contain a formal error typology for these strategies —
   the thing the ticket's error question is really asking for. **I could not access the full text
   (paywalled).** If the error question matters, this is the next thing to read.

Caveat: I verified N10/1010 from abstracts and secondary summaries only, not the paper itself.

### 5c. Other gaps
- **Fuson's "mixed methods" has no CGI equivalent**, and it is a real thing children do (Brickwedde
  independently documents it as a Tens-&-Ones variant). If our data vocabulary only has three
  buckets, mixed-method responses will be mis-coded — most likely as split, which would be wrong.
- **Left-to-right is the norm, not an exception.** Fuson et al. (1997, p. 150): "children's invented
  methods almost always begin at the left with the largest multiunits." Worth encoding as an
  expectation, since it is the opposite of the standard algorithm.
- **Regrouping placement is a real dimension of variation**, not noise. Fuson's Table 1 splits
  decompose-tens-and-ones into three sub-families by *when* regrouping happens: "Add or Subtract
  Everywhere, Then Regroup"; "Regroup, Then Add or Subtract Everywhere"; "Alternate
  Adding/Subtracting and Regrouping."
- **Fuson's conceptual-structure layer is separate from the strategy layer** and the ticket conflates
  nothing here only because it omits it entirely. The five conceptions — **unitary, decade-and-ones,
  sequence-tens-and-ones, separate-tens-and-ones, integrated sequence-separate** — are what a
  strategy *runs on*. Fuson maps them: begin-with-one-number and mixed methods "were typically done
  with a sequence-ten or integrated-tens conceptions, and the separate-tens and change-both-number
  methods used a sequence-tens, separate-tens, or integrated-tens conception" (p. 149). If we ever
  want a latent variable behind observed strategies, this is the literature's version of it.

---

## Recommendation for the project's vocabulary

1. **Adopt the *Children's Mathematics* names** (Direct modeling with tens / Incrementing / Combining
   tens and ones / Compensating / Standard algorithm) — they are the ticket's designated primary
   source, they are what practitioners use, and they are the shortest. But **record the aliases**
   (§1d + §5b) in the data model, because the journals and the European literature use different
   words for the same things and anything we ingest may use either.
2. **Add "mixed" as a fifth category**, or accept known mis-coding.
3. **Do not order incrementing and combining-tens-and-ones.** Model strategies as an unordered
   repertoire. The coarse direct-modeling → invented → algorithm arc is defensible; the fine ordering
   is contradicted by the primary source.
4. **Do not describe the split as containing a make-ten step.** If we want a make-ten sub-step in the
   model, attach it to incrementing, where both primary sources put it.
5. **Do not name the 8+5 error as if it were established.** Coin a label if we need one, and mark it
   as ours. Cite Fuson et al. (1997) footnote b as the analogous documented phenomenon.
6. **Don't over-weight a single buggy turn** in the KT visualization — Carpenter et al. (1998) found
   bugs coexisting with understanding and eliminable by one counter-suggestion.

## What to verify in the book

Someone with *Children's Mathematics* (2nd ed., 2015) should check Ch. 6 "Base-Ten Number Concepts"
and Ch. 7 "Children's Strategies for Solving Multidigit Problems" for:

- The exact strategy labels and whether "Combining Like Units" has replaced "Combining tens and ones."
- Whether the book asserts a developmental *order* among invented strategies (if it does, it is in
  tension with Carpenter et al. 1998 p. 16, and we should note the tension).
- Whether the book shows a make-ten step inside any combining-tens-and-ones example.
- Whether "direct modeling with tens" is the book's phrase (the 1998 paper's phrase is "modeling with
  tens materials").

## Sources

- [Carpenter, Franke, Jacobs, Fennema & Empson (1998), JRME 29(1), 3–20 — full text PDF](https://bpb-us-e1.wpmucdn.com/blog.lrei.org/dist/4/11389/files/2018/11/Carpenter-1aviy2d.pdf)
- [Fuson, Wearne, Hiebert, Murray, Human, Olivier, Carpenter & Fennema (1997), JRME 28(2), 130–162 — full text PDF](https://karenfusonmath.net/wp-content/uploads/2023/06/47-MD-Methods-Conceptual-Structures-JRME-1997.pdf)
- [Fuson & Kwon (1992), JRME 23(2), 148–165 — full text PDF](https://karenfusonmath.net/wp-content/uploads/2023/06/59-Korean-SD-JRME-1992.pdf)
- [Carpenter, Fennema, Fuson, Hiebert, Human, Murray, Olivier & Wearne (1994), ERIC ED373971](https://files.eric.ed.gov/fulltext/ED373971.pdf)
- [Carpenter, Fennema, Peterson, Chiang & Loef (1989), AERJ 26(4), 499–531](https://journals.sagepub.com/doi/10.3102/00028312026004499)
- [Beishuizen (1993), JRME 24(4), 294–323 — abstract](https://pubs.nctm.org/view/journals/jrme/24/4/article-p294.xml)
- [Brickwedde, *Students' Multidigit Addition & Subtraction Strategies*, Project for Elementary Mathematics](https://www.projectmath.net/wp-content/uploads/2017/06/Students%E2%80%99-Multidigit-Addition-_-Subtraction.pdf) — secondary
- [CGI: Children's Solution Strategies (PD handout citing Carpenter et al. 2015)](https://faculty.gordonstate.edu/gclement/Community%20Education/Number%20&%20Operations%20CGI%20PD%20Resources/CGI%20Childrens%20Solution%20Strategies.pdf) — secondary
- [Multi-digit Addition and Subtraction (blog citing Carpenter 1999 p. 70)](https://mathteachingstrategies.wordpress.com/2008/11/24/multi-digit-addition-and-subtraction/) — secondary, definitions garbled
- [Children's Mathematics, 2nd ed. — publisher TOC](http://www.heinemann.com/products/childrens-mathematics-second-edition-e05287.aspx)
