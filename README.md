# The Unofficial Guide

<!-- Leanna Persaud - city_guides. -->

---

# Unit 1

## What This Does

This system focuses on a corpus involving 14 travel guides about a region with 9 different towns,
along with general guides on accessibility, eating, regional transportation, seasons, and walking. The system
answers questions about the towns in this region, such as where to eat, when do buses run, which locations may
have cheaper accomodations, etc.

## Chunking Strategy

**Chunk size:** 1000
**Overlap:** 120


I picked these numbers because each guide is formatted with a title and distinct sections, marked with a "#" 
and a "##" respecitively. Additionally, each section has a maximum of 1000 characters in it, including their
section headers. This gave me the idea for the chunk size and to fashion the chunks after the sections. I
initially was going to label chunks just by their headers before I recognized that many of the guides have
identical header names, making it difficult to determine which guide a section was coming from. To compensate
for this, I also added the title at the top of each document to the chunk labels.

Using this method, I generated more, smaller chunks. But I could also guarantee that all these chunks included
relevant information grouped together and I could tell where they were sourced from. Additionally, these chunks
no longer included sentences or paragraphs that were cut off in the middle or too short.

## Sample Chunks

**Chunk 1** — source: `guide_accessibility.md#0` — produced by: `chunker.py::split_documents`

```
Getting around the region with limited mobility, Intro

An honest assessment rather than a promotional one. Some of these places are
difficult and it is better to know in advance.
```

**Chunk 2** — source: `guide_corry_vale.md#6` — produced by: `chunker.py::split_documents`

```
Corry Vale, When to go

May to September. Outside those months the pub in the third village closes, the farm shop reduces its hours, and several footpaths become genuinely boggy rather than merely wet. The road is not gritted above the second village and is impassable in snow.
```

**Chunk 3** — source: `guide_givens_mill.md#3` — produced by: `chunker.py::split_documents`

```
Givens Mill, Eat and drink

A tearoom attached to the mill, open 10 to 4 daily except Tuesdays, which sells bread made from the flour ground twenty metres away and is the reason most people come. One pub, food served lunchtimes and Thursday to Saturday evenings.
```

**Chunk 4** — source: `guide_kestrelford.md#6` — produced by: `chunker.py::split_documents`

```
Kestrelford, When to go

Late spring and early autumn. The Saturday market runs year-round but is much reduced from November to February. August is busy with walkers. The single-track approach road is genuinely difficult in snow and the town can be cut off for a day or two most winters.
```

**Chunk 5** — source: `guide_regional_transport.md#1` — produced by: `chunker.py::split_documents`

```
Getting around the region, The railway

The line runs along the river valley, connecting Brightwater to the regional
hub in 50 minutes. Eleven services a day on weekdays, six on Sundays. The line
north of Brightwater closed in 1963 and everything beyond it is bus or car.

Tickets are cheaper booked the day before than on the day, and considerably
cheaper than that booked a week ahead. There is no ticket office at
Brightwater station outside weekday mornings; the machine on the platform takes
cards only.

```

## Sample Answer

**Question:**
In what year did the pier in Pellew Sands catch on fire?

**Answer:**

```
(best distance 0.194, cutoff 0.6)

The pier in Pellew Sands was partly destroyed by fire in 1978 (guide_pellew_sands.md).

Sources retrieved: guide_pellew_sands.md
```

**My relevance cutoff:**

| Question | In corpus? | Best distance |
|---|---|---|
| How many shops does Elder Ness have? | Yes | 0.3657 |
| How often do buses run in Halden Bay in a day? | Yes | 0.2891 |
| In what year did the pier in Pellew Sands catch on fire? | Yes | 0.1944 |
| Does Brightwater or Corry Vale have cheaper accommodations? | Yes | 0.2557 |
| Which places are busiest in October? | Yes | 0.4777 |
| What is the capital of Mongolia? | No | 0.7850 |
| How do I change the oil in a diesel engine? | No | 0.8834 |
| Who won the 1994 World Cup? | No | 0.9351 |
| What is the recommended dosage of ibuprofen for a headache? | No | 0.8482 |
| How do I write a for loop in Rust? | No | 0.8419 |

Looking at these two groups of questions, the corpus question distances range between 0.19-0.47 while the
non-corpus questions range between 0.78-0.93. Seeing this, I decided to put my relevance cutoff 0.65.

## How I Used AI


**1.**
To put a number to my chunk size, I asked Claude to analyze the 14 documents and tell me what the longest section
character count was. It returned a specific number in the 700s, so I increased the chunk size to 1000 as a ceiling
to ensure no characters escaped.

