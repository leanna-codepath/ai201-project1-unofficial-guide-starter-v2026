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

For at least 4 of my 5 test questions, the retrieved chunks include one that
contains the answer.

**Why this target:**
4 out of 5 of my questions have answers which can be found in a specific document,
whose title is generally refrenced in the question itself. This should make it easier
to find. The fifth question involves compiling answers from multiple documents, and it 
isn't obvious which documents those are. Additionally, these answers may not be obvious 
in each document. As such, I expect this question to fail. If all five questions passed,
it would imply my questions are too simple, rather than my chunking being well-formed.

---

## 2. Every answer names a source

Every answer the system produces names at least one source document.

**Why this target:**
Asking the system to name at least one source document for every answer it gives ensures
its grounded. If the system couldn't name a source, it implies that the model is answering
from what it already knows, which may not be the correct answer. If this is the case,
then something needs to be fixed in the system's build.

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
When presented with a question my documents clearly don't cover, the relevance gate
should be good enough that it will return "I don't have enough information about that"
rather than attempt to answer the question with irrelevant information. Allowing one 
question to pass ensures the system is not too strict, especially for questions that
might hover near the relevance gate boundary.

---

## 4. Chunks come from distinct sections

For at least 4 out of 5 of my questions, the sampled chunk starts with a title and a section title of
format "## <Section Title>". 

<!-- YOU WRITE THIS ONE.

     How would you know if your chunks were the right size? Name something
     countable or observable.

     Examples of the right shape — don't copy these, they should come from
     what you actually saw in Milestone 3:
       - "At least 4 of 5 sampled chunks read as a complete thought, with no
          sentence cut in half at either end."
       - "No chunk is shorter than 200 characters, since anything below that
          in my corpus turned out to be a heading with no content under it." -->



**Why this target:**
I picked this target because each of the 14 documents in my corpus is formatted 
with a title, beginning with "#", and sections beginning with "##". I know if my
chunks are the right size if I can clearly tell which section the system's answers
are from. Without this information, it is difficult to tell where the system is getting
its info. This is especially true for my criteria questions, as 4 out of 5 can be clearly
sourced from a specific document. The fifth, and most complex, question may fail this criteria.
         > **Revised in unit 2:** For at least 5 of 5 questions, the sampled chunk starts with a
         > title and a section title of format "<Title>, <Section Title>". 
         >
         > **Why revised:**
         > I realized that multiple of the guide documents shared section headings, which
         > could make it difficult for me to determine exactly which document the specific
         > section came from. This has been revised with the addition of a Title in the chunk.



---

## 5. All information is located from the correct documents
For 4 out of 5 of the answers, the retrieved chunks are sourced from the correct
documents.

<!-- YOU WRITE THIS ONE TOO.

     Pick something you actually care about getting right. It could be about
     speed, about refusals, about a particular kind of question your corpus
     handles badly, about source attribution being correct rather than merely
     present — anything, as long as it names a number or an observable
     outcome. -->



**Why this target:**
For 4 out of 5 of the questions, their answers can be sourced back to a single 
document whose title is clearly found in the question. Answers coming from other
documents may be subject to scrutiny. There is leeway for the last question, as 
it spans multiple documents, meaning the system might have difficulty narrowing
down which documents are the correct source.


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
