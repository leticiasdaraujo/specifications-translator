# Changelog

## 0.2.0 · October 2026

English translation. The flow, principles, questions, templates and base skills are the same as in 0.1.0.

- Skill renamed from `tradutor-de-especificacoes` to `specifications-translator`, matching the repository.
- Reference files renamed: `adaptador-lumora.md` → `lumora-adapter.md`, `formato-canonico.md` → `canonical-format.md`, `catalogo-perguntas.md` → `question-catalog.md`, `gerador-lovable-kit.md` → `lovable-kit-generator.md`, `gerador-lovable-prompts.md` → `lovable-prompt-generator.md`.
- Base skills renamed: `sugestao-voto-ranking` → `suggest-vote-ranking`, `conteudo-do-publico` → `public-content`, `perfis-e-acesso` → `profiles-and-access`, `votacao-com-ciclo` → `voting-with-cycle`.
- Question ids renamed: `importancia` → `importance`, `voto_unico` → `single_vote`, `criacao_aberta` → `open_creation`, `moderacao` → `moderation`, `ciclo_votacao` → `voting_cycle`, `resultado_parcial` → `partial_results`.
- The skill now replies, and writes the kit and prompts, in the user's language. In 0.1.0 it always used Brazilian Portuguese.
- Yes/no answers accept "Y/N/?" and the equivalent in the user's language (e.g., "S/N/?").
- The Lumora adapter keeps the original Portuguese labels of the export, so it still recognizes them.

## 0.1.0 · October 2026

First version, in Brazilian Portuguese. This is the version evaluated in the paper.

- Six-step flow (read, enrich, ask, kit, prompts, maintain) and a first-contact section for users who arrive without knowing how to use it.
- Lumora adapter, based on the exported text. Handling of the .zip is provisional.
- Canonical specification format, with an origin per item (prototype, inferred, validated).
- Catalog with 7 yes/no questions.
- Lovable kit generator: workspace Knowledge, 4 base skills and a project Knowledge template.
- Prompt generator: base prompt, feature prompt and fix prompt.
