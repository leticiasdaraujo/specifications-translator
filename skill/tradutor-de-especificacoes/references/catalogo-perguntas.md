# Catálogo de perguntas

Cada entrada guarda a pergunta e o efeito de cada resposta já mapeado. Assim o Tradutor não reinterpreta a resposta do usuário a cada projeto, e a mesma decisão produz o mesmo resultado em qualquer projeto.

## Regras

- **Pergunte só quando as duas condições valem:** o protótipo não responde e a resposta muda dados, permissões ou fluxo. Limites, desempates e textos são detalhe: viram suposição [I] com constante nomeada.
- **Teto:** 3 perguntas por funcionalidade, fora a de importância. Decisões de projeto (login, voto único) são feitas uma vez e valem para todas as funcionalidades. O que passar do teto vira suposição [I].
- **Formato:** sim/não, em linguagem de negócio, com sugerido e o efeito de cada resposta em uma linha. Sem termos técnicos.
- **Respostas:** "S", "N" ou "?". O "?" adota o sugerido e a decisão fica [I], não [V].
- **Encadeamento:** `criacao_aberta` = Sim leva a `moderacao`. `voto_unico` depende de `login`: se login for Não, avise que o limite por pessoa passa a ser aproximado e peça confirmação.

## Como apresentar

Uma tabela só, com todas as perguntas, numeradas. Peça a resposta em uma linha.

~~~
| # | Pergunta | Sugerido | O que muda |
|---|---|---|---|
| 1 | Para votar ou postar, a pessoa precisa entrar com uma conta? | Sim | Sim: votos e posts ligados à conta. Não: acesso livre, e o limite de voto por pessoa fica pouco confiável. |
| 2 | Os posts do mural precisam de aprovação do organizador antes de aparecer? | Sim | Sim: posts ficam pendentes até serem aprovados. Não: publicam na hora. |

Responda numa linha, por exemplo: 1 S, 2 N.
~~~

## Como registrar

Cada resposta vira uma linha em "Decisões" (projeto) ou nas regras da funcionalidade, com origem [V] ou [I]. Em seguida, atualize o que ela afeta (entidades, perfis, regras, critérios de aceite) e diga em uma linha o que mudou.

## Perguntas livres

Se o protótipo deixar uma lacuna que o catálogo não cobre (ex.: "o mural precisa de uma tela própria para projeção no telão?"), faça a pergunta no mesmo formato e conte-a no teto. Se uma pergunta livre aparecer em dois ou mais projetos, proponha ao usuário incluí-la neste catálogo e entregue a entrada pronta, no formato abaixo.

## Entradas

### importancia
- **Quando:** para cada funcionalidade.
- **Pergunta:** {Funcionalidade} é importante para a primeira versão?
- **Sugerido:** Sim.
- **Se Sim:** entra nos prompts desta rodada.
- **Se Não:** fica na especificação como "depois" e não gera prompt agora.
- **Escopo:** funcionalidade.

### login
- **Quando:** o app tem votos, posts ou qualquer ação ligada a uma pessoa.
- **Pergunta:** Para {ação}, a pessoa precisa entrar com uma conta?
- **Sugerido:** Sim.
- **Se Sim:** contas de usuário; a ação fica ligada à conta; perfis aplicados no banco.
- **Se Não:** acesso livre; limites por pessoa ficam pouco confiáveis.
- **Escopo:** projeto.
- **Skill:** `perfis-e-acesso`.

### voto_unico
- **Quando:** existe voto ou curtida em itens.
- **Pergunta:** Cada pessoa pode votar só uma vez em cada {item}?
- **Sugerido:** Sim.
- **Se Sim:** um voto por pessoa e item, garantido no banco; retirar o voto é permitido [I].
- **Se Não:** votos ilimitados; o ranking passa a medir insistência, não preferência.
- **Escopo:** projeto.
- **Skill:** `sugestao-voto-ranking`, `votacao-com-ciclo`.

### criacao_aberta
- **Quando:** o público sugere ou cadastra {itens}, ou o protótipo não diz quem cadastra.
- **Pergunta:** Qualquer participante pode criar novos {itens}?
- **Sugerido:** Sim, se o protótipo fala em "sugerir" ou "criar"; Não, se os itens são uma lista conhecida (ex.: pratos do concurso).
- **Se Sim:** participante cria itens; pergunte `moderacao`; entra a checagem de duplicatas.
- **Se Não:** só {perfil_admin} cadastra; o público só consulta e vota.
- **Escopo:** funcionalidade.
- **Skill:** `sugestao-voto-ranking`.

### moderacao
- **Quando:** o público publica algo que outros veem e o protótipo não fala em aprovação.
- **Pergunta:** Os {itens} precisam ser aprovados por {perfil_admin} antes de aparecerem para os outros?
- **Sugerido:** Sim, se o conteúdo vai para tela pública; Não, nos demais casos.
- **Se Sim:** item ganha status (pendente, aprovado ou rejeitado); fila de moderação para {perfil_admin}; o autor vê "Aguardando aprovação"; entram os critérios do estado pendente.
- **Se Não:** publicação imediata, sem status.
- **Escopo:** funcionalidade; vira decisão de projeto se o padrão se repetir em várias.
- **Skill:** `conteudo-do-publico`.

### ciclo_votacao
- **Quando:** existe enquete ou votação com opções predefinidas.
- **Pergunta:** {perfil_admin} abre e encerra cada {votação}?
- **Sugerido:** Sim.
- **Se Sim:** estados rascunho, aberta e encerrada, com botões de abrir e encerrar.
- **Se Não:** a votação encerra sozinha por horário; entra o campo de prazo.
- **Escopo:** funcionalidade.
- **Skill:** `votacao-com-ciclo`.

### resultado_parcial
- **Quando:** existe ranking ou contagem de votos.
- **Pergunta:** O público vê o resultado enquanto a votação está aberta?
- **Sugerido:** Sim, se algum requisito ou história fala em ranking visível ao público; Não, se só o organizador acompanha.
- **Se Sim:** ranking ou contagem público, atualizado em tempo real.
- **Se Não:** só {perfil_admin} vê até encerrar; o público vê o resultado final depois.
- **Escopo:** funcionalidade.
- **Skill:** `sugestao-voto-ranking`, `votacao-com-ciclo`.

## Formato de uma entrada nova

~~~
### {id}
- **Quando:** {situação que dispara a pergunta}
- **Pergunta:** {frase com {campos}}
- **Sugerido:** {Sim | Não, com a regra de escolha}
- **Se Sim:** {efeito em dados, permissões, fluxo e critérios}
- **Se Não:** {efeito}
- **Escopo:** projeto | funcionalidade
- **Skill:** {skill que implementa o padrão, se houver}
~~~
