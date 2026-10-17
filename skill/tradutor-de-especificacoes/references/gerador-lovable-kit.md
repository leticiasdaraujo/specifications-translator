# Gerador do kit do Lovable

Produz o que o usuário configura no Lovable antes de qualquer prompt de desenvolvimento: Knowledge do workspace, skills e Knowledge do projeto. É uma das duas partes do Tradutor que dependem da ferramenta de destino (a outra é `gerador-lovable-prompts.md`).

## Onde cada coisa vai

Use o critério da própria documentação do Lovable:

- Vale para todos os projetos e para toda mensagem → **Knowledge do workspace**.
- Só importa em um tipo de tarefa (sugestão e voto, conteúdo do público) → **skill do workspace**.
- Vale para este projeto em toda mensagem (perfis, entidades, identidade visual) → **Knowledge do projeto**.
- Pertence a esta entrega → **prompt**.

Nenhuma regra mora em dois lugares. A skill diz como fazer; o prompt diz o quê.

## Knowledge do workspace (base)

Entregue este texto quando o workspace ainda não tem Knowledge. Se tiver, peça o texto atual e proponha só os acréscimos. É um único Knowledge, compartilhado por todos os projetos do workspace: mantenha-o curto e genérico.

~~~
# Convenções (valem para todos os projetos deste workspace)

## Idioma e tom
- Interface em português do Brasil, com frases curtas e diretas, sem jargão técnico.

## Estados de interface
- Toda tela que busca dados tem três estados: vazio (mensagem útil e uma ação sugerida), carregando (indicador de progresso) e erro (mensagem clara e botão "Tentar de novo"). Nunca deixe tela em branco nem texto genérico como "Sem dados".
- Ação demorada mostra progresso e desabilita o botão até terminar.

## Interação
- Toda ação do usuário tem retorno imediato na tela (voto contado, item adicionado).
- Filtros e busca: aplicar atualiza a lista na hora; "Limpar" volta ao estado inicial; resultado vazio oferece "Limpar filtros".
- Ação que apaga ou cancela pede confirmação.

## Como trabalhar
- Faça só o que o prompt pede. Não altere telas, tabelas ou regras existentes que o prompt não cita; se o pedido conflitar com algo que já existe, avise antes de mudar.
- Valores ajustáveis (limites, prazos, textos fixos) ficam em constantes nomeadas, em um só lugar.
- Permissões são aplicadas no banco, não só escondendo botões na interface.

## Ao concluir cada prompt
- Liste os critérios de aceite (A1, A2...) como atendido ou não atendido e diga o que ficou de fora.
~~~

## Skills

### Regras

- **Uma função por skill.** Skill que faz muito carrega na hora errada.
- **A descrição é o gatilho.** Comece com "Use quando...", diga o que cobre e o que não cobre, e aponte a skill vizinha quando houver (ex.: "não use para enquetes fixas; use votacao-com-ciclo").
- **Instruções como playbook curto.** Seções pequenas, frases diretas, valores concretos. Onde o prompt for omisso, a skill traz o padrão; onde o prompt disser algo, o prompt vence.
- **Não repita o Knowledge.** Estados de interface, constantes e permissões no banco já estão lá.
- **Nome:** letras minúsculas, números e hífens, no máximo 64 caracteres, sem hífen no começo ou no fim. O nome não muda depois de criada.
- **Entregue só as skills dos padrões que a especificação usa** e que ainda não existem no workspace. Para um padrão que não está nesta biblioteca, redija uma nova com o mesmo molde e acrescente a entrada correspondente ao catálogo de perguntas.

### Como entregar

Um bloco por skill, com os três campos do formulário do Lovable (nome, descrição, instruções), para colar em Configurações do workspace → Skills → Add → Write manually. Se o usuário preferir importar, e houver ferramenta de arquivos, entregue um .zip por skill, cada um com um SKILL.md na raiz (frontmatter com name e description, seguido das instruções). Só owner ou admin do workspace adiciona skills. Adicionar pelas configurações não consome créditos.

### Biblioteca base

