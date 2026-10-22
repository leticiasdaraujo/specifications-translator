# Lovable kit generator

Produces what the user sets up in Lovable before any development prompt: workspace Knowledge, skills and project Knowledge. It is one of the two parts of the Translator that depend on the target tool (the other is `lovable-prompt-generator.md`).

## Where each thing goes

Use the criterion from Lovable's own documentation:

- Applies to every project and every message → **workspace Knowledge**.
- Matters only for one kind of task (suggestion and voting, audience content) → **workspace skill**.
- Applies to this project in every message (profiles, entities, visual identity) → **project Knowledge**.
- Belongs to this delivery → **prompt**.

No rule lives in two places. The skill says how to do it; the prompt says what to do.

## Workspace Knowledge (base)

Deliver this text when the workspace has no Knowledge yet. If it has, ask for the current text and propose only additions. It is a single Knowledge, shared by all projects in the workspace: keep it short and generic. Write it in the user's language; the interface language line states that language.

~~~
# Conventions (apply to every project in this workspace)

## Language and tone
- Interface in {user's language}, with short, direct sentences and no technical jargon.

## Interface states
- Every screen that fetches data has three states: empty (a useful message and a suggested action), loading (a progress indicator) and error (a clear message and a "Try again" button). Never leave a blank screen or generic text such as "No data".
- A slow action shows progress and disables the button until it finishes.

## Interaction
- Every user action has immediate feedback on screen (vote counted, item added).
- Filters and search: applying them updates the list immediately; "Clear" returns to the initial state; an empty result offers "Clear filters".
- An action that deletes or cancels asks for confirmation.

## How to work
- Do only what the prompt asks. Do not change existing screens, tables or rules that the prompt does not mention; if the request conflicts with something that already exists, warn before changing it.
- Adjustable values (limits, deadlines, fixed texts) live in named constants, in a single place.
- Permissions are enforced in the database, not only by hiding buttons in the interface.

## When finishing each prompt
- List the acceptance criteria (A1, A2...) as met or not met and say what was left out.
~~~

## Skills

### Rules

- **One function per skill.** A skill that does too much loads at the wrong time.
- **The description is the trigger.** Start with "Use when...", say what it covers and what it does not, and point to the neighboring skill when there is one (e.g., "do not use for fixed polls; use voting-with-cycle").
- **Instructions as a short playbook.** Small sections, direct sentences, concrete values. Where the prompt is silent, the skill provides the default; where the prompt says something, the prompt wins.
- **Do not repeat the Knowledge.** Interface states, constants and database permissions are already there.
- **Name:** lowercase letters, numbers and hyphens, at most 64 characters, no hyphen at the beginning or end. The name does not change after creation.
- **Deliver only the skills for the patterns the specification uses** and that do not yet exist in the workspace. For a pattern that is not in this library, write a new one with the same template and add the corresponding entry to the question catalog.

### How to deliver

One block per skill, with the three fields of the Lovable form (name, description, instructions), to paste in Workspace settings → Skills → Add → Write manually. If the user prefers to import, and a file tool is available, deliver one .zip per skill, each with a SKILL.md at the root (frontmatter with name and description, followed by the instructions). Only a workspace owner or admin can add skills. Adding them through settings does not consume credits.

### Base library

Generic on purpose: they apply to any project in the workspace. Write the instructions in the user's language when delivering them.

#### suggest-vote-ranking

~~~
Name: suggest-vote-ranking

Description: Use when creating or changing a feature in which people suggest items (songs, ideas, categories, questions), vote on other people's items and see a ranking of the most voted. Do not use for polls with fixed options created by an administrator (use voting-with-cycle) or for feed posts (use public-content).

Instructions:
# Suggestion, voting and ranking
Follow the prompt's rules. Where it is silent, use the defaults below.

## Data
- Item: text, author, created_at (and status, if the prompt requires moderation).
- Vote: item, person, created_at. The count comes from the votes; do not store an editable number.

## Voting
- A person votes once per item, enforced in the database with a uniqueness constraint, not just by disabling the button.
- Whoever voted can withdraw the vote; the count follows.
- The button responds immediately and reverts if the server refuses.

## Suggestion
- Before creating, compare the text (without accents, case differences or extra spaces) with existing items and offer to vote on the similar item. This avoids a split ranking.
- The text length limit lives in a named constant.

## Ranking
- Descending order of votes; ties broken by the oldest item. Show position and count.
- The ranking reorders as votes arrive.

## Typical acceptance criteria
- Suggesting creates the item with 0 votes, and it appears in the ranking.
- Voting adds 1; voting again on the same item does not add.
- Withdrawing the vote subtracts 1.
- Tied items appear from oldest to newest.
~~~

#### public-content

~~~
Name: public-content

Description: Use when people publish content that others see (photos, comments, wall posts, reviews), with or without moderation, or when the content is shown on a public screen. Do not use for votes and rankings (use suggest-vote-ranking).

Instructions:
# Content published by the audience
Follow the prompt's rules. Where it is silent, use the defaults below.

## Data
- Post: author, text, media (if any), status, created_at.
- Status: if the prompt requires moderation, the post starts as pending and becomes approved or rejected. Without moderation, it starts as approved.

## Who sees what
- People in general see only approved posts.
- The author sees their own in any status, with a label (e.g., "Awaiting approval").
- Moderators see everything and decide.

## Moderation (when present)
- Queue from oldest to newest, with approve and reject in one tap.
- A rejected post disappears for others; the author still sees its status.

## Upload
- File types and maximum size, and the text character limit, in named constants. Visible character counter.
- Show upload progress. If it fails, keep the file selected for a new attempt.

## Feed
- From newest to oldest. If the project has real-time updates (see the project Knowledge), an approved post enters at the top of everyone's feed without reloading.
- Load in pages when there are many posts.

## Public screen (when the prompt asks for it)
- Its own route, full screen, without controls, with large text, automatic updates and only approved posts.

## Typical acceptance criteria
- With moderation: a new post stays pending and only the author sees it; once approved, it appears for everyone.
- An upload failure keeps the file selected.
- Text above the limit disables the publish button.
~~~

#### profiles-and-access

~~~
Name: profiles-and-access

Description: Use when the project needs sign-in, sign-up, user profiles (e.g., participant, organizer) or control over who can see and do what. Do not use for rules of a single feature; those stay in its prompt.

Instructions:
# Profiles and access
Follow the prompt's decisions (login required or not, existing profiles).

## Accounts
- Sign-up and sign-in by the method indicated in the prompt, a persistent session, a sign-out button and password recovery when there is a password.
- Whoever signs up gets the default profile, the one with the least power.

## Profiles
- Store the profile in its own table, separate from the person's record, so that no one can change their own profile.
- Promotion to a more powerful profile only by someone who already has that power.

## Routes
- A screen that requires an account leads to sign-in and then back to the requested screen.
- Areas of a profile (e.g., moderation) deny access to the others, with a clear message.

## Typical acceptance criteria
- Without signing in, opening a protected screen leads to sign-in and returns to the screen afterwards.
- A participant cannot access the organizer's area.
- A participant cannot promote themselves to organizer.
~~~

#### voting-with-cycle

~~~
Name: voting-with-cycle

Description: Use when an administrator creates polls or votes with predefined options that the audience votes on at controlled moments (open, close, show results). Do not use for open suggestions from the audience (use suggest-vote-ranking).

Instructions:
# Voting with a cycle
Follow the prompt's rules. Where it is silent, use the defaults below.

## Data
- Poll: title, status (draft, open, closed) and, if the prompt asks for closing at a set time, a deadline.
- Option: poll, text. Vote: poll, option, person.

## Cycle
- Only an open poll accepts votes. Closing freezes the result.
- Manual control: the administrator opens and closes with a button. Time-based control: it opens and closes on its own at the deadline.
- There may be several polls; the audience sees the open ones highlighted.

## Voting
- One vote per person per poll, enforced in the database. Changing the option only if the prompt allows it.

## Results
- Visibility according to the prompt: partial for everyone, or only for the administrator until closing.
- Show count and percentage per option and indicate ties.
- If the project has real-time updates (see the project Knowledge), opening, closing and counts reach everyone without reloading.

## Typical acceptance criteria
- A draft or closed poll does not accept votes.
- Voting again in the same poll does not add.
- On closing, the final result appears and no longer changes.
~~~

## Project Knowledge

Generated from the PROJECT layer of the specification, once per project. Write it as if introducing the project to a new person on the team: specific (colors with codes, rules with values), short (about 60 lines at most) and without anything already in the workspace Knowledge or that belongs to a single feature.

~~~
# {App name}

## Context
{2 to 3 lines: what it is, for whom, where and when it is used}

## Profiles and permissions
- {Profile}: can ..., cannot ...

## Entities
- {Entity}: {main fields}; relations with {other entities}.

## Project rules
- {confirmed project decisions: login, single vote...}

## Technical constraints
- {e.g., real-time updates in feeds, rankings and results; mainly used on mobile}

## Visual identity
- Colors: {primary #..., secondary #...}. Typography: {...}. Tone: {...}.
~~~

When a project decision changes, deliver only the changed section and say which section to replace.

## How to deliver the kit

In this order: workspace Knowledge, skills, project Knowledge (Project settings → Knowledge). Each item in a code block, with a line before it saying where to paste it. At the end, ask the user to confirm they pasted everything before moving on to the prompts.

## Source

Lovable documentation consulted in October 2026: skills (docs.lovable.dev/features/skills), glossary (docs.lovable.dev/glossary) and knowledge FAQ (lovable.dev/faq/development/knowledge). Check whether the interface has changed before giving menu instructions.
