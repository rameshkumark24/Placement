# Day 100B

**L-100B** · ➕ **Code review, both sides** — what to look for, and ⭐⭐ how to say it

**Time:** 2–3 hrs · **Mode:** NEW · ➕ **an addition to the roadmap**

> ⭐⭐ **The sentence to own: code review is not quality control performed on a person — it is the
> cheapest mechanism a team has for spreading knowledge, applying design pressure and sharing
> ownership, and catching defects is only one of the four things it does.**
>
> ⭐ Day 088 covered PRs from the author's side. **Today is the reviewing itself**, and it is the skill
> that most visibly separates a mid-level engineer from a junior one.

---

# Part 1 — ⭐ What review is actually for

```
⭐⭐ FOUR PURPOSES, AND DEFECT-CATCHING IS ONLY ONE:
⭐  1. ⭐ CATCH DEFECTS — real, but ⭐⭐ humans are worse at this than tests and
⭐       types (Day 099). ⭐ Let the machine take the mechanical half.
⭐  2. ⭐⭐ SPREAD KNOWLEDGE — after review, at least two people understand that
⭐       code. ⭐⭐ THAT IS BUS-FACTOR INSURANCE, and it is worth more than the
⭐       bugs found.
⭐  3. ⭐⭐ DESIGN PRESSURE — knowing someone will read it changes what you write,
⭐       before the review even happens.
⭐  4. ⭐ SHARED OWNERSHIP — "we merged this" rather than "her code broke".
⭐  ⇒ ⭐⭐ WHICH IS WHY "LGTM" ON A 900-LINE DIFF FAILS ALL FOUR AT ONCE, not
⭐       just the first (Day 088).
```

---

# Part 2 — ⭐⭐ Reviewing: the order matters

```
⭐⭐ REVIEW IN THIS ORDER, AND STOP AT THE FIRST LEVEL THAT HAS A REAL PROBLEM:
⭐
⭐ 1. ⭐⭐ WHY — read the description first. ⭐ If you cannot tell what problem
⭐      this solves, that is comment number one and everything else is premature.
⭐ 2. ⭐⭐ DESIGN — is this the right shape? Right layer (Day 090)? Should it
⭐      exist at all? ⭐⭐ THE MOST EXPENSIVE COMMENTS TO ACT ON, SO THEY MUST
⭐      COME FIRST. A design comment after someone has polished 400 lines is
⭐      infuriating and usually gets deferred forever.
⭐ 3. ⭐ CORRECTNESS — does it do what it says? Edge cases, errors, concurrency.
⭐ 4. ⭐⭐ TESTS — ⭐⭐ REVIEW THEM AS CAREFULLY AS THE CODE. Do they test
⭐      behaviour (Day 092)? Would they fail if the code were wrong?
⭐ 5. ⭐ READABILITY — names, structure, the next reader (Day 089).
⭐ 6. ⭐ NITS — style the linter did not catch. ⭐⭐ LAST, AND MARKED AS OPTIONAL.
⭐
⭐ ⚠️ ⭐⭐ STARTING AT 6 IS THE CLASSIC JUNIOR REVIEW: twelve comments about
⭐    naming on a PR whose design is wrong. ⭐ It also uses up all the goodwill
⭐    before the important comment arrives.
```

```
⭐⭐ LABEL EVERY COMMENT WITH ITS WEIGHT. This one convention removes most
⭐   review friction, because the author no longer has to guess what blocks:
⭐     ⭐⭐ blocking:  this will lose data if the retry fires twice
⭐     ⭐   question: what happens when `items` is empty?
⭐     ⭐   suggestion: a dict lookup would read better here — up to you
⭐     ⭐   nit: spelling
⭐     ⭐   praise: nice — this is much clearer than what it replaced
⭐   ⇒ ⭐⭐ AND USE "APPROVE WITH COMMENTS" LIBERALLY. Blocking a PR on nits
⭐        costs the team a full round-trip — often a day — for something the
⭐        author would have fixed anyway.
```

---

# Part 3 — ⭐⭐ The checklist, drawn from the whole stage

