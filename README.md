# The Unofficial Guide

<!-- Replace this line with your name and which corpus you picked. -->

> **This file is your submission.** Fill it in as you go — most sections get
> written during the milestone that produces them, not at the end.
>
> How the starter works, and every command you'll need, is in `RUNNING.md`.
> Leave that file alone.
>
> **Paste everything as text.** No screenshots, no video. A typed table gets
> full credit; a picture of the same table gets none.
>
> Delete these instruction blocks as you replace them. The `<!-- -->` comments
> are notes to you and don't show up when the page renders — you can leave them
> or remove them.

---

# Unit 1

## What This Does

This system answers practical travel questions about the towns and walking
routes in the `city_guides` corpus. It retrieves relevant guide sections using
local embeddings, checks their relevance before answering, and then asks the
generation model to use only those sections. Answers cover transport,
accessibility, food, accommodation, seasonal conditions, and things to see,
with the source filenames shown alongside each answer.

## Chunking Strategy

**Chunk size:** 800 characters maximum, while preserving each Markdown `##` section
**Overlap:** 0 characters

I picked `city_guides` because its 14 documents are long guides organized into
labelled sections such as Getting there, Eat and drink, and When to go. The
starter's fixed windows produced 51 chunks and cut through words and sentences;
the longest complete section was 711 characters, so an 800-character limit is
large enough to keep every section intact. I used no overlap because each chunk
repeats the guide title and includes its own section heading, which supplies the
context without duplicating neighbouring sections.

<!-- What about YOUR documents made you pick these numbers? Short posts and
     long sectioned guides don't want the same chunking, and "800 seemed
     reasonable" earns nothing. Point at something you noticed when you read
     the documents in Milestone 1.

     If you changed your mind partway through, say so and say why. That's worth
     more than pretending you got it right first time.

     Milestone 3. -->

## Sample Chunks

<!-- Five chunks, pasted as text. Label each one and name the file it came from
     AND the function that produced it — the grader checks your code against
     what you claim here.

     `python app.py chunks -n 5` prints all three for you. Copy them straight
     across.

     Milestone 3. -->

**Chunk 1** — source: `guide_accessibility.md#0` — produced by: `chunker.py::split_documents`

```
# Getting around the region with limited mobility

An honest assessment rather than a promotional one. Some of these places are
difficult and it is better to know in advance.
```

**Chunk 2** — source: `guide_corry_vale.md#5` — produced by: `chunker.py::split_documents`

```
# Corry Vale

## Where to stay

Perhaps thirty beds in the entire valley, spread across two pubs and a handful of farmhouse rooms. In summer these are booked months ahead. Camping is permitted on two marked fields and nowhere else.
```

**Chunk 3** — source: `guide_givens_mill.md#2` — produced by: `chunker.py::split_documents`

```
# Givens Mill

## Getting around

Everything is on one street along the river. The mill is at one end and the church at the other, eight minutes apart. The riverside path continues in both directions for as far as you want to walk.
```

**Chunk 4** — source: `guide_kestrelford.md#4` — produced by: `chunker.py::split_documents`

```
# Kestrelford

## What to see

The market square on a Saturday morning is the main event and has run continuously since the 1400s. The parish church has a 13th-century tower you can climb for £2. The old trackbed walk runs six miles to the next village along an easy gradient and is the best half-day here.
```

**Chunk 5** — source: `guide_pellew_sands.md#6` — produced by: `chunker.py::split_documents`

```
# Pellew Sands

## When to go

June and September for the beach without the crowds. July and August are busy and the town is at its most itself, for better and worse. Winter is bleak, largely closed, and has a following among people who like that sort of thing.
```

## Sample Answer

<!-- One complete question and answer, pasted as text, with the source line
     visible. Milestone 4. -->

**Question:** Where should I eat in Pellew Sands if I want better food at a lower price?

**Answer:** You should eat on Marine Terrace, which is one street back from the
seafront. The cooking there is better and costs roughly half the seafront price.

```
Sources retrieved: guide_accessibility.md, guide_eating.md, guide_pellew_sands.md
```

The answer stayed within the retrieved excerpts and named the files it used, so
the existing `GROUNDING_INSTRUCTION` was strict enough for this corpus.

**My relevance cutoff:**

<!-- The number you set in config.py, and how you got there.

     You ran five questions your corpus covers and the five in OUT_OF_SCOPE
     that it clearly doesn't, and wrote down the best distance for each. What
     did those two groups look like? Where was the gap? Put the actual numbers
     here — the table below wants all ten rows.

     Milestone 4. -->

**Top-k:** 5

**Cutoff:** 0.6

The in-scope questions had best distances from 0.3524 to 0.5394. The
out-of-scope questions ranged from 0.8026 to 0.9753, leaving a clear gap
between 0.5394 and 0.8026. I kept the starter cutoff of 0.6 because it passes
all five in-scope questions while refusing all five out-of-scope questions.

