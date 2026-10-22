# Prompt generator (Lovable)

Turns the improved specification into prompts that the user pastes, one at a time, into the project chat. It depends on the kit already being created (`lovable-kit-generator.md`): the prompt carries only what belongs to this delivery, because the rest is already in the Knowledge and the skills.

## Prompt types

- **Base prompt:** the app's foundation (profiles, access, base entities, navigation). No business features.
- **Feature prompt:** one per feature, in dependency order.
- **Fix prompt:** arises from an acceptance criterion that failed. It touches only that criterion.

## Plan

Before the first prompt, deliver the plan as a table:

~~~
| # | Prompt | Covers | Depends on | Skill |
|---|---|---|---|---|
| 1 | App base | access and profiles | | profiles-and-access |
| 2 | Collaborative Playlist | R1, R2, R3 | 1 | suggest-vote-ranking |
~~~

Order: base first; then by dependency; between independent features, by the importance the user stated. The first prompt that uses a pattern builds the reusable component and gives it a name. The following ones say "reuse component X created in prompt 2" instead of recreating it.

## Feature prompt template

Omit any section that would be empty.

~~~
PROMPT · {Feature name}
Skill: {skill-name}
Covers: {R..} · {US..}
Depends on: {base prompt | prompt N: what must exist}

GOAL
{1 to 2 sentences}

SCOPE
Includes: ...
Does not include: ...

DATA AND PERMISSIONS
{new fields; who can do what}

FLOW
1. ...
Exception: ...

RULES
- ... (constant CONSTANT_NAME, when it is an adjustable value)

STATES AND INTERACTION (beyond the global convention)
- ...

ACCEPTANCE
A1 (R..) Given ..., when ..., then ...
~~~

## Base prompt template

~~~
PROMPT · App base
Skill: profiles-and-access

GOAL
Prepare the foundation of {App name}: profiles, access, data model and navigation. No business features yet.

FOUNDATION
Profiles: {list, with what each one can do}
Access: {login required or not, according to the decision}
Base entities: {those used by more than one feature; specific fields are left to the prompts}
Navigation: {main screens and routes, empty, with the default empty state}

SCOPE
Does not include: business features.

ACCEPTANCE
A1 Given I am not signed in, when I open a screen that requires an account, then I am taken to sign-in and returned to the screen afterwards.
A2 Given I am {profile without the permission}, when I try to open the {profile with the permission} area, then access is denied.
~~~

## Fix prompt template

~~~
PROMPT · Fix · {Feature} · {A#}
Covers: {R..}

PROBLEM
Criterion {A#}: {criterion text}.
Expected: {...}. Observed: {what the user saw}.

SCOPE
Fix only this behavior. Do not change the rest of the feature or other screens.

ACCEPTANCE
{A# repeated}. Also confirm that {neighboring criteria} are still met.
~~~

If the user did not say what they observed, ask for it: one sentence or a screenshot.

## Writing rules

- **One feature per prompt.** If it goes beyond about 60 lines, split it.
- **Imperative and without hesitation.** No "maybe" or "if possible", and no [P], [I] or [V] marks. A detail assumption goes in as a named constant.
- **Do not repeat the Knowledge.** Default states, default interaction, "do not change what exists" and the criteria report at the end are already in the workspace Knowledge. The prompt only declares exceptions, by the convention's name.
- **Say what it does not include.** That is what keeps Lovable from adding things on its own.
- **Interface texts in quotes**, exactly as they must appear.
- **Acceptance at the end**, with the requirement ID in each criterion and one behavior per criterion.
- **Named dependencies.** Say what must exist and, when reusing, the component's name.
- **In the user's language**, the same language as the app interface.

## How to deliver

For each prompt:

1. A title line: "Prompt 2 of 5 · Collaborative Playlist".
2. The prompt in a code block.
3. One line: "Before pasting, type / in the Lovable chat and choose {skill}." The skill also loads on its own when its description matches, but invoking it guarantees it.
4. "How to check": criteria A1 to An, one line each, in the language of someone testing the app.
5. A final line: "When it passes, ask for the next one. If something fails, say which criterion and what you saw."

If the user wants to check Lovable's understanding before generating code, suggest running the prompt first in Plan mode, which discusses without changing the code, and then in Build mode.

## Example: Interactive Wall

Assumption in this example: the `moderation` decision was Yes. If it were No, the status and the moderation queue would disappear, and criteria A1 and A2 would be rewritten.

~~~
PROMPT · Interactive Wall
Skill: public-content
Covers: R4 · US05
Depends on: base prompt (profiles, login, real time)

GOAL
Real-time feed where participants post a photo with a caption and the organizer decides what goes to the big screen.

SCOPE
Includes: wall, post form, moderation queue and the /screen route (approved posts, full screen).
Does not include: likes, comments, voting.

DATA AND PERMISSIONS
Post: author, photo, caption, status (pending | approved | rejected), created_at.
Participant: creates posts, sees approved posts and their own.
Organizer: sees all, approves or rejects.

FLOW
1. The participant chooses the photo, writes the caption and publishes; the post starts as "pending".
2. The organizer approves or rejects it in the moderation queue.
3. Approved: it enters at the top of the feed and on the big screen, without reloading the screen.
Exception: upload fails → warn and keep the selected photo for a new attempt.

RULES
- Caption up to 140 characters (constant CAPTION_MAX).
- One photo per post.

STATES AND INTERACTION (beyond the global convention)
- Empty feed: "Be the first to post".
- Pending post, on the author's screen: "Awaiting approval".

ACCEPTANCE
A1 (R4) Given I am on the wall, when I publish a photo with a valid caption, then the post stays pending and only I can see it.
A2 (R4) Given a pending post, when the organizer approves it, then it enters at the top of everyone's feed.
A3 (R4) Given the upload failed, when I try again, then the photo is still selected.
A4 (R4) Given I am writing the caption, when I go beyond 140 characters, then Publish is disabled.
~~~
