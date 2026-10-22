# Adapter: Lumora

Reads the Lumora export and fills the canonical format (`canonical-format.md`). It is the only part of the Translator that depends on the source tool: another prototyping tool needs another adapter, and the rest of the flow does not change.

Based on a real export (Palco Aberto, from Festifood UFAM). The content of the .zip has not been mapped yet; see the end of this file.

## Structure of the exported text

| Section | What it contains | Destination | Handling |
|---|---|---|---|
| Title and subtitle | App and event name | Project · Context | Literal name |
| "Protótipo Gerado", "Ver Protótipo", "Expandir conteúdo (F/R/US)" | Leftovers from the Lumora interface | none | Discard |
| Description | Long paragraph about the app | Project · Context | Condense into at most 3 lines: what it is, for whom, where and when it is used |
| Why this app matters | Justification | Project · Context | One sentence, only if it adds something to the description |
| Features | List of "Name: description" | Features | One per item; the description becomes the Goal, in 1 to 2 sentences |
| Requirements | R1 to Rn, one sentence each | Features · Covers | Keep ID and text; map each R to features by meaning |
| User stories | US01 to USnn: "As X, I want Y so that Z (meets R1, R2)"; in the export, "Como X, eu quero Y para Z (atende R1, R2)" | Features · Stories; Project · Profiles | Keep the ID; "meets" (atende) gives traceability; the actor becomes a profile |
| Impact on the audience experience | Engagement taxonomy (behavioral, cognitive, affective) | none | Discard; at most 2 lines of tone for the Knowledge (e.g., immediate feedback when voting) |

Section names above are translated; in the export they appear in Portuguese. A new or renamed section: handle it by meaning and tell the user in "What I read".

## Profiles

- Collect the actor of each story and the subjects of the requirements (e.g., "administrators").
- Merge synonyms and record them as [I]: food fan, spectator and participant become Participant; organizer and administrator become Organizer.
- Someone who only views (e.g., a musician viewing the ranking) becomes a Participant with read permission on that screen, without an account of their own, unless the user says otherwise.

## Coverage and gaps

Cross-check features, requirements and stories and record in "Coverage and gaps":

- a feature without its own requirement (in the example, voting in the main contest and registering the dishes do not appear in any requirement);
- a requirement that belongs to no feature;
- things mentioned only in the description (in the example, projecting the wall on big screens).

Do not fill the gap silently. If it is a detail, it becomes an [I] assumption; if it changes data, permissions or flow, it becomes a question (see `question-catalog.md`).

## The .zip (provisional)

No real .zip has been mapped yet. When you receive one:

1. Unzip it and list the folder structure.
2. Look for design decisions: colors, typography, spacing (theme file, style configuration, CSS variables) and logos.
3. Look for existing screens and routes; they go in as "screens in the prototype" in each feature's Scope.
4. Look for interface texts (labels, messages) and reuse them literally in the prompts.
5. Record the stack found under Technical constraints.

Do not copy prototype code into the prompts; extract only decisions and texts. After the first real .zip, update this section with what it actually contains.