**2.**
I asked Claude to look over my code for the section chunking and help me debug. It did as such, but made some
formatting changes that I didn't ask for, which I ignored.

---

# Unit 2


## Run Log — Before


| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunk contains the answer | 4 of 5 | 3/5 | 3/5 | 4/5 | MISSED |
| 2. Every answer names a source | 5 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 3. Gate stops out-of-corpus questions | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 4. Chunks come from distinct sections | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 5. All information is located from the correct documents | 4 of 5 | 4/5 | 4/5 | 4/5 | MET |


**Criterion 1** Scored manually by checking if each answer in the before log had the expected answer in the
`expects` list appeared in the answer. From the before log:

| Question | Run 1 | Run 2 | Run 3 |
|---|---|---|---|
| How many shops does Elder Ness have? | pass  | pass  | pass  |
| How often do buses run in Halden Bay in a day? | pass  | pass  | pass  |
| In what year did the pier in Pellew Sands catch on fire? | pass  | pass  | pass  |
| Does Brightwater or Corry Vale have cheaper accommodations? | fail  | fail  | pass  |
| Which places are busiest in October? | fail  | fail  | fail  |

**Criterion 2**  Read off the answers frpm the before log, produced by `generate.py::answer_from_chunks`.
Each of the answers in the before log names at least one source file.

```
Based on the provided documents, Elder Ness has one shop (guide_elder_ness.md and guide_eating.md).
```

**Criterion 3** Produced by `run_eval.py::check_out_of_scope`, text comes form the before log

| Out-of-scope question | Best distance | Gate |
|---|---|---|
| What is the capital of Mongolia? | 0.785 | refused |
| How do I change the oil in a diesel engine? | 0.883 | refused |
| Who won the 1994 World Cup? | 0.935 | refused |
| What is the recommended dosage of ibuprofen for a headache? | 0.848 | refused |
| How do I write a for loop in Rust? | 0.842 | refused |

**Criterion 4** The five chunks analyzed were produced by `chunker.py::split_documents` and each
can be seen to have a label in the form `<Title>, <Section Title>`.

```
Getting around the region, The railway

The line runs along the river valley, connecting Brightwater to the regional
hub in 50 minutes. Eleven services a day on weekdays, six on Sundays. The line
north of Brightwater closed in 1963 and everything beyond it is bus or car.
```

**Criterion 5** Scored manually by double checking the sources given by the answers in the
before log with the sources I expected.

```
- Sources retrieved: guide_eating.md, guide_elder_ness.md

Elder Ness has one shop (guide_elder_ness.md and guide_eating.md).
```


## Verdicts


| # | Criterion | Verdict | How I decided |
|---|---|---|---|
| 1 | Retrieved chunk contains the answer | MISSED | Two runs were labeled as 3/5, as the answers weren't what was expected. The target was 4/5 |
| 2 | Every answer names a source | MET | All answers named at least one source, reaching the target of 5/5 in each run. |
| 3 | Gate stops out-of-corpus questions | MET | All 5 of the our-of-corpus questions were refused, meeting the target of 4/5. |
| 4 | Chunks come from distinct sections | MET | Looking at the chunks produced, they everyone one of them meets the target, going beyond the 4/5 target.  |
| 5 | All information is located from the correct documents | MET | Each run resulted in a 5/5, hitting the target,. Criterion was revised and tightened|

**On the revision to criterion 5.**
This criterion initially stated that information should be sourced from the "correct documents",
but I realized that what I might believe to be the correct documents may not be the same for 
someone else. Additionally, there was no way to necessarily check what these correct documents
were. As such, I tightened the defnition to mean "documents with information that answers the
questions." This is less ambigiuous. With this new definition, all the answers actually meet the 
criterion, changing it from a 4/5 to a 5/5 for each run. Either case hits the target.

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

## The Improvement

**What I changed:**

**Why I picked it:**

<!-- Connect it to a specific diagnosis above in one sentence. If you can't,
     you picked a fix because it sounded impressive. -->

### Run Log — After

<!-- Same format, same five criteria, three runs each.
     `python run_eval.py --label after` -->

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunk contains the answer | 4 of 5 |  |  |  |  |
| 2. Every answer names a source | 5 of 5 |  |  |  |  |
| 3. Gate stops out-of-corpus questions | 4 of 5 |  |  |  |  |
| 4. | | | | | |
| 5. | | | | | |

**Did it help?**

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

## What I'd Do Differently

<!-- Knowing what you know now — which of your five criteria would you write
     differently, and why?

     Milestone 5. -->
