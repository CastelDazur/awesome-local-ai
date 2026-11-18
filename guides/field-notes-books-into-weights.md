# Field notes: teaching a 7B model two books

We trained a small model on two novels, one after the other, and measured what stuck, what it forgot and what it made up. Three things surprised us: forgetting turned into "I don't know" instead of lies, a time-saving shortcut cost 23 points of memory, and a clever confidence gate made things worse. Small proof of concept, one seed.

**Measured on:** RTX 5090 32 GB, Qwen2.5-7B-Instruct, LoRA rank 32, two books of about 9,000 words each, one seed. October 2026. Code, data and every number: [book-to-weights](https://github.com/CastelDazur/book-to-weights).

## How we kept the test honest

- **Counterfactual books.** A public-domain novel is already in the model's training data, so a right answer proves nothing. We rewrote each book so names, numbers and causes changed. A right answer can only come from our training.
- **The exam came first.** It was written from the facts before any training row existed, and exam questions too close to a training question were removed.
- **The judge was tested.** Before grading, it had to catch planted wrong answers (15 of 15 caught).

## What worked

- **Knowledge goes in.** On book A, direct questions went from 20% (untrained) to 90% (9 of 10).
- **Self-distillation fixed general knowledge.** Training on 420 general questions answered by the untouched model brought general knowledge from -7 points back to 100% in round 3.
- **Forgetting became abstaining.** After learning book B, the model more often said "the book does not say" about a book A fact than invented an answer. On the 15 original book A trap questions it invented nothing, although the larger 60-question set below still shows some.

## What cost us

While training on book B we mixed in the model's own book A answers, kept only when the judge marked them correct. To save time we had the judge check only 200 of the 386 answers. Retention of book A fell to **58%**: the half we skipped was the half it forgot. Checking all 386 and replaying the correct ones three times each, plus the book text, brought it back to **81%** (21 of 26).

## What did not work

A confidence gate (answer only when the model is sure) looked good on a validation set: abstention on unanswerable questions rose from 91% to 94%. On the real exam it silenced more correct answers than it removed made-up ones, and retention dropped from 58% to 50%. We did not use it.

## Where it falls short

Retention is 81% against a target of 95%. On the book A trap set the model still invented 8 answers out of 60 (target: at most 2). One seed, two books and small exams: read the intervals in the repository, not only the percentages.

## What we would reuse

1. Write the exam before the training data, and keep it frozen.
2. When you replay your own model's answers, check and replay all of them, not a sample.
3. Give the model examples where the right answer is "the source does not say this".