Genéricas de propósito: valem para qualquer projeto do workspace.

#### sugestao-voto-ranking

~~~
Nome: sugestao-voto-ranking

Descrição: Use quando for criar ou alterar uma funcionalidade em que pessoas sugerem itens (músicas, ideias, categorias, perguntas), votam nos itens dos outros e veem um ranking dos mais votados. Não use para enquetes com opções fixas criadas por um administrador (use votacao-com-ciclo) nem para posts de feed (use conteudo-do-publico).

Instruções:
# Sugestão, voto e ranking
Siga as regras do prompt. Onde ele for omisso, use os padrões abaixo.

## Dados
- Item: texto, autor, criado_em (e status, se o prompt exigir moderação).
- Voto: item, pessoa, criado_em. A contagem sai dos votos; não guarde um número editável.

## Voto
- Uma pessoa vota uma vez por item, garantido no banco com restrição de unicidade, não só desabilitando o botão.
- Quem votou pode retirar o voto; a contagem acompanha.
- O botão responde na hora e volta atrás se o servidor recusar.

## Sugestão
- Antes de criar, compare o texto (sem acento, sem diferença de caixa, sem espaços extras) com os itens existentes e ofereça votar no item parecido. Evita ranking dividido.
- O limite de tamanho do texto fica em constante nomeada.

## Ranking
- Ordem decrescente de votos; desempate pelo item mais antigo. Mostre posição e contagem.
- O ranking reordena quando chegam votos.

## Critérios típicos de aceite
- Sugerir cria o item com 0 votos e ele aparece no ranking.
- Votar soma 1; votar de novo no mesmo item não soma.
- Retirar o voto subtrai 1.
- Itens empatados aparecem do mais antigo para o mais novo.
~~~

#### conteudo-do-publico

~~~
Nome: conteudo-do-publico

Descrição: Use quando pessoas publicarem conteúdo que outras veem (fotos, comentários, posts de mural, avaliações), com ou sem moderação, ou quando o conteúdo for exibido em tela pública. Não use para votos e rankings (use sugestao-voto-ranking).

Instruções:
# Conteúdo publicado pelo público
Siga as regras do prompt. Onde ele for omisso, use os padrões abaixo.

## Dados
- Post: autor, texto, mídia (se houver), status, criado_em.
- Status: se o prompt exigir moderação, o post nasce pendente e vira aprovado ou rejeitado. Sem moderação, nasce aprovado.

## Quem vê o quê
- Pessoas em geral veem só posts aprovados.
- O autor vê os seus em qualquer status, com rótulo (ex.: "Aguardando aprovação").
- Quem modera vê todos e decide.

## Moderação (quando houver)
- Fila do mais antigo para o mais novo, com aprovar e rejeitar em um toque.
- Rejeitado some para os outros; o autor continua vendo o status.

## Envio
- Tipos e tamanho máximo de arquivo, e limite de caracteres do texto, em constantes nomeadas. Contador de caracteres visível.
- Mostre o progresso do envio. Se falhar, mantenha o arquivo selecionado para nova tentativa.

## Feed
- Do mais recente para o mais antigo. Se o projeto tem atualização em tempo real (veja o Knowledge do projeto), um post aprovado entra no topo do feed de todos sem recarregar.
- Carregue em partes quando houver muitos posts.

## Tela pública (quando o prompt pedir)
- Rota própria, em tela cheia, sem controles, com letra grande, atualização automática e só posts aprovados.

## Critérios típicos de aceite
- Com moderação: o post novo fica pendente e só o autor o vê; ao ser aprovado, aparece para todos.
- Falha no envio mantém o arquivo selecionado.
- Texto acima do limite desabilita o botão de publicar.
~~~

#### perfis-e-acesso

~~~
Nome: perfis-e-acesso

Descrição: Use quando o projeto precisar de login, cadastro, perfis de usuário (ex.: participante, organizador) ou controle de quem pode ver e fazer o quê. Não use para regras de uma única funcionalidade; essas ficam no prompt dela.