```
⭐⭐ WHAT I ACTUALLY LOOK FOR — and note that every line is a day you have done:
⭐
⭐ CORRECTNESS
⭐   □ ⭐⭐ errors: expected vs unexpected, caught at the right layer      Day 060
⭐   □ ⭐⭐ EVERY network/db call has a TIMEOUT                            Day 060
⭐   □ retries are bounded, with backoff — ⭐ and the operation is IDEMPOTENT
⭐   □ ⭐ mutable default argument                                        Day 025
⭐   □ ⭐ `except` without `from`, or a bare `except:`                     Day 059
⭐   □ float used for money                                              Day 041
⭐   □ ⭐⭐ a naive datetime, or a stored offset instead of a zone         Day 076
⭐
⭐ CONCURRENCY
⭐   □ ⭐⭐ a BLOCKING call inside `async def`                             Day 072
⭐   □ ⭐⭐ a LOCK HELD ACROSS I/O                                          Day 066
⭐   □ ⭐ a bare `create_task` with no reference kept                      Day 071
⭐   □ shared mutable state between workers                              Day 067
⭐
⭐ DATA
⭐   □ ⭐⭐ AN N+1 QUERY                                                    Day 100A
⭐   □ ⭐ a query with no index on its filter column                       Stage 5
⭐   □ ⭐⭐ a migration that LOCKS a big table, or is not backward-compatible  Day 021
⭐   □ ⭐ unbounded growth — a list, a cache, a query with no LIMIT
⭐   □ ⭐ a join that could fan out                                        Day 100
⭐
⭐ SECURITY
⭐   □ ⭐⭐ A SECRET IN THE DIFF                                            Day 085
⭐   □ ⭐ string-interpolated SQL, or `shell=True`                         Day 076
⭐   □ ⭐⭐ PII or a token in a LOG LINE                                    Day 099A
⭐   □ ⭐ authorisation checked, not just authentication                   Day 019
⭐
⭐ TESTS
⭐   □ ⭐⭐ WOULD THIS TEST FAIL IF THE CODE WERE WRONG? ⭐ (assertion-free
⭐     tests, over-broad `pytest.raises`, mocks asserting on themselves)  Days 095, 098
⭐   □ ⭐ the failure paths are tested, not only the happy one            Day 094
⭐   □ ⭐ tests are independent and deterministic                         Day 092
⭐
⭐ ⇒ ⭐⭐ THE SINGLE HIGHEST-VALUE QUESTION ON THE WHOLE LIST: "WHAT HAPPENS IF
⭐     THIS IS CALLED TWICE?" ⭐ It finds missing idempotency, double charges,
⭐     duplicate emails and races, and it costs six words.
```

---

# Part 4 — ⭐⭐ How to say it

| ⚠️ Instead of | ⭐⭐ Say |
|---|---|
| "You forgot the null check." | ⭐ "This will raise if `user` is `None` — is that reachable here?" |
| "This is wrong." | ⭐ "I think this double-charges when the retry fires. Am I reading it right?" |
| "Why did you do it this way?" | ⭐⭐ "What made you choose X over Y? I might be missing a constraint." |
| "Use a dict." | ⭐ "suggestion: a dict lookup here would drop the loop — your call." |
| *(silence on the good parts)* | ⭐⭐ "praise: this error message is genuinely useful at 3 a.m." |

```
⭐⭐ THREE RULES THAT DO ALMOST ALL THE WORK:
⭐  1. ⭐⭐ REVIEW THE CODE, NOT THE PERSON. "This function does X" not "you did
⭐       X". ⭐ It is a small change and it removes defensiveness entirely.
⭐  2. ⭐⭐ ASK, DO NOT ASSERT — when you are unsure, and you are unsure more often
⭐       than it feels. ⭐⭐ A question costs nothing if you are wrong and lands
⭐       just as hard if you are right. ⭐ "Is that reachable here?" beats
⭐       "this is a bug" when it turns out to be guarded upstream.
⭐  3. ⭐ SAY WHAT IS GOOD, EXPLICITLY. ⭐⭐ It calibrates — the author learns what
⭐       to do more of, which is information no criticism carries. ⭐ It is free.
⭐
⭐ ⭐⭐ AND THE ONE THAT IS PURE INFORMATION: "I DO NOT UNDERSTAND THIS."
⭐   That is not an admission — ⭐⭐ IT IS DATA. If the reviewer cannot follow it
⭐   with the context fresh, nobody will in six months. ⭐ Say it plainly.
```

```
⭐⭐ WHEN TO STOP COMMENTING AND GO AND TALK: THE THREE-ROUND RULE.
⭐   If the same point has gone back and forth three times, the medium is the
⭐   problem. ⭐ A fifteen-minute call resolves what six more comments will not,
⭐   ⭐⭐ and you write the OUTCOME back into the PR so the decision is recorded
⭐   (Day 101's ADR, at small scale).
⭐   ⇒ ⭐ likewise: a disagreement about DESIGN belongs in a conversation before
⭐        the code is written, not in a comment thread after.
```

