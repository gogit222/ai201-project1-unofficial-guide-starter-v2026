# Acceptance criteria — The Unofficial Guide

Five criteria that say what "working" means for this system, written in unit 1
**before** any results existed.

An acceptance criterion names a target: a number, a count, a rate, or something
a person could plainly observe. *"Retrieval works"* is an opinion. *"For at
least 4 of my 5 test questions, the top results include a chunk containing the
answer"* is a criterion.

Under each one, write a sentence or two on **why that target** and not a
stricter or looser one. A reason that says something about your corpus or your
pipeline earns credit; *"80% seemed reasonable"* does not.

> Missing your own targets next unit costs you nothing. Setting a target so
> easy you can't miss it does.

---

## 1. Retrieved chunks contain the answer

For at least 4 of my 5 test questions, the top five retrieved chunks include
one containing the question's `expects` phrase from `questions.py`.

**Why this target:**

The city guides are split into separate town and topic documents, so the
answer-bearing passage should be near the top even when several guides share
words such as transport, food, or walking. Four of five allows for one
question whose topic may have weaker vocabulary overlap, while three would
not show dependable retrieval.

---

## 2. Every answer names a source

For all 5 in-scope questions, the output includes a `Sources retrieved:` line
followed by at least one filename from the retrieved chunks.

**Why this target:**

The command-line pipeline already returns the source filenames with every
successful answer, so this is achievable unless the answer path loses the
retrieval results or the source metadata. I chose all five because source
metadata is attached during retrieval, so allowing fewer would hide a
preventable attribution failure.

---

## 3. The relevance gate stops out-of-corpus questions

When I ask a question my documents clearly don't cover, the relevance gate
stops it and the system returns "I don't have enough information about that" —
in at least 4 of 5 tries.

<!-- The five questions are the ones in `OUT_OF_SCOPE` at the bottom of
     `questions.py`, and `run_eval.py` puts them through the gate and writes
     what happened into your run log. Swap them for your own if you'd rather —
     just keep five of them, or the "4 of 5" above has nothing to be 4 of. -->

**Why this target:**

The out-of-scope questions concern unrelated subjects, while the index only
contains regional travel guides, so at least 4 of 5 should fall beyond the
relevance cutoff and be refused without a model answer. Four is a useful
minimum because one unrelated question may still have an accidental wording
match, while a lower target would tolerate too many false answers.

---

## 4. Something about your chunks

<!-- YOU WRITE THIS ONE.

     How would you know if your chunks were the right size? Name something
     countable or observable.

     Examples of the right shape — don't copy these, they should come from
     what you actually saw in Milestone 3:
       - "At least 4 of 5 sampled chunks read as a complete thought, with no
          sentence cut in half at either end."
       - "No chunk is shorter than 200 characters, since anything below that
          in my corpus turned out to be a heading with no content under it." -->

When I run `python app.py chunks -n 5`, at least 4 of the 5 printed chunks read
as complete sections, with no sentence cut in half at either end.



**Why this target:**

The city guide documents are short, self-contained sections, so a chunk that
ends mid-sentence would lose useful travel details and make the answer harder
to ground in the source. Four of five recognizes that a sampled boundary can
be imperfect without accepting frequent broken chunks.



---

## 5. Your choice

<!-- YOU WRITE THIS ONE TOO.

     Pick something you actually care about getting right. It could be about
     speed, about refusals, about a particular kind of question your corpus
     handles badly, about source attribution being correct rather than merely
     present — anything, as long as it names a number or an observable
     outcome. -->

For at least 4 of my 5 test questions, the generated answer contains the
question's `expects` phrase from `questions.py`.



**Why this target:**

The expected phrases identify concrete places or travel details, so this tests
whether generation uses the retrieved evidence rather than merely returning a
non-empty or generally plausible answer. Four of five leaves room for one
correctly paraphrased answer that does not use the exact expected phrase,
while a lower score would not demonstrate dependable grounding.



---

<!-- ─────────────────────────────────────────────────────────────────────────
     UNIT 2 — read this before you change anything above.

     If a criterion turns out to be BROKEN rather than merely unmet, you can
     revise it, and that earns credit. But never delete or edit the original
     line. Add the revision underneath it, like this:

         ## 1. Retrieved chunks contain the answer

         For at least 4 of my 5 test questions, the retrieved chunks include
         one that contains the answer.

         **Why this target:** ...

         > **Revised in unit 2:** For at least 4 of 5 questions, the top three
         > results contain the answer.
         >
         > **Why revised:** I couldn't judge "the chunks include one that
         > contains the answer" the same way twice — I scored two questions
         > differently on Monday than on Wednesday. The new version is
         > something I can actually check.

     That's a revision because the criterion couldn't be MEASURED.

     Lowering a target because you missed it is not a revision, and it costs
     you the point:

         ✗ "I said 4 of 5 but got 2 of 5, so 2 of 5 is more realistic."

     A number you missed stays where it is, gets diagnosed, and gets a fix
     attempted. That's where the points are.

     The whole reason the originals stay visible is so someone can see what you
     said before you knew the answer.
     ───────────────────────────────────────────────────────────────────────── -->
