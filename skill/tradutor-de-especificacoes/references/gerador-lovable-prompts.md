# Gerador de prompts (Lovable)

Transforma a especificação melhorada em prompts que o usuário cola, um a um, no chat do projeto. Depende do kit já criado (`gerador-lovable-kit.md`): o prompt só traz o que é desta entrega, porque o resto já está no Knowledge e nas skills.

## Tipos de prompt

- **Prompt-base:** a fundação do app (perfis, acesso, entidades base, navegação). Sem funcionalidade de negócio.
- **Prompt de funcionalidade:** um por funcionalidade, na ordem das dependências.
- **Prompt de correção:** nasce de um critério de aceite que falhou. Mexe só nele.

## Plano

Antes do primeiro prompt, entregue o plano em uma tabela:

~~~
| # | Prompt | Cobre | Depende de | Skill |
|---|---|---|---|---|
| 1 | Base do app | acesso e perfis | | perfis-e-acesso |
| 2 | Playlist Colaborativa | R1, R2, R3 | 1 | sugestao-voto-ranking |
~~~

Ordem: base primeiro; depois por dependência; entre funcionalidades independentes, pela importância dita pelo usuário. O primeiro prompt que usa um padrão constrói o componente reutilizável e dá um nome a ele. Os seguintes dizem "reutilize o componente X criado no prompt 2", em vez de recriá-lo.

## Molde do prompt de funcionalidade

Omita a seção que ficar vazia.

~~~
PROMPT · {Nome da funcionalidade}
Skill: {nome-da-skill}
Cobre: {R..} · {US..}
Depende de: {prompt-base | prompt N: o que precisa existir}

OBJETIVO
{1 a 2 frases}

ESCOPO
Inclui: ...
Não inclui: ...

DADOS E PERMISSÕES
{campos novos; quem pode o quê}

FLUXO
1. ...
Exceção: ...

REGRAS
- ... (constante NOME_DA_CONSTANTE, quando for valor ajustável)

ESTADOS E INTERAÇÃO (além da convenção global)
- ...

ACEITE
A1 (R..) Dado ..., quando ..., então ...
~~~

## Molde do prompt-base

~~~
PROMPT · Base do app
Skill: perfis-e-acesso

OBJETIVO
Preparar a fundação de {Nome do app}: perfis, acesso, modelo de dados e navegação. Nenhuma funcionalidade de negócio ainda.

FUNDAÇÃO
Perfis: {lista, com o que cada um pode}
Acesso: {login obrigatório ou não, conforme a decisão}
Entidades base: {as que mais de uma funcionalidade usa; campos específicos ficam para os prompts}
Navegação: {telas principais e rotas, vazias, com o estado vazio padrão}

ESCOPO
Não inclui: funcionalidades de negócio.

ACEITE
A1 Dado que não estou logado, quando abro uma tela que exige conta, então sou levado ao login e volto à tela depois.
A2 Dado que sou {perfil sem poder}, quando tento abrir a área de {perfil com poder}, então o acesso é negado.
~~~

## Molde do prompt de correção

~~~
PROMPT · Correção · {Funcionalidade} · {A#}
Cobre: {R..}

PROBLEMA
Critério {A#}: {texto do critério}.
Esperado: {...}. Observado: {o que o usuário viu}.

ESCOPO
Corrija somente este comportamento. Não altere o resto da funcionalidade nem outras telas.

ACEITE
{A# repetido}. Confirme também que {critérios vizinhos} continuam atendidos.
~~~

Se o usuário não disse o que observou, peça: uma frase ou uma captura de tela.

## Regras de escrita

- **Uma funcionalidade por prompt.** Se passar de cerca de 60 linhas, divida.
- **Imperativo e sem hesitação.** Nada de "talvez" ou "se possível", e nenhuma marca [P], [I] ou [V]. Suposição de detalhe entra como constante nomeada.
- **Não repita o Knowledge.** Estados padrão, interação padrão, "não altere o que existe" e o relatório de critérios no final já estão no Knowledge do workspace. O prompt só declara exceções, pelo nome da convenção.
- **Diga o que não inclui.** É o que impede o Lovable de acrescentar por conta própria.
- **Textos de interface entre aspas**, exatamente como devem aparecer.
- **Aceite no fim**, com o ID do requisito em cada critério e um comportamento por critério.
- **Dependências nomeadas.** Diga o que precisa existir e, quando for reaproveitar, o nome do componente.
- **Português do Brasil.**

## Como entregar

Para cada prompt:

1. Uma linha de título: "Prompt 2 de 5 · Playlist Colaborativa".
2. O prompt em bloco de código.
3. Uma linha: "Antes de colar, digite / no chat do Lovable e escolha {skill}." A skill também carrega sozinha quando a descrição combina, mas invocar garante.
4. "Como conferir": os critérios A1 a An em uma linha cada, na linguagem de quem testa o app.
5. Uma linha final: "Quando passar, peça o próximo. Se algo falhar, diga qual critério e o que você viu."

Se o usuário quiser conferir o entendimento do Lovable antes de gerar código, sugira rodar o prompt primeiro no modo Plan, que discute sem alterar o código, e depois no modo Build.

## Exemplo: Mural Interativo

Suposição neste exemplo: a decisão `moderacao` foi Sim. Se fosse Não, sumiriam o status e a fila de moderação, e os critérios A1 e A2 seriam reescritos.

~~~
PROMPT · Mural Interativo
Skill: conteudo-do-publico
Cobre: R4 · US05
Depende de: prompt-base (perfis, login, tempo real)

OBJETIVO
Feed em tempo real onde participantes postam foto com legenda e o organizador decide o que vai para o telão.

ESCOPO
Inclui: mural, formulário de post, fila de moderação e rota /telao (posts aprovados, em tela cheia).
Não inclui: curtidas, comentários, votação.

DADOS E PERMISSÕES
Post: autor, foto, legenda, status (pendente | aprovado | rejeitado), criado_em.
Participante: cria posts, vê os aprovados e os seus.
Organizador: vê todos, aprova ou rejeita.

FLUXO
1. Participante escolhe a foto, escreve a legenda e publica; o post nasce "pendente".
2. Organizador aprova ou rejeita na fila de moderação.
3. Aprovado: entra no topo do feed e no telão, sem recarregar a tela.
Exceção: falha no upload → avisar e manter a foto selecionada para nova tentativa.

REGRAS
- Legenda de até 140 caracteres (constante LEGENDA_MAX).
- Uma foto por post.

ESTADOS E INTERAÇÃO (além da convenção global)
- Feed vazio: "Seja o primeiro a postar".
- Post pendente, na tela do autor: "Aguardando aprovação".

ACEITE
A1 (R4) Dado que estou no mural, quando publico foto com legenda válida, então o post fica pendente e só eu o vejo.
A2 (R4) Dado um post pendente, quando o organizador o aprova, então ele entra no topo do feed de todos.
A3 (R4) Dado que o upload falhou, quando tento de novo, então a foto continua selecionada.
A4 (R4) Dado que escrevo a legenda, quando passo de 140 caracteres, então Publicar fica desabilitado.
~~~