---

# Part 5 — ⭐ Receiving review, and the timing rule

```
⭐⭐ AS THE AUTHOR:
⭐   · ⭐⭐ SELF-REVIEW FIRST on the platform. ⭐ You will find something every
⭐        time, and it costs ninety seconds (Day 085).
⭐   · ⭐ RESPOND TO EVERY COMMENT — even "done". ⭐⭐ An unanswered comment
⭐        leaves the reviewer unsure whether it was seen or dismissed.
⭐   · ⭐⭐ PUSH BACK WITH REASONS when you disagree. "I kept the loop because
⭐        the dict version allocates per row in a hot path" is a good outcome —
⭐        ⭐⭐ REVIEW IS A CONVERSATION, NOT AN INSPECTION, and a reviewer who is
⭐        never contradicted is not being helped either.
⭐   · ⭐ SEPARATE the disagreement from the change: if it is not blocking,
⭐        take the note and open a follow-up.
⭐   · ⭐⭐ IT IS NOT ABOUT YOU. ⭐ The strongest signal of seniority in a PR
⭐        thread is someone saying "good catch" to a comment on their own code.
```

```
⭐⭐ AND THE TEAM RULE THAT MATTERS MOST, WHICH IS ABOUT LATENCY RATHER THAN
⭐   QUALITY: REVIEW WITHIN A FEW HOURS.
⭐   ⇒ ⭐⭐ A PR SITTING FOR A DAY COSTS FAR MORE THAN THE REVIEW TAKES: the
⭐        author has context-switched away, branches diverge (Day 088's product
⭐        rule), and the author starts a second PR stacked on the first.
⭐   ⇒ ⭐ so: review before starting your own new work, not after.
⭐   ⇒ ⭐⭐ AND THE CORRESPONDING AUTHOR RULE: KEEP IT SMALL ENOUGH THAT A REVIEW
⭐        FITS IN TWENTY MINUTES. ⭐ The two rules are the same rule.
```

---

## Common mistakes

| Mistake | Correction |
|---|---|
| ⭐ Starting with nits | ⭐⭐ Design first — the expensive comments must come first. |
| ⭐⭐ **"LGTM" on a huge diff** | ⭐⭐ Fails all four purposes, not just defect-catching. |
| Not labelling comment weight | ⭐ The author has to guess what blocks. |
| ⭐ Blocking on style | ⭐⭐ Approve with comments; the linter owns style anyway. |
| ⭐ Reviewing code but not tests | ⭐⭐ Ask: would this test fail if the code were wrong? |
| "You forgot…" | ⭐ Review the code, not the person. |
| Asserting when unsure | ⭐⭐ Ask. Costs nothing if wrong, lands just as hard if right. |
| ⭐ Never saying what is good | ⭐ Praise calibrates; criticism cannot. |
| Hiding "I don't understand this" | ⭐⭐ That is data about the next reader. |
| ⭐ Six rounds on one point | ⭐ Three-round rule — go and talk, then record the outcome. |
| ⭐⭐ **Leaving a PR for a day** | ⭐⭐ Costs more than the review does. Review before starting new work. |
| Not responding to a comment | ⭐ The reviewer cannot tell if it was seen. |
| Taking it personally | ⭐ "Good catch" on your own PR is the seniority signal. |

---

## Interview questions

**Q: What do you look for in a code review?**
> I go in order — why, design, correctness, tests, readability, nits — and stop at the first level with
> a real problem, because design comments are the most expensive to act on and have to arrive before
> someone has polished four hundred lines. Concretely, the things I check every time are timeouts on
> every network call, what happens if this is retried, N+1 queries, blocking calls inside `async def`,
> locks held across I/O, secrets or PII in the diff, and whether the migration is backward-compatible.
> And I review the tests as carefully as the code, with one question: would this test fail if the code
> were wrong?

**Q: What's the single most useful question to ask in a review?**
> "What happens if this is called twice?" It costs six words and it finds missing idempotency, double
> charges, duplicate emails, retry bugs and races. Most systems eventually retry something — a client,
> a queue, a load balancer — so anything that isn't safe to repeat is a latent incident.

