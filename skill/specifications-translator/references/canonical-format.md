# Canonical specification format

Neutral with respect to source and target: any source adapter fills it, any target generator reads it. It has two layers, mirroring Lovable: what applies to the whole project (feeds the project Knowledge) and what belongs to each feature (feeds one prompt each).

## Origin of each item

- **[P]** is in the prototype, literally or by direct implication.
- **[I]** the Translator added it.
- **[V]** the user confirmed it.

Only an explicit answer promotes [I] to [V]; "don't know" keeps [I]. The origin exists only in the specification: it never appears in prompts, Knowledge or skills.

## Template

~~~markdown
# Specification · {App name}
Version {n} · {date}

## PROJECT

### Context
{at most 3 lines}

### Profiles
| Profile | Can | Cannot | Origin |

### Entities
| Entity | Main fields | Relations | Origin |

### Project rules
- {rule} [origin]

### Technical constraints
- {rule} [origin]

### Visual identity
{colors, typography, tone; or "pending: no .zip"}

### Decisions
| ID | Question | Answer | Origin |

### Patterns
{pattern id: features that use it}

## FEATURES

### F{n} · {Name}
Covers: R.. · Stories: US..
Depends on: {F.. | base}
Patterns: {ids}
Goal: {1 to 2 sentences}
Includes: ... · Does not include: ...
Data and permissions: ...

#### Stories
{template below}

#### Rules
- {rule} [origin]

#### States and interaction
- {only what departs from the global convention} [origin]

#### Acceptance
A{n} (R..) Given ..., when ..., then ...

## COVERAGE AND GAPS
{list}

## PENDING
{questions still unanswered}
~~~

A pattern's id is the name of the skill that implements it (e.g., `suggest-vote-ranking`). It is what links the feature to the skill and to the corresponding question in the catalog.

## Enriched story template

~~~
US05 · Post a photo on the wall
Feature: F3 · Covers: R4
Actor: Participant
Precondition: event active; account [I]
Flow: open the wall → Post → choose the photo → write the caption → publish
Exception: upload fails → warning, and the photo is kept for a new attempt [I]
Rules: caption up to 140 characters [I]
Interaction: an approved post enters at the top of the feed without reloading [I]
States: empty feed → "Be the first to post" [I]
Acceptance: A1 to A4
~~~

## How to enrich

1. **Split compound stories.** A story that mixes two actions (creating a category and voting on it) becomes two, with the same parent (US03.1, US03.2). Each one needs its own flow and criteria; mixed together, the code generator implements half.
2. **Fill the typical gaps.** Who can (permission), identification (account), limits (size, quantity), ordering and tie-breaking, time windows (open, close), duplicates, editing and deletion, real-time updates, and the empty, loading and error states.
3. **Replace vague words with a value, marked [I].** "Short" becomes 140 characters; "real time" becomes "everyone's screen updates without reloading"; "most voted" becomes "descending order of votes, ties broken by the oldest". A vague word is where the generator improvises.
4. **Record only the deviations in states and interaction.** The global convention lives in the workspace Knowledge. A custom text for the empty state is a deviation; "show loading" is not.
5. **Write observable acceptance criteria.** Given/when/then, one behavior per criterion, each with the requirement ID. Every R has at least one. Include exceptions and limits, not just the happy path.
6. **List what is out of scope.** What someone would expect and the prototype does not ask for (e.g., comments on the wall). It prevents the generator from adding it on its own.
7. **Record the patterns used.** If the feature uses a pattern from the catalog, note its id.
8. **Declare dependencies.** A feature that uses what another creates depends on it. The first prompt that uses a pattern builds the reusable component; the following ones reuse it by name.
