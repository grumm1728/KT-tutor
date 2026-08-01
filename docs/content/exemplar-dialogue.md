# The exemplar dialogue and its branch tree

Authored content for issue [#7](https://github.com/grumm1728/KT-tutor/issues/7). Written 2026-07-31.

**Composite utterances drawn from classroom experience, not recordings.** Every student turn below is
authored — paraphrases of aggregate children from the author's professional experience, plus loose
quotes from his own children with consent. No third-party identifiable data, and nothing
CoMTA-derived. That sentence, or one like it, must appear in the shipped artifact: the demo argues
about the authenticity of spoken input, so a viewer who assumes verbatim transcript is misled in
exactly the dimension under argument.

---

## What this document is

The finite dialogue tree the demo walks, with each student node carrying the analysis the right pane
renders: a free-text description, its alignment onto the component set from
[#6](https://github.com/grumm1728/KT-tutor/issues/6), a polarity and confidence per component, and
the utterance fragment that evidences each one.

**The component values here are authored targets, not the pass's output.** The precompute pass
([#10](https://github.com/grumm1728/KT-tutor/issues/10)) will emit its own. If what it emits is far
from what is written here, one of the two is wrong and that is worth knowing — so treat this
document as the acceptance criteria for the analyzer, not as a description of it.

The shipped data shape is #10's call. This is the source content, not the format.

### The genre

A **Number Talk** (Parrish; popularized by Boaler), which is a *problem string* — several related
problems worked in sequence, mentally, out loud, with the teacher's job being to hear a strategy and
represent it. So the session is three problems, and the branching budget is spent almost entirely on
the first.

| Problem | Role | Branching |
|---|---|---|
| **17 + 8** | The spine | Deep — 3 branch points, 7 student responses, 2 repair routes |
| **24 + 26** | Stress case: composite strategy behind a correct answer | Single path, 2 turn pairs |
| **37 + 15** | Stress case: constraint reasoning, fires the escape hatch | Single path, 1 turn pair |

15 student nodes total. Trivially precomputable, and it stays that way — adding a branch means
re-running the pass, so the tree is meant to be small.

### The tree at a glance

```
p1.t1  "Seventeen plus eight. …What did you get, and how'd you get there?"
│
├── p1.s1a  "Twenty-four. …four on this side and four on that side."   ◀ SPINE   y=0
│   │
│   ├── p1.t2a  "Show me the four?"                                    ◀ CARD
│   │   ├── p1.s2a  "…up to twenty — I mean twenty-five."              ◀ SPINE   y=1
│   │   │   └── p1.t4 → p1.s4  "I counted the twenty as one of them."           y=1
│   │   ├── p1.s2b  "Seventeen, eighteen, nineteen, twenty — four."             y=0
│   │   │   └── p1.t3b → p1.s3b  "one… two… three. Oh."                         y=1
│   │   │       └── p1.t4 → p1.s4                                               y=1
│   │   └── p1.s2c  "'Cause eight is four and four."                            y=0
│   │       └── p1.t3c → p1.s3c  "Twenty-one. …That's not twenty."              y=1
│   │
│   ├── p1.t2b  "Not quite. Try again?"        → p1.s2d  "…twenty-three?"       y=0
│   └── p1.t2c  "Where's the twenty in that?"  → p1.s2e  "seventeen and the four"  y=0
│
├── p1.s1b  "Twenty-five. …three more is twenty, and then five more."           y=1
│   └── p1.t2d "How'd you know it was three?" → p1.s2f "It just is."            y=1
│
└── p1.s1c  "Twenty-four."  [silence]                                 ◀ CONTROL y=0
    └── p1.t2e "How'd you get there?" → p1.s2g  ══ converges on p1.s1a ══       y=0

p2.t5 → p2.s5  "two twenty-sixes is fifty-two, and then you take two away."     y=1
        p2.t6 → p2.s6  "the first one's twenty-four. It's two littler."         y=1

p3.t7 → p3.s7  "it ends in a two. And forty-two's too small. So… fifty-two."    y=1
```

Three branch points on problem 1 — what the student says (`p1.s1`), what the teacher does
(`p1.t2`), which hypothesis is true (`p1.s2`). Deepest path is 12 turns:
`t1 · s1c · t2e · s2g · t2a · s2b · t3b · s3b · t4 · s4`.

### It is speech, not chat

Committed in [#13](https://github.com/grumm1728/KT-tutor/issues/13). The transcript carries
disfluency, restarts, mid-utterance self-repair, trailing off, and prosodic emphasis. Number Talk
protocol is authored in too: the problem is posed, there is silent think time, and children signal
readiness with a thumb rather than a raised hand. A transcript that reads like a chat log undercuts
the claim it exists to support.

Punctuation convention: `—` is a self-interruption, `…` is a pause or trailing off, *italics* is
spoken stress.

---

## Reference: the component set

Fifteen components, six clusters, from #6. Ids are used throughout the node blocks below.

| Cluster | Id | Component | Provenance |
|---|---|---|---|
| **Reading the gap to a landmark** | `gap.counting-order` | counting-order reading — "17, 18, 19, 20 is four" | cited — Kwon et al. 2024, "hatch mark counting" |
| | `gap.path` | discrete / path reading — jumps, blocks | cited — Solomon et al. 2015 |
| | `gap.length` | length reading — distance as measure | cited — NRC 2009; Solomon et al. 2015 |
| | `gap.retrieved` | retrieved combination — "17 needs 3" | adapted |
| **Decomposing an addend** | `decomp.preserves-whole` | the parts still make the whole | adapted — "embedded number", CCSS OA Progression |
| | `decomp.purposeful-split` | the split is chosen *for* the landmark | **coined** |
| | `decomp.role-differentiation` | the two parts play different roles | **coined** |
| **Landmark structure** | `landmark.salience` | ten / a decade is worth reaching for | reused — Parrish; Fosnot & Dolk |
| | `landmark.place-value-separation` | tens and ones handled as separate units | adapted |
| **Derived facts** | `derived.doubles` | doubles and near-doubles | established |
| | `derived.near-double-recognition` | seeing a problem *as* near a double | established |
| **Operating by adjustment** | `adjust.compensation` | change one number, undo the change | established — CGI "compensating" |
| | `adjust.direction-and-size` | which way to adjust, and by how much | **coined** |
| **Reasoning about the answer** | `answer.ones-digit` | the ones digit is determined | adapted |
| | `answer.magnitude` | roughly how big the answer must be | established |

### Authoring rules for polarity

- **`present`** — the utterance gives positive evidence the idea is in play and doing work.
- **`not-yet`** — the utterance gives positive evidence the idea was *not* in play at a point where
  it was needed. A claim about this moment, not about the child.
- **`ambiguous`** — the utterance is consistent with the idea being in play or not. Rival components
  co-lit as `ambiguous` in one cluster is the representation of competing live hypotheses, and it is
  the demo's payload.

**Silence is not `not-yet`.** A component with no evidence either way does not appear on the node at
all. Getting this wrong turns the right pane into a deficit checklist, which the project's own
anti-deficit commitment (Smith, diSessa & Roschelle 1994) forbids. The invariant test in
`src/model/invariants.test.ts` should assert it: no component may be emitted with `not-yet` unless it
also carries an evidence fragment.

Confidences are independent per component — they do not compete for probability mass, so a composite
reads as *two things present* rather than *uncertainty between two things*. Authored values are drawn
from a deliberately coarse set — `0.9 / 0.75 / 0.6 / 0.45 / 0.3` — because finer granularity would
be false precision on a hand-authored judgment.

### Baseline column

Each student node records `y_j ∈ {0, 1, na}` per the paper's definition: whether `s_j` correctly
answers `t_j`. That is a property of the authored content, so it belongs here. **KC assignment and
mastery trajectory are [#9](https://github.com/grumm1728/KT-tutor/issues/9)'s call** and are not
decided in this document.

One thing to carry into #9, from [#2](https://github.com/grumm1728/KT-tutor/issues/2): at the moment
of prediction, LLMKT has not seen the utterance it is scoring — the dialogue is truncated after the
teacher turn. So the baseline reads strategy talk only on *subsequent* turns. The contrast this tree
draws is with the baseline's **output representation**, never with its perception.

---

## Problem 1 — 17 + 8

### `p1.t1` — the pose

> **Teacher.** Seventeen plus eight. …Take your time. Thumb up when you've got something.
>
> *[silent think time]*
>
> **Teacher.** Okay — what did you get, and how'd you get there?

The second question is load-bearing and should be visible as such. "What did you get" alone produces
the `p1.s1c` branch, and the whole demo is the difference between those two nodes.

---

### Branch point 1 — what the student says

Three responses. `p1.s1a` is the spine; `p1.s1c` is the control that makes the input-channel argument
without anyone having to assert it.

---

#### `p1.s1a` — wrong answer, correct strategy *(spine)*

> **Student.** Twenty-four. …'Cause it's *four* on this side and four on that side.

**Baseline:** `y = 0`.

**Free text.** *Bridging to twenty. The student splits eight into two parts, one to close the gap
from seventeen and one to add on past the landmark, and gives the total as twenty-four. The split is
4 and 4, which is where the answer goes wrong — the gap from seventeen to twenty is three, not four.
The strategy is intact; the distance is not. Two readings of that four are live: it may have been
counted as the number names seventeen-eighteen-nineteen-twenty, or eight may have split into four
and four because that partition is salient, with the four then assigned to the gap without checking.*

| Component | Polarity | Conf. | Evidence fragment |
|---|---|---|---|
| `decomp.preserves-whole` | present | 0.9 | "four on this side and four on that side" |
| `decomp.purposeful-split` | present | 0.75 | "four on this side" |
| `decomp.role-differentiation` | present | 0.75 | "this side and… that side" |
| `landmark.salience` | present | 0.75 | "four on this side" |
| `gap.counting-order` | ambiguous | 0.6 | "*four* on this side" |
| `gap.path` | ambiguous | 0.45 | "*four* on this side" |
| `derived.doubles` | ambiguous | 0.45 | "four on this side and four on that side" |

**This is the key turn.** Four components present, two rival components co-lit in *Reading the gap to
a landmark*, on a turn the baseline scores wrong. A correctness-based tracer sees `y = 0` and
decrements; four ideas are working and one specific reading of one specific gap is not, and the
scalar cannot carry the difference.

Note that the same fragment — "four on this side" — evidences both rivals *and* the doubles reading.
That is correct and should not be smoothed away. One phrase, three readings, told apart only by
asking.

⚠️ **For [#8](https://github.com/grumm1728/KT-tutor/issues/8):** the doubles hypothesis is lit in a
*different cluster* from the two it competes with. Rivalry is not always intra-cluster, so a design
that renders competition only as "two bars in one box" will fail on the spine's most interesting
node.

---

#### `p1.s1b` — correct answer, clean bridge

> **Student.** Twenty-five. Um… I did seventeen, and then three more is twenty, and then five more.
> Twenty-five.

**Baseline:** `y = 1`.

**Free text.** *A clean bridge to twenty. Eight is split into three and five, the three chosen
specifically to close the gap to the landmark, and the five added on after. The gap is read
correctly. Nothing here distinguishes how the three was arrived at — retrieved, or counted, or seen.*

| Component | Polarity | Conf. | Evidence fragment |
|---|---|---|---|
| `decomp.preserves-whole` | present | 0.9 | "three more is twenty, and then five more" |
| `decomp.purposeful-split` | present | 0.9 | "three more is twenty" |
| `decomp.role-differentiation` | present | 0.75 | "three more… and then five more" |
| `landmark.salience` | present | 0.9 | "three more is twenty" |
| `gap.retrieved` | ambiguous | 0.45 | "three more is twenty" |

This is the counterfactual the spine is measured against, and it is where the pane should be least
interesting. Everything present, nothing competing.

---

#### `p1.s1c` — the bare answer *(control)*

> **Student.** Twenty-four.
>
> *[silence]*

**Baseline:** `y = 0` — **identical to `p1.s1a`.**

**Free text.** *An answer with no account of how it was reached. There is nothing here to analyze.*

*(no components)*

**This is the most argumentative node in the tree, and it works by being empty.** `p1.s1a` and
`p1.s1c` are the same problem, the same student, the same wrong answer, and the same baseline label.
The right pane is rich on one and blank on the other. The demo's first leg — that the input channel
is what makes conception visible — is a two-node A/B inside its own content, not a claim in a
caption.

⚠️ **For #8:** the empty state must read as *nothing was said to analyze*, not as low mastery or a
failed analysis. Rendering blankness as a deficit inverts the argument this node exists to make.

---

### Branch point 2 — what the teacher does

From `p1.s1a`. Three moves, of which one is the card.

---

#### `p1.t2a` — the probe *(card)*

> **Teacher.** Mm. …Show me the four?

**Disambiguates:** `gap.counting-order` vs `gap.path` vs `derived.doubles`. It works by forcing the
student to say *which object* the four lives in — the gap from seventeen to twenty, or the eight.
Those are different objects, and no amount of re-reading the first utterance separates them.

This is the mechanic's citable warrant, not a design intuition. Kwon et al. (2024) show hatch mark
counting is invisible under ordinary conditions and surfaces only on an item engineered to expose it;
Minstrell's DIAGNOSER writes distractors to correspond to specific problematic facets. Same move.
Card design proper is [#11](https://github.com/grumm1728/KT-tutor/issues/11)'s.

---

#### `p1.t2b` — correctness only *(anti-pattern tail)*

> **Teacher.** Not quite. Try again?

The move a correctness-based tutor can make, and the ceiling on what it can do with `y = 0`. Kept as
a two-node tail so the contrast is walkable rather than asserted.

---

#### `p1.t2c` — the weaker probe *(tail)*

> **Teacher.** Where's the twenty in that?

A real probe that disambiguates less. It asks about the landmark, which is the part already working,
so the answer confirms what is not in doubt. Useful to #11 as the near-miss: two cards, both
plausible, one of which resolves the live competition and one of which does not.

---

### Branch point 3 — which hypothesis is true

From `p1.t2a`. The payload.

---

#### `p1.s2a` — self-repair *(spine)*

> **Student.** Seventeen, up to twenty — …I mean twenty-*five*. It's twenty-five. 'Cause it's three
> on this side and five on that side.

**Baseline:** `y = 1`.

**Free text.** *Articulating the jump repairs it mid-utterance. The student begins to trace
seventeen up to twenty, sees the distance is three rather than four, and revises both the split and
the total without being told anything was wrong. The strategy never changed — only the distance did.
This is the reading of the gap as a traversal rather than as a count of number names.*

| Component | Polarity | Conf. | Evidence fragment |
|---|---|---|---|
| `gap.path` | present | 0.75 | "Seventeen, up to twenty —" |
| `gap.counting-order` | not-yet | 0.45 | "Seventeen, up to twenty — …I mean twenty-*five*" |
| `decomp.purposeful-split` | present | 0.9 | "three on this side and five on that side" |
| `decomp.preserves-whole` | present | 0.9 | "three on this side and five on that side" |
| `decomp.role-differentiation` | present | 0.9 | "three on this side and five on that side" |
| `landmark.salience` | present | 0.9 | "up to twenty" |
| `derived.doubles` | not-yet | 0.6 | "three on this side and five on that side" |

The transition is the thing to render, not the state: three ambiguous components resolve on one turn,
in response to a question rather than to being corrected. Carpenter et al. predict exactly this —
bugs coexist with understanding and are "often eliminated by simple counter suggestion."

`gap.counting-order` is marked `not-yet` at low confidence rather than `present`: the repair is
evidence the counting-order reading was not load-bearing after all, but a single self-correction is
weak evidence. Do not let it read as a verdict.

⚠️ **For #8:** the probe → repair arc is a *temporal transition*, and a pane that renders only
current state will show the payload as a state change with no visible cause.

---

#### `p1.s2b` — counting-order confirmed

> **Student.** Seventeen, eighteen, nineteen, twenty — see. Four.

**Baseline:** `y = 0`.

**Free text.** *The four is a count of number names, seventeen through twenty inclusive. The gap is
being read as the number of labels passed rather than as the number of steps between them — the
hatch mark counting error, here in its numeric rather than its ruler form. The bridging strategy
around it is untouched and still correct. No repair; the probe has surfaced the reading rather than
dislodging it.*

| Component | Polarity | Conf. | Evidence fragment |
|---|---|---|---|
| `gap.counting-order` | present | 0.9 | "Seventeen, eighteen, nineteen, twenty — see. Four." |
| `gap.path` | not-yet | 0.75 | "Seventeen, eighteen, nineteen, twenty — see. Four." |
| `derived.doubles` | not-yet | 0.75 | "Seventeen, eighteen, nineteen, twenty" |
| `decomp.purposeful-split` | present | 0.75 | *(carried — the split was for the landmark, and the count confirms it)* |
| `landmark.salience` | present | 0.9 | "…twenty" |

The important branch for honesty: the probe does not always trigger repair. Kwon et al. find the
error stable absent targeted instruction and persisting into middle school, so a tree in which every
probe produces self-correction would be authoring a research finding out of existence.

Note `derived.doubles` goes `not-yet` here — the four was counted, not imported from the eight. One
utterance, two clusters updated, in opposite directions.

---

#### `p1.s2c` — doubles-pull confirmed

> **Student.** 'Cause eight *is* four and four.

**Baseline:** `y = 0`.

**Free text.** *The four comes from the eight, not from the gap. Eight splits into four and four
because that partition is the salient one, and the resulting four was then assigned to the
seventeen-to-twenty gap without the gap ever being read. The decomposition is sound and the landmark
is real; what is missing is the link between them — the split was not chosen for this gap.*

| Component | Polarity | Conf. | Evidence fragment |
|---|---|---|---|
| `derived.doubles` | present | 0.9 | "eight *is* four and four" |
| `decomp.preserves-whole` | present | 0.9 | "eight *is* four and four" |
| `decomp.purposeful-split` | not-yet | 0.75 | "eight *is* four and four" |
| `gap.retrieved` | not-yet | 0.6 | "eight *is* four and four" |
| `landmark.salience` | present | 0.6 | *(carried from "four on this side")* |

The instructive shape: the *Reading the gap* cluster goes largely dark rather than lit. The student
did not misread the gap — they never read it. An architecture that can only represent "which reading
was used" has no way to say that, which is why polarity has a `not-yet` and not just a confidence.

---

### Repair routes

---

#### `p1.t3b` → `p1.s3b` — repair via the path reading

> **Teacher.** Okay. Point them out for me — the jumps.
>
> **Student.** Seventeen to eighteen, that's one… two… three. …Oh. …Three. So it's twenty…
> twenty-five.

**Baseline:** `y = 1`.

**Free text.** *Asked for the jumps rather than the count, the student re-reads the gap as a
traversal and gets three. The correction propagates: the split becomes three and five, and the total
becomes twenty-five. Same repair as the spine, arrived at more slowly and with the teacher naming the
unit.*

| Component | Polarity | Conf. | Evidence fragment |
|---|---|---|---|
| `gap.path` | present | 0.9 | "Seventeen to eighteen, that's one… two… three." |
| `gap.counting-order` | not-yet | 0.6 | "…Oh. …Three." |
| `landmark.salience` | present | 0.75 | "So it's twenty… twenty-five." |
| `decomp.preserves-whole` | present | 0.75 | "twenty… twenty-five" |

The slower route matters. Solomon et al. found performance was fine on discrete units and at chance
on the continuous ruler — the two readings conflict rather than nest, and here the discrete one is
what rescues the answer. That is why they are sibling components in a cluster and not rungs on a
ladder.

---

#### `p1.t3c` → `p1.s3c` — repair via the landmark check

> **Teacher.** Mm hm. So seventeen and four is…?
>
> **Student.** Twenty-one. …Oh. That's not twenty.

**Baseline:** `y = 1`.

**Free text.** *Computing seventeen plus four surfaces the contradiction directly: the part meant to
land on twenty lands on twenty-one. The student notices without being told. The gap has still not
been re-read — what has been established is that the four does not do the job it was assigned, not
yet what does.*

| Component | Polarity | Conf. | Evidence fragment |
|---|---|---|---|
| `decomp.role-differentiation` | present | 0.75 | "That's not twenty." |
| `landmark.salience` | present | 0.9 | "That's not twenty." |
| `derived.doubles` | ambiguous | 0.45 | *(carried — the split's origin is untouched by this)* |

A partial repair, and worth having one in the tree. The demo should not imply every probe closes
every gap in one turn.

---

### `p1.t4` → `p1.s4` — consolidation

Reachable from `p1.s2a` and from `p1.s3b`.

> **Teacher.** So what changed, between twenty-four and twenty-five?
>
> **Student.** I counted the twenty as one of them. But it's only three *jumps*.

**Baseline:** `y = 1`.

**Free text.** *The student names the error rather than just correcting it: the endpoint was counted
as one of the things being counted, and the unit is the jump, not the label. This is the distinction
between reading a gap as a sequence of names and reading it as a set of intervals, stated by the
student in their own terms.*

| Component | Polarity | Conf. | Evidence fragment |
|---|---|---|---|
| `gap.path` | present | 0.9 | "it's only three *jumps*" |
| `gap.length` | present | 0.6 | "it's only three *jumps*" |
| `gap.counting-order` | not-yet | 0.75 | "I counted the twenty as one of them" |

This is the node that earns the phrase the project's own writing uses — *distance to the nearest ten*
becoming a first-class idea. It is also the strongest single-turn evidence in the tree, and the
baseline records it as one more `1`.

---

### Tails

---

#### `p1.t2b` → `p1.s2d` — after "try again"

> **Student.** Um… …twenty-three?

**Baseline:** `y = 0`.

**Free text.** *A second answer offered with no account attached. The correction has been received as
"that number was wrong" rather than as information about the reasoning, and the student has moved to
guessing. Nothing about the strategy is recoverable from this turn.*

| Component | Polarity | Conf. | Evidence fragment |
|---|---|---|---|
| *(none)* | | | |

The point of the tail: the explanation channel is not a fixed property of the medium. It is opened by
what the teacher asks and closed by being told you are wrong. Two turns after a rich node, the pane
is as empty as `p1.s1c`.

---

#### `p1.t2c` → `p1.s2e` — after the weaker probe

> **Student.** It's… seventeen and the four. That makes the twenty.

**Baseline:** `y = 0`.

**Free text.** *Restates the plan without re-examining the four. The landmark and the role
assignment are confirmed — which were never in doubt — and all three competing readings of the four
survive untouched.*

| Component | Polarity | Conf. | Evidence fragment |
|---|---|---|---|
| `landmark.salience` | present | 0.9 | "That makes the twenty." |
| `decomp.role-differentiation` | present | 0.75 | "seventeen and the four" |
| `gap.counting-order` | ambiguous | 0.6 | *(carried, unchanged)* |
| `gap.path` | ambiguous | 0.45 | *(carried, unchanged)* |
| `derived.doubles` | ambiguous | 0.45 | *(carried, unchanged)* |

**A card that does not resolve anything is a legible outcome, not a bug.** The three ambiguous
components carry forward at unchanged confidence, which is the visual signature of a probe that
missed. #11 gets a worked negative example out of this.

---

#### `p1.t2d` → `p1.s2f` — after the clean bridge

> **Teacher.** How'd you know it was three?
>
> **Student.** 'Cause seventeen needs three to get to twenty. …It just is.

**Baseline:** `y = 1`.

**Free text.** *The three is retrieved rather than derived — the student reports it as known, not as
worked out, and cannot decompose it further. That is a fact about how the gap is held, and it is not
a deficiency; it is what fluency looks like from outside.*

| Component | Polarity | Conf. | Evidence fragment |
|---|---|---|---|
| `gap.retrieved` | present | 0.9 | "seventeen needs three to get to twenty. …It just is." |

⚠️ **For #8:** "It just is" is a floor on inspectability, and the artifact is better for containing
one. Verbal explanation does not make everything visible, and a demo that never hits that floor is
overclaiming.

---

#### `p1.t2e` → `p1.s2g` — eliciting after the bare answer

> **Teacher.** Twenty-four. Okay — how'd you get there?
>
> **Student.** 'Cause it's four on this side and four on that side.

**Baseline:** `y = 0`.

**Analysis:** identical to `p1.s1a`. This node **converges** on the spine state.

The convergence is the argument, and it should be visible in the UI: the same student, the same turn,
the same wrong answer, and one question in between. The channel was not absent — it was unopened.
From here the tree continues into branch point 2 exactly as from `p1.s1a`, so this costs one node and
buys the demo's cleanest statement of its own first leg.

---

## Problem 2 — 24 + 26

Stress case: a **composite** strategy behind a **correct** answer. Single path.

### `p2.t5` → `p2.s5`

> **Teacher.** Try this one. Twenty-four plus twenty-six.
>
> *[think time]*
>
> **Student.** Fifty. Um — two twenty-*sixes* is fifty-two, and then you take two away. Fifty.

**Baseline:** `y = 1`.

**Free text.** *Two strategies at once. The student replaces twenty-four with a second twenty-six to
make a double, computes fifty-two, then compensates by removing the two that replacement added. Both
moves are fully present — this is not uncertainty between doubling and compensating, it is doubling
and compensating.*

| Component | Polarity | Conf. | Evidence fragment |
|---|---|---|---|
| `derived.doubles` | present | 0.9 | "two twenty-*sixes* is fifty-two" |
| `derived.near-double-recognition` | present | 0.75 | "two twenty-*sixes*" |
| `adjust.compensation` | present | 0.9 | "and then you take two away" |
| `adjust.direction-and-size` | present | 0.6 | "take *two* away" |

**Why this one is here.** It is the composite the settled model has to hold with no special handling,
and it is the case for independent confidences over a simplex: on a distribution these two would
split the mass and the pane would show doubt where the student showed none. Two clusters, both fully
lit, is the correct picture.

It is also the strategy-behind-a-correct-answer case in its purest form. The baseline records `1` and
is finished. Everything the student actually did happens inside that bit.

---

### `p2.t6` → `p2.s6` — probing a correct answer

> **Teacher.** Why take two away, and not add two?
>
> **Student.** 'Cause I used twenty-six twice, but the first one's twenty-*four*. It's two littler,
> so I gotta… take the two back off.

**Baseline:** `y = 1`.

**Free text.** *The direction of the adjustment is reasoned, not remembered. The student names the
substitution, notes that the substituted number is larger, and derives that the correction must
subtract. This is the part of compensating that most often runs as an unexamined habit.*

| Component | Polarity | Conf. | Evidence fragment |
|---|---|---|---|
| `adjust.direction-and-size` | present | 0.9 | "the first one's twenty-*four*. It's two littler, so… take the two back off." |
| `adjust.compensation` | present | 0.9 | "I used twenty-six twice" |
| `decomp.preserves-whole` | present | 0.6 | "It's two littler" |

**Note what the turn pair is.** A probe on a *correct* answer, which a correctness-driven tutor has
no reason to play — there is nothing to remediate. The card mechanic is about resolving live
hypotheses, and hypotheses are live regardless of `y`. This node is the argument for that, and it
belongs in #11's material.

---

## Problem 3 — 37 + 15

Stress case: **constraint reasoning**, and the node that fires the escape hatch. Single path.

### `p3.t7` → `p3.s7`

> **Teacher.** Last one. Thirty-seven plus fifteen.
>
> *[think time]*
>
> **Student.** Um… it ends in a *two*. And it's not forty-two — forty-two's too small. So… fifty-two.

**Baseline:** `y = 1`.

**Free text.** *The student never adds. Seven and five fix the ones digit at two, which narrows the
answer to a set of candidates, and forty-two is then eliminated as too small, leaving fifty-two. The
answer is arrived at by constraining and selecting rather than by constructing a value — the object
being reasoned about is the answer itself, not the addends.*

| Component | Polarity | Conf. | Evidence fragment |
|---|---|---|---|
| `answer.ones-digit` | present | 0.9 | "it ends in a *two*" |
| `answer.magnitude` | present | 0.75 | "forty-two's too small" |

> **⚠️ Not in this set.** *Treating candidate answers as objects to be ruled out. Every component in
> the set describes operating on the addends or on the gap between them; none describes generating a
> field of possible answers and eliminating from it. Forty-two is not a wrong answer the student
> made — it is a rival candidate they considered and rejected, and there is nowhere in the set to put
> that.*

**The escape hatch is a feature, displayed.** A closed-set-only output would have forced this turn
onto `answer.magnitude` and lost the interesting half, which is multiple choice reintroduced at the
model layer — precisely what the project argues against. The hatch firing on authored content that
was written to fire it is the demonstration.

Two further things this node does. The first four clusters stay dark, so the pane shows *a different
kind of move* without needing a separate category or a second rendering mode. And the baseline
records `y = 1` with the same KCs any two-digit addition turn would carry: that the student never
performed an addition is invisible in the scalar.

---

## Coverage

| Cluster | Nodes reaching it |
|---|---|
| Reading the gap to a landmark | `p1.s1a` `p1.s1b` `p1.s2a` `p1.s2b` `p1.s2c` `p1.s2e` `p1.s2f` `p1.s3b` `p1.s4` |
| Decomposing an addend | `p1.s1a` `p1.s1b` `p1.s2a` `p1.s2b` `p1.s2c` `p1.s2e` `p1.s3b` `p1.s3c` `p2.s5` `p2.s6` |
| Landmark structure | `p1.s1a` `p1.s1b` `p1.s2a` `p1.s2b` `p1.s2c` `p1.s2e` `p1.s3b` `p1.s3c` |
| Derived facts | `p1.s1a` `p1.s2a` `p1.s2b` `p1.s2c` `p1.s2e` `p1.s3c` `p2.s5` |
| Operating by adjustment | `p2.s5` `p2.s6` |
| Reasoning about the answer | `p3.s7` |

**`landmark.place-value-separation` is never evidenced.** No authored utterance splits into tens and
ones — the string is a bridging string, and combining-tens-and-ones does not appear in it. Two honest
options: add a fourth problem, or leave the component unexercised and say so. Recommend leaving it.
The component set was settled on the research rather than on this string, an unexercised component is
not a defect, and a fourth problem bought only to light a bar is content added for the model's
convenience rather than the argument's. Worth one line in the artifact's own notes.

`gap.length` appears once, at `p1.s4`, at 0.6. That is right — length reading is the least accessible
of the four gap readings for a child at this level, and authoring it as confidently present would be
authoring past the research.

---

## Downstream

**[#8](https://github.com/grumm1728/KT-tutor/issues/8) — right pane.** Four constraints this tree
imposes, beyond what #6 already pushed:

1. **The empty state is not the low state.** `p1.s1c` and `p1.s2d` have no components, and must read
   as *nothing was said to analyze* rather than as a floor.
2. **Rivalry crosses clusters.** At `p1.s1a` the doubles reading competes with two gap readings from
   a different cluster. Competition rendered only as adjacency inside a box fails on the spine's key
   node.
3. **The unchanged case needs a signature.** At `p1.s2e` three ambiguous components carry forward at
   identical confidence. A probe that missed should be visibly distinct from a probe not yet played.
4. **The transition is the payload.** `p1.t2a` → `p1.s2a` is a resolution event. A pane rendering
   only current state shows the effect with no visible cause.

**[#10](https://github.com/grumm1728/KT-tutor/issues/10) — precompute.** The pass runs over 15
student nodes. Two nodes carry state that is *carried* rather than freshly evidenced (`p1.s2c`,
`p1.s2e`), so the emitted shape needs to distinguish a component evidenced by this utterance from one
persisting from an earlier turn — otherwise the invariant test asserting a fragment on every
hypothesis either fails or gets weakened. `p1.s2g` converges on `p1.s1a`'s state, so node identity
and state identity are not the same thing.

**[#11](https://github.com/grumm1728/KT-tutor/issues/11) — cards.** Three worked examples: `p1.t2a`
resolves, `p1.t2c` does not, `p2.t6` fires on a correct answer. Kwon et al. (2024) and DIAGNOSER are
the external warrant for the mechanic — items engineered so competing components predict different
answers.

**[#9](https://github.com/grumm1728/KT-tutor/issues/9) — baseline.** `y_j` per node is above; KCs and
mastery are #9's. The tree is built so the contrast lands at `p1.s1a` (`y = 0`, four components
present) and at `p2.s5` / `p3.s7` (`y = 1`, everything interesting inside the bit).

## Open

1. **The four coined components are load-bearing on the spine.** `decomp.purposeful-split` and
   `decomp.role-differentiation` carry the spine's central claim — strategy intact, distance wrong.
   Provenance is rendered per #6, but the artifact should not lean on a coined component to make its
   headline point without saying so out loud.
2. **`p1.s2b` has no exit.** The counting-order-confirmed branch deliberately ends without repair.
   Whether the demo can leave a path unresolved, or needs a closing teacher turn that acknowledges it
   without fixing it, is a UI question for #8.
3. **Think-time rendering.** The tree assumes silent think time between pose and response, which is
   Number Talk protocol and part of what makes the genre claim real. How that reads on a phone
   outline is unspecified.