**Q: How do you give feedback that lands?**
> Review the code, not the person — "this function raises if `user` is None", not "you forgot the null
> check" — because it removes defensiveness for free. Ask rather than assert when I'm unsure, and I'm
> unsure more often than it feels: a question costs nothing if I'm wrong and lands just as hard if I'm
> right. Label the weight of each comment — blocking, question, suggestion, nit — so the author isn't
> guessing what holds up the merge. And say what's good explicitly, because praise calibrates and
> criticism can't.

**Q: What if you don't understand the code?**
> I say so plainly, and I treat it as data rather than as an admission. If I can't follow it with the
> context fresh and the author available, nobody will in six months — and that's a finding about the
> code, not about me. Usually it produces either a better name, a comment explaining a genuine
> constraint, or the discovery that the function is doing two things.

**Q: How quickly should reviews happen?**
> Within a few hours, and I'd review before starting my own new work rather than after. A PR sitting
> for a day costs far more than the review takes: the author has context-switched away, the branch
> diverges from main — and merge pain grows with the product of both sides' changes — and they start a
> second PR stacked on the first. The author-side version of the same rule is keeping the PR small
> enough that a review fits in twenty minutes. They're really one rule.

---

## Mini task

1. ⭐⭐ Review a real PR — an open-source one if you have no team. **Write the comments in order:
   design, correctness, tests, readability, nits.**
2. ⭐ Label every comment with its weight. Count how many were actually blocking.
3. ⭐⭐ Rewrite three "you" comments as "the code" comments.
4. ⭐⭐ Rewrite two assertions as questions. **Check whether either turns out to be guarded upstream.**
5. ⭐ Add one genuine praise comment.
6. ⭐⭐ Take the Part 3 checklist and run it against your own most recent PR. **Write down every hit.**
7. ⭐⭐ Ask "what happens if this is called twice?" of three functions in your project. **Note the
   answers.**
8. ⭐ Find a test in a PR that would still pass if the code were wrong.
9. ⭐⭐ Self-review your next PR before requesting a human, and **record what you found.**
10. ⭐ Find a comment thread (yours or public) that ran past three rounds. **Say what should have
    happened instead.**
11. ⭐⭐ Turn the Part 3 checklist into a `PULL_REQUEST_TEMPLATE.md` for one of your repos.

---

# 🚪 Exit questions

Answer aloud, no notes.

1. Name the four purposes of review, and which is over-weighted.
2. Give the review order, and why design must come before nits.
3. What does labelling comment weight solve?
4. When should you approve with comments rather than block?
5. Give six items from the correctness/concurrency checklist.
6. What is the highest-value question, and what does it find?
7. What question do you ask of a test?
8. Give the three rules for how to say it.
9. Why is "I don't understand this" data?
10. What is the three-round rule, and what do you do afterwards?
11. Two obligations of the author receiving review.
12. Why does review latency cost more than review time?

## 🎙️ Articulation drill

Record two minutes: **"What do you look for when you review code?"**

⭐ **Lead with the order, because it shows you have actually done this:** **"I go in order — why,
design, correctness, tests, readability, nits — and I stop at the first level with a real problem.
Design comments are the most expensive to act on, so they have to come first; twelve naming comments
on a PR whose shape is wrong is the classic bad review, and it burns all the goodwill before the
important comment arrives."**

⭐⭐ **Then get concrete, because the generic answer is worthless here:** *"the things I check every
time are timeouts on every network call, whether a retry is safe, N+1 queries, blocking calls inside
an `async def`, locks held across I/O, and anything that could put a secret or PII into a log line. And
I review the tests as carefully as the code, with one question: would this test fail if the code were
wrong? Assertion-free tests and mocks that only assert on themselves are common and they cover
nothing."*

⭐⭐ **Then the one-line habit:** *"the highest-value question I know is 'what happens if this is
called twice?' — six words, and it finds missing idempotency, double charges, duplicate emails and
races. Something in the system will eventually retry, so anything not safe to repeat is a latent
incident."*

⭐ **Close on how, not just what — and this is what marks out seniority:** *"I try to review the code
rather than the person, and to ask rather than assert when I'm unsure, because a question costs
nothing if I'm wrong. I label whether a comment blocks, so the author isn't guessing. And I say what's
good explicitly — praise calibrates, criticism doesn't. Also I try to review within a few hours: a PR
sitting for a day costs the team far more than the review itself does."*

---

**Previous:** [Day 100A](Day-100A.md) · **Tomorrow:** [Day 101](Day-101.md) — 🚪 **documentation,
ADRs, and the Stage 2 exit gate**