| Question | In corpus? | Best distance |
|---|---|---|
| How do I get to Pellew Sands, and where can I park cheaply? | Yes | 0.3987 |
| Which towns in the region are most accessible for someone with limited mobility? | Yes | 0.5305 |
| What are the best easy walking routes, and how long are they? | Yes | 0.4676 |
| Where should I eat in Pellew Sands if I want better food at a lower price? | Yes | 0.3524 |
| What should I know about traveling by bus or train on Sundays? | Yes | 0.5394 |
| What is the capital of Mongolia? | No | 0.8026 |
| How do I change the oil in a diesel engine? | No | 0.8881 |
| Who won the 1994 World Cup? | No | 0.9753 |
| What is the recommended dosage of ibuprofen for a headache? | No | 0.8350 |
| How do I write a for loop in Rust? | No | 0.8365 |

## How I Used AI

<!-- Two specific moments. For each: what you asked for, what came back, and
     what you changed about it.

     "I asked Claude to write the chunking function from my notes. It ignored
     the overlap, so I added that myself" is the level of detail we're after.
     "I used AI to help me code" is not.

     Milestone 5. -->

**1.** I asked AI to suggest a chunking strategy after I measured that the
starter made 51 chunks from 14 city-guide documents and cut through labelled
sections. The first suggestion used fixed-size assumptions, so I checked the
actual section lengths myself and changed `chunker.py` to preserve each `##`
section, repeat the guide title, and use zero overlap.

**2.** I asked AI to diagnose the `KeyError: '_type'` raised by Chroma during
indexing. It identified stale persisted Chroma metadata and suggested resetting
the local database; after the reset, indexing succeeded, so I kept that repair
as an environment fix rather than changing the retrieval code.

**3.** I asked AI to help interpret the failure pattern in unit 2 after all five
in-scope questions were refused by the gate and the answers were generic
fallbacks. It pointed out that the questions were written for the `city_guides`
corpus but the project was still configured to use `campus_life`, so retrieval
was pulling unrelated documents. I changed the corpus setting to `city_guides`
and reran the evaluation; the pass/fail pattern matched the real issue rather
than a model-quality problem.

<!-- ── Stretch features ─────────────────────────────────────────────────────
     Doing one? Say so here BEFORE you start. A feature this README never
     claims earns nothing.
     ───────────────────────────────────────────────────────────────────────── -->

---

# Unit 2

<!-- These sections get ADDED to what's already above. Don't delete or rewrite
     unit 1 — the point is that someone can see what you said before you knew
     how it went. -->

## Run Log — Before

<!-- Your five criteria, three runs each. `python run_eval.py --label before`
     runs the questions, puts the OUT_OF_SCOPE ones through the gate, and
     writes it all into results/ for you. Targets come from criteria.md; the
     verdict column is your call.

     Criterion 3 is measured in one deterministic pass rather than three, so
     the same number goes in all three run columns. That's correct, not lazy.

     Milestone 1. -->

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunk contains the answer | 4 of 5 | 0/5 | 0/5 | 0/5 | MISSED |
| 2. Every answer names a source | 5 of 5 | 0/5 | 0/5 | 0/5 | MISSED |
| 3. Gate stops out-of-corpus questions | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 4. | | | | | |
| 5. | | | | | |

<!-- Underneath, paste the REAL output for each criterion from one of your
     runs — the actual text your system produced, not a description of it.
     Name the file and function that produced it. -->

Produced by `run_eval.py::main` and `run_eval.py::check_out_of_scope` in
`results/run_2026-09-23_2104_before.md`.

### Criterion 1 — retrieved chunk contains the answer (run 1)

```text
How do I get to Pellew Sands, and where can I park cheaply? — run 1
I don't have enough information about that.

Which towns in the region are most accessible for someone with limited mobility? — run 1
I don't have enough information about that.

What are the best easy walking routes, and how long are they? — run 1
I don't have enough information about that.

Where should I eat in Pellew Sands if I want better food at a lower price? — run 1
I don't have enough information about that.

What should I know about traveling by bus or train on Sundays? — run 1
I don't have enough information about that.
```

### Criterion 2 — every answer names a source (run 1)

```text
How do I get to Pellew Sands, and where can I park cheaply? — run 1
I don't have enough information about that.
```

No answer in this run named any source file, so the source requirement failed
for all five questions.

### Criterion 3 — gate stops out-of-corpus questions (single deterministic pass)

```text
What is the capital of Mongolia? | 0.860 | refused
How do I change the oil in a diesel engine? | 0.966 | refused
Who won the 1994 World Cup? | 0.842 | refused
What is the recommended dosage of ibuprofen for a headache? | 0.970 | refused
How do I write a for loop in Rust? | 0.865 | refused
```

## Verdicts

<!-- MET or MISSED for each of the five, against the target you wrote last
     unit — not a new one. Plus a sentence on how you decided. That sentence
     matters most where it was close.

     If your target said 4 of 5 and your runs came out 4, 3, 4, that's a MISS.
     The target has to hold, not show up occasionally.

     Milestone 2. -->