Instruções:
# Perfis e acesso
Siga as decisões do prompt (login obrigatório ou não, perfis existentes).

## Contas
- Cadastro e entrada pelo método indicado no prompt, sessão que persiste, botão de sair e recuperação de senha quando houver senha.
- Quem se cadastra recebe o perfil padrão, o de menor poder.

## Perfis
- Guarde o perfil em tabela própria, separada do cadastro da pessoa, de modo que ninguém consiga mudar o próprio perfil.
- Promoção a um perfil com mais poder só por quem já tem esse poder.

## Rotas
- Tela que exige conta leva ao login e, depois, de volta à tela pedida.
- Áreas de um perfil (ex.: moderação) negam acesso aos demais, com mensagem clara.

## Critérios típicos de aceite
- Sem login, abrir uma tela protegida leva ao login e volta à tela depois.
- Participante não acessa a área do organizador.
- Participante não consegue se promover a organizador.
~~~

#### votacao-com-ciclo

~~~
Nome: votacao-com-ciclo

Descrição: Use quando um administrador criar enquetes ou votações com opções predefinidas que o público vota em momentos controlados (abrir, encerrar, mostrar resultado). Não use para sugestões abertas do público (use sugestao-voto-ranking).

Instruções:
# Votação com ciclo
Siga as regras do prompt. Onde ele for omisso, use os padrões abaixo.

## Dados
- Enquete: título, status (rascunho, aberta, encerrada) e, se o prompt pedir encerramento por horário, prazo.
- Opção: enquete, texto. Voto: enquete, opção, pessoa.

## Ciclo
- Só enquete aberta recebe voto. Encerrar congela o resultado.
- Controle manual: quem administra abre e encerra por botão. Controle por horário: abre e encerra sozinha no prazo.
- Pode haver várias enquetes; o público vê as abertas em destaque.

## Voto
- Um voto por pessoa por enquete, garantido no banco. Trocar de opção só se o prompt permitir.

## Resultado
- Visibilidade conforme o prompt: parcial para todos, ou só para quem administra até encerrar.
- Mostre contagem e percentual por opção e indique empate.
- Se o projeto tem atualização em tempo real (veja o Knowledge do projeto), abertura, encerramento e contagens chegam a todos sem recarregar.

## Critérios típicos de aceite
- Enquete em rascunho ou encerrada não aceita voto.
- Votar de novo na mesma enquete não soma.
- Ao encerrar, o resultado final aparece e não muda mais.
~~~

## Knowledge do projeto

Gerado da camada PROJETO da especificação, uma vez por projeto. Escreva como se estivesse apresentando o projeto a uma pessoa nova no time: específico (cores com código, regras com valores), curto (cerca de 60 linhas no máximo) e sem nada que já esteja no Knowledge do workspace ou que pertença a uma funcionalidade só.

~~~
# {Nome do app}

## Contexto
{2 a 3 linhas: o que é, para quem, onde e quando é usado}

## Perfis e permissões
- {Perfil}: pode ..., não pode ...

## Entidades
- {Entidade}: {campos principais}; relações com {outras entidades}.

## Regras do projeto
- {decisões de projeto confirmadas: login, voto único...}

## Restrições técnicas
- {ex.: atualização em tempo real em feeds, rankings e resultados; uso principal em celular}

## Identidade visual
- Cores: {primária #..., secundária #...}. Tipografia: {...}. Tom: {...}.
~~~

Quando uma decisão de projeto mudar, entregue só a seção alterada e diga qual seção substituir.

## Como entregar o kit

Nesta ordem: Knowledge do workspace, skills, Knowledge do projeto (Configurações do projeto → Knowledge). Cada item em bloco de código, com uma linha antes dizendo onde colar. Ao final, peça que o usuário confirme que colou tudo antes de seguir para os prompts.

## Fonte

Documentação do Lovable consultada em outubro de 2026: skills (docs.lovable.dev/features/skills), glossário (docs.lovable.dev/glossary) e FAQ de knowledge (lovable.dev/faq/development/knowledge). Confira se a interface mudou antes de dar instruções de menu.
