# Question catalog

Each entry stores the question and the already-mapped effect of each answer. This way the Translator does not reinterpret the user's answer in each project, and the same decision produces the same result in any project.

## Rules

- **Ask only when both conditions hold:** the prototype does not answer it, and the answer changes data, permissions or flow. Limits, tie-breaks and texts are details: they become [I] assumptions with a named constant.
- **Cap:** 3 questions per feature, not counting the importance question. Project decisions (login, single vote) are made once and apply to every feature. Anything beyond the cap becomes an [I] assumption.
- **Format:** yes/no, in business language, with a suggested answer and the effect of each answer in one line. No technical terms.
- **Answers:** "Y", "N" or "?" (or the equivalent in the user's language, e.g., "S", "N" or "?" in Portuguese). "?" adopts the suggested answer and the decision stays [I], not [V].
- **Chaining:** `open_creation` = Yes leads to `moderation`. `single_vote` depends on `login`: if login is No, warn that the per-person limit becomes approximate and ask for confirmation.

## How to present

A single table with all the questions, numbered. Ask for the answer in one line.

~~~
| # | Question | Suggested | What changes |
|---|---|---|---|
| 1 | To vote or post, does the person need to sign in with an account? | Yes | Yes: votes and posts are tied to the account. No: open access, and the per-person vote limit becomes unreliable. |
| 2 | Do wall posts need the organizer's approval before they appear? | Yes | Yes: posts stay pending until approved. No: they are published immediately. |

Answer in one line, for example: 1 Y, 2 N.
~~~

## How to record

Each answer becomes a line in "Decisions" (project) or in the feature's rules, with origin [V] or [I]. Then update what it affects (entities, profiles, rules, acceptance criteria) and say in one line what changed.

## Free questions

If the prototype leaves a gap the catalog does not cover (e.g., "does the wall need its own screen for projection on the big screen?"), ask the question in the same format and count it toward the cap. If a free question appears in two or more projects, propose adding it to this catalog and deliver the entry ready, in the format below.

## Entries

### importance
- **When:** for each feature.
- **Question:** Is {feature} important for the first version?
- **Suggested:** Yes.
- **If Yes:** it goes into the prompts of this round.
- **If No:** it stays in the specification as "later" and generates no prompt now.
- **Scope:** feature.

### login
- **When:** the app has votes, posts or any action tied to a person.
- **Question:** To {action}, does the person need to sign in with an account?
- **Suggested:** Yes.
- **If Yes:** user accounts; the action is tied to the account; profiles enforced in the database.
- **If No:** open access; per-person limits become unreliable.
- **Scope:** project.
- **Skill:** `profiles-and-access`.

### single_vote
- **When:** there are votes or likes on items.
- **Question:** Can each person vote only once on each {item}?
- **Suggested:** Yes.
- **If Yes:** one vote per person and item, enforced in the database; withdrawing the vote is allowed [I].
- **If No:** unlimited votes; the ranking starts measuring insistence, not preference.
- **Scope:** project.
- **Skill:** `suggest-vote-ranking`, `voting-with-cycle`.

### open_creation
- **When:** the audience suggests or registers {items}, or the prototype does not say who registers them.
- **Question:** Can any participant create new {items}?
- **Suggested:** Yes, if the prototype talks about "suggesting" or "creating"; No, if the items are a known list (e.g., the dishes in the contest).
- **If Yes:** participants create items; ask `moderation`; duplicate checking comes in.
- **If No:** only {admin_profile} registers items; the audience only views and votes.
- **Scope:** feature.
- **Skill:** `suggest-vote-ranking`.

### moderation
- **When:** the audience publishes something others see, and the prototype does not mention approval.
- **Question:** Do {items} need to be approved by {admin_profile} before they appear to others?
- **Suggested:** Yes, if the content goes to a public screen; No otherwise.
- **If Yes:** the item gets a status (pending, approved or rejected); a moderation queue for {admin_profile}; the author sees "Awaiting approval"; criteria for the pending state are added.
- **If No:** immediate publication, no status.
- **Scope:** feature; it becomes a project decision if the pattern repeats across several features.
- **Skill:** `public-content`.

### voting_cycle
- **When:** there is a poll or vote with predefined options.
- **Question:** Does {admin_profile} open and close each {vote}?
- **Suggested:** Yes.
- **If Yes:** draft, open and closed states, with open and close buttons.
- **If No:** the vote closes on its own at a set time; a deadline field is added.
- **Scope:** feature.
- **Skill:** `voting-with-cycle`.

### partial_results
- **When:** there is a ranking or vote count.
- **Question:** Does the audience see the results while voting is open?
- **Suggested:** Yes, if a requirement or story mentions a ranking visible to the audience; No, if only the organizer follows it.
- **If Yes:** public ranking or count, updated in real time.
- **If No:** only {admin_profile} sees it until closing; the audience sees the final result afterwards.
- **Scope:** feature.
- **Skill:** `suggest-vote-ranking`, `voting-with-cycle`.

## Format of a new entry

~~~
### {id}
- **When:** {situation that triggers the question}
- **Question:** {sentence with {fields}}
- **Suggested:** {Yes | No, with the rule for choosing}
- **If Yes:** {effect on data, permissions, flow and criteria}
- **If No:** {effect}
- **Scope:** project | feature
- **Skill:** {skill that implements the pattern, if any}
~~~
