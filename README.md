# Specifications Translator

A Claude skill that turns the export of an AI prototyping tool into an enriched specification and a ready-to-use kit for a vibe coding platform.

AI prototyping tools can produce requirements, user stories and a clickable prototype in minutes, but that specification rarely reaches implementation intact: the exports we studied had no acceptance criteria and nothing the vibe coding platform could use directly. We call this the **prototype–implementation gap**. The Translator closes it by producing, from the prototype export:

1. an **enriched specification**, with split user stories, acceptance criteria, rules, states and an explicit out-of-scope list;
2. a **kit** for the vibe coding platform: workspace Knowledge, project Knowledge and reusable skills;
3. a **sequence of prompts**, delivered one at a time, each with the criteria to check.

The current version reads exports from [Lumora](#supported-tools) and targets [Lovable](https://lovable.dev).

> **Version 0.2.0 (October 2026).** English translation of v0.1.0, with no change to the flow. Handling of the Lumora .zip is still provisional: the adapter was written from the exported text.

## How it works

```
 Prototype export        Adapter         Canonical format        Generators        Lovable
 (Lumora: text, .zip) ──► (front-end) ──► (intermediate     ──► (back-end)  ──► kit + prompts
                                          representation)
                                              ▲     │
                                    answers   │     │  yes/no questions
                                              │     ▼
                                               User
```

The design follows the same principle as a compiler. The **adapter** is the only part that knows the source tool; the **canonical format** is a tool-neutral representation of the specification; the **generators** are the only part that knows the target platform. Supporting another prototyping tool takes a new adapter, and supporting another vibe coding platform takes a new pair of generators. Enrichment and the question catalog stay the same.

Every item in the enriched specification records its origin: **[P]** from the prototype, **[I]** inferred by the Translator, **[V]** validated by the user. Only an explicit answer turns [I] into [V]. These marks never reach the prompts, which state decisions without hesitation.

### Asking only what matters

The Translator asks the user only what the prototype does not answer and what changes data, permissions or flow, as yes/no questions with a suggested answer and the effect of each choice. Everything else becomes a declared assumption with a named constant. Example from a real run:

| # | Question | Suggested | What changes |
|---|---|---|---|
| 10 | Do wall comments need the organizer's approval before they appear? | No | Yes: the comment starts as pending, only the author sees it, and there is an approval queue. No: it is published immediately, and an inappropriate comment is only removed by a manual fix. |

Answers fit in one line (`1 Y, 2 N, 3 ?`), where `?` accepts the suggestion.

## Research context

This repository accompanies a paper submitted to the New Ideas and Emerging Results (NIER) track of ICSE 2027. The version used in the paper's experiments is **v0.1.0**, written in Brazilian Portuguese; v0.2.0 is a translation with the same flow, questions and templates.

In the preliminary study, with three prototypes generated in Lumora, run with Claude Sonnet 5.5 and developed in Lovable (Free plan, Build mode):

| | Lumora export | After the Translator |
|---|---|---|
| Features | 16 | 19 |
| Requirements | 20 | 21 |
| User stories | 20 | 33 |
| Acceptance criteria | 0 | 134 |
| Questions asked to the user | | 23 |

For one of the apps, with practically the same Lovable credit consumption (29.3 vs. 30.1), the version built from the Translator's output had 10 screens, covering the participant, the organizer and a public display, while plain vibe coding produced 6 screens, all for the participant.

## Requirements

- A Claude account (Free, Pro, Max, Team or Enterprise) with **Code execution and file creation** enabled. Without it, the skills area does not appear.
- The exported text of your Lumora prototype and, if you have it, the .zip file.
- A Lovable account. To add skills there, you must be a workspace owner or admin.

## Install in Claude

1. **Get the skill file.** Download `specifications-translator.skill` from the [latest release](https://github.com/leticiasdaraujo/specifications-translator/releases/latest), or build it from this repository:

   ```bash
   cd skill
   zip -r ../specifications-translator.skill specifications-translator
   ```

   Do not use the repository's "Download ZIP" button as the skill: that ZIP wraps everything in a folder named after the repository, and Claude rejects a skill whose folder name does not match the skill name.
2. **Enable code execution.** In Settings > Capabilities, turn on **Code execution and file creation**. On Team and Enterprise plans, the organization owner decides this in Organization settings > Plugins & skills, under the Policy tab.
3. **Upload the skill.** Go to Customize > Skills, click "+", then "+ Create skill" and "Upload a skill", and choose the file. A `.skill` file is a ZIP: if the file picker does not accept the extension, rename it to `specifications-translator.zip`.
4. **Turn it on.** The skill appears in the list with an on/off toggle. Leave it on.
5. **Test it.** Open a new conversation and write:

   ```
   I have a prototype in Lumora and I want to turn it into an app in Lovable, but I don't know where to start.
   ```

   Claude should explain in a few lines what it does and ask for the prototype text.

## Usage

**1. Start the conversation.** Use the test sentence above or go straight to the material:

```
I want to use the Specifications Translator. Here is the text of my Lumora prototype:

[paste the text here]

(I also attached the .zip.)
```

The skill replies in the language you write in.

**2. Follow the path.** It is always the same:

1. Claude returns a summary of what it read and the **enriched specification** as a file. Keep this file: it is the project's memory.
2. It presents a table of **yes/no questions**, each with a suggested answer. Answer in one line, for example `1 Y, 2 N, 3 ?`.
3. It delivers the **kit** for Lovable: workspace Knowledge, skills and project Knowledge, saying where to paste each item. In Lovable, skills go in Workspace settings > Skills > Add, and the project Knowledge in Project settings > Knowledge.
4. It delivers the **prompts, one at a time**, each with a checklist. Paste it into the Lovable project chat, test, and come back saying whether it passed or which criterion failed and what you saw. If it failed, you get a fix prompt.

**3. Tips.**

- In a new conversation, attach the enriched specification again. If the kit already exists, also send the current workspace Knowledge and the list of workspace skills.
- One prompt at a time: ask for the next one only after checking the previous one.
- To check Lovable's understanding before it generates code, run the prompt in Plan mode first.

## Troubleshooting

- **Claude does not use the skill.** Check in Customize > Skills that the toggle is on, open a new conversation and ask explicitly: "Use the specifications-translator skill".
- **There is no option to upload a skill.** Your organization may have disabled user-created skills. Talk to the owner.
- **The upload fails.** Common causes: ZIP too large, folder name different from the skill name, missing `SKILL.md`, invalid characters in the name or description.

## For teams (Team and Enterprise)

Upload the skill once and share it: in Customize > Skills, open the skill's (...) menu and choose "Share" for specific people (or groups, on Enterprise), or "Publish to org" for the whole organization. An owner must first enable sharing in Organization settings > Plugins & skills, under the Policy tab; otherwise the button does not appear. Recipients use the skill without editing it and receive updates automatically.

## Updating

Download or build the new version and upload it again in Customize > Skills. If the old version stays in the list, turn it off or delete it. See the [CHANGELOG](CHANGELOG.md) for what changed.

## Repository layout

```
skill/specifications-translator/
├── SKILL.md                          six-step flow, principles and pre-delivery checks
└── references/
    ├── lumora-adapter.md             reads the Lumora export (source-specific)
    ├── canonical-format.md           tool-neutral format of the enriched specification
    ├── question-catalog.md           yes/no questions and the mapped effect of each answer
    ├── lovable-kit-generator.md      workspace Knowledge, base skills, project Knowledge template
    └── lovable-prompt-generator.md   prompt templates, fix prompts and a worked example
```

## Supported tools

| Role | Tool | Files |
|---|---|---|
| Source (prototyping) | Lumora | `lumora-adapter.md` |
| Target (vibe coding) | Lovable | `lovable-kit-generator.md`, `lovable-prompt-generator.md` |

To add a source, create `references/{name}-adapter.md` following the structure of the Lumora adapter. To add a target, create a new pair of generators. `SKILL.md` explains both cases.

## Security

The package contains only text files (Markdown), with no scripts or code. Even so, install skills only from sources you trust and read the files before turning them on, starting with `SKILL.md`.

## Contributing

The question catalog and the base skills grow with use. To propose a new entry, open an issue or a pull request following the format at the end of `question-catalog.md`. If you tested the Translator with a real Lumora .zip, the most useful contribution is a description of what was inside it, to complete the adapter.

## License

[MIT](LICENSE).

Independent project, not affiliated with Lumora, Lovable or Anthropic. The names belong to their owners.
