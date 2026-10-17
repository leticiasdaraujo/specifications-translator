# Adaptador: Lumora

Lê a exportação do Lumora e preenche o formato canônico (`formato-canonico.md`). É a única parte do Tradutor que depende da ferramenta de origem: outra ferramenta de prototipação pede outro adaptador, e o restante do fluxo não muda.

Baseado em uma exportação real (Palco Aberto, do Festifood UFAM). O conteúdo do .zip ainda não foi mapeado; veja o fim deste arquivo.

## Estrutura do texto exportado

| Seção | O que traz | Destino | Tratamento |
|---|---|---|---|
| Título e subtítulo | Nome do app e do evento | Projeto · Contexto | Nome literal |
| "Protótipo Gerado", "Ver Protótipo", "Expandir conteúdo (F/R/US)" | Resíduos da interface do Lumora | nenhum | Descarte |
| Descrição | Parágrafo longo sobre o app | Projeto · Contexto | Condense em até 3 linhas: o que é, para quem, onde e quando é usado |
| Por que este app é importante | Justificativa | Projeto · Contexto | Uma frase, só se acrescentar algo à descrição |
| Funcionalidades | Lista "Nome: descrição" | Funcionalidades | Uma por item; a descrição vira o Objetivo, em 1 a 2 frases |
| Requisitos | R1 a Rn, uma frase cada | Funcionalidades · Cobre | Preserve ID e texto; associe cada R às funcionalidades pelo sentido |
| Histórias de usuário | US01 a USnn: "Como X, eu quero Y para Z (atende R1, R2)" | Funcionalidades · Histórias; Projeto · Perfis | Preserve o ID; o "atende" dá a rastreabilidade; o ator vira perfil |
| Impacto na experiência da audiência | Taxonomia de engajamento (comportamental, cognitivo, afetivo) | nenhum | Descarte; no máximo 2 linhas de tom para o Knowledge (ex.: feedback imediato ao votar) |

Seção nova ou renomeada: trate pelo sentido e avise o usuário em "O que li".

## Perfis

- Colete o ator de cada história e os sujeitos dos requisitos (ex.: "administradores").
- Una sinônimos e registre como [I]: fã de gastronomia, espectador e participante viram Participante; organizador e administrador viram Organizador.
- Quem só consulta (ex.: músico vendo o ranking) vira Participante com permissão de leitura naquela tela, sem conta própria, a menos que o usuário diga o contrário.

## Cobertura e lacunas

Cruze funcionalidades, requisitos e histórias e registre em "Cobertura e lacunas":

- funcionalidade sem requisito próprio (no exemplo, votar no concurso principal e cadastrar os pratos não aparecem em nenhum requisito);
- requisito que não pertence a nenhuma funcionalidade;
- coisas citadas só na descrição (no exemplo, a projeção do mural em telões).

Não preencha a lacuna em silêncio. Se for detalhe, vira suposição [I]; se mudar dados, permissões ou fluxo, vira pergunta (veja `catalogo-perguntas.md`).

## O .zip (provisório)

Ainda não há um .zip real mapeado. Ao receber um:

1. Descompacte e liste a estrutura de pastas.
2. Procure decisões de design: cores, tipografia, espaçamentos (arquivo de tema, configuração de estilos, variáveis CSS) e logotipos.
3. Procure telas e rotas existentes; elas entram como "telas no protótipo" no Escopo de cada funcionalidade.
4. Procure textos de interface (rótulos, mensagens) e reaproveite-os literalmente nos prompts.
5. Registre a stack encontrada em Restrições técnicas.

Não copie código do protótipo para os prompts; extraia só decisões e textos. Depois do primeiro .zip real, atualize esta seção com o que ele de fato contém.