| # | Criterion | Verdict | How I decided |
|---|---|---|---|
| 1 | Retrieved chunk contains the answer | MISSED | The target was 4 of 5, but all three runs produced 0/5 answers that actually contained the answer in the retrieved material, so the target never held. |
| 2 | Every answer names a source | MISSED | The target was 5 of 5, but every answer in every run said only "I don't have enough information about that." and named no source file. |
| 3 | Gate stops out-of-corpus questions | MET | The target was 4 of 5 and all five out-of-scope questions were refused by the gate in the deterministic pass, exceeding the target. |
| 4 |  |  |  |
| 5 |  |  |  |

## Diagnoses

<!-- For each miss: which stage caused it, and how. The stage alone isn't
     enough — you need the mechanism.

     Not a diagnosis: "Question 3 didn't work."
     A diagnosis:     "Question 3 asks about laundry costs. The answer is in
                       one sentence that got split across two chunks, so
                       neither chunk on its own contains it."

     The five stages: loading → chunking → embedding → retrieval → generation.

     Look for a pattern. If three misses all ask about numbers, that's one
     problem, not three.

     Missed nothing? Say so, then say honestly whether your targets were set
     low, and which one you'd tighten and to what.

     Milestone 3. -->

**Criterion 1 — Retrieved chunk contains the answer**

- **Stage:** loading / retrieval
- **Mechanism:** the evaluation was run against the `campus_life` corpus while the test questions were written for the `city_guides` corpus, so the top-k retrieval results were unrelated documents such as housing and course admin pages. Because none of those results contained the answer text, the generation step had no usable evidence and returned the generic refusal "I don't have enough information about that."

**Criterion 2 — Every answer names a source**

- **Stage:** generation
- **Mechanism:** the answer generator never received relevant evidence because the retrieval results were off-corpus, so it produced the fallback refusal text instead of a grounded response. That same fallback lacked any `Sources retrieved:` line or filename metadata, which is why the source-attribution criterion failed in all three runs.

**Pattern across misses:**

The two missed criteria are both downstream effects of the same root cause: the system was evaluating the `city_guides` questions against the wrong corpus (`campus_life`). The retrieval stage returned unrelated housing and admin documents, so the generator had no evidence to ground a real answer and therefore produced the generic refusal text with no source metadata. This is one underlying problem, not two separate failures.

The system did not miss nothing: I would not treat the result as "excellent" because the target was purposely easy to reach only if retrieval was correct. The criterion I would tighten next is criterion 1: I would require the top three retrieved results to include the answer phrase for at least 4 of 5 questions, because the current wording was too broad and too easy to mismeasure across runs.

## The Improvement

**What I changed:**

I switched the project configuration from the `campus_life` corpus to the `city_guides` corpus, which matches the five questions in `questions.py` and the expected answer phrases.

**Why I picked it:**

The diagnosis showed both missed criteria were caused by the same root problem: retrieval was searching the wrong corpus, so the system returned unrelated housing and admin documents instead of the travel-guide answers.

<!-- Connect it to a specific diagnosis above in one sentence. If you can't,
     you picked a fix because it sounded impressive. -->

### Run Log — After

<!-- Same format, same five criteria, three runs each.
     `python run_eval.py --label after` -->

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunk contains the answer | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 2. Every answer names a source | 5 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 3. Gate stops out-of-corpus questions | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 4. | | | | | |
| 5. | | | | | |

**Did it help?**

Yes. After switching to the matching `city_guides` corpus, all five in-scope questions passed in each of the three runs and the gate refused all five out-of-scope questions. The root cause was the wrong corpus, so fixing that directly repaired the retrieval and generation path without changing unrelated pipeline logic.

<!-- Say plainly whether it did, and how you know. If it made things worse,
     say that — a change that backfired, honestly reported, earns full credit
     and is more interesting than one that worked. What matters is that you can
     tell.

     Milestone 4. -->

## What's Still Broken

<!-- For each criterion still missed after your fix: what you'd do about it,
     and why you stopped where you did.

     "I ran out of time" is fine if it's true. Pretending nothing is left is
     not.

     Milestone 5. -->

None of the measured criteria are still broken after the corpus fix. In the after-run, criteria 1, 2, and 3 all met their targets in all three runs: 5/5 for the in-scope retrieval and source checks, and 5/5 gate refusals for the out-of-scope questions. The root cause was the wrong corpus, and fixing the corpus resolved the retrieval and generation failures that caused the earlier misses.

Criteria 4 and 5 are still blank in this project log because I have not written or measured them yet; they are placeholders, not active failures. I stopped here because the assignment's core evaluation criteria were fixed and verified, and I did not want to invent a new problem to solve. If I continued beyond this point, I would next tighten the retrieval criterion to make the check more precise, but I would not claim there is still a broken requirement in the measured set.

## What I'd Do Differently

<!-- Knowing what you know now — which of your five criteria would you write
     differently, and why?

     Milestone 5. -->

I would rewrite criterion 1 to be more precise: instead of judging whether the retrieved chunks "contain the answer," I would define it as "for at least 4 of 5 questions, the top three retrieved results contain the answer phrase from `questions.py`." That is easier to measure consistently across runs and better matches the actual retrieval behavior that the system can verify without relying on a subjective judgment of what counts as relevant.

