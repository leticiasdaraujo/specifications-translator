# Formato canônico da especificação

Neutro em relação à origem e ao destino: qualquer adaptador de origem o preenche, qualquer gerador de destino o lê. Tem duas camadas, espelhando o Lovable: o que vale para o projeto inteiro (alimenta o Knowledge do projeto) e o que pertence a cada funcionalidade (alimenta um prompt cada).

## Origem de cada item

- **[P]** está no protótipo, literalmente ou por implicação direta.
- **[I]** o Tradutor acrescentou.
- **[V]** o usuário confirmou.

Só uma resposta explícita promove [I] a [V]; "não sei" mantém [I]. A origem existe apenas na especificação: nunca aparece em prompts, Knowledge ou skills.

## Modelo

~~~markdown
# Especificação · {Nome do app}
Versão {n} · {data}

## PROJETO

### Contexto
{até 3 linhas}

### Perfis
| Perfil | Pode | Não pode | Origem |

### Entidades
| Entidade | Campos principais | Relações | Origem |

### Regras do projeto
- {regra} [origem]

### Restrições técnicas
- {regra} [origem]

### Identidade visual
{cores, tipografia, tom; ou "pendente: sem .zip"}

### Decisões
| ID | Pergunta | Resposta | Origem |

### Padrões
{id do padrão: funcionalidades que o usam}

## FUNCIONALIDADES

### F{n} · {Nome}
Cobre: R.. · Histórias: US..
Depende de: {F.. | base}
Padrões: {ids}
Objetivo: {1 a 2 frases}
Inclui: ... · Não inclui: ...
Dados e permissões: ...

#### Histórias
{molde abaixo}

#### Regras
- {regra} [origem]

#### Estados e interação
- {só o que foge da convenção global} [origem]

#### Aceite
A{n} (R..) Dado ..., quando ..., então ...

## COBERTURA E LACUNAS
{lista}

## PENDÊNCIAS
{perguntas ainda sem resposta}
~~~

O id de um padrão é o nome da skill que o implementa (ex.: `sugestao-voto-ranking`). É o que liga a funcionalidade à skill e à pergunta correspondente do catálogo.

## Molde de história enriquecida

~~~
US05 · Postar foto no mural
Funcionalidade: F3 · Cobre: R4
Ator: Participante
Pré-condição: evento ativo; conta [I]
Fluxo: abrir o mural → Postar → escolher a foto → escrever a legenda → publicar
Exceção: falha no upload → aviso e foto mantida para nova tentativa [I]
Regras: legenda de até 140 caracteres [I]
Interação: post aprovado entra no topo do feed sem recarregar [I]
Estados: feed vazio → "Seja o primeiro a postar" [I]
Aceite: A1 a A4
~~~

## Como enriquecer

1. **Quebre histórias compostas.** Uma história que mistura duas ações (criar uma categoria e votar nela) vira duas, com o mesmo pai (US03.1, US03.2). Cada uma precisa de fluxo e critérios próprios; misturadas, o gerador de código implementa metade.
2. **Preencha as lacunas típicas.** Quem pode (permissão), identificação (conta), limites (tamanho, quantidade), ordenação e desempate, janelas de tempo (abrir, encerrar), duplicidade, edição e exclusão, atualização em tempo real, e os estados vazio, carregando e erro.
3. **Troque palavra vaga por valor, marcado [I].** "Curto" vira 140 caracteres; "tempo real" vira "a tela de todos atualiza sem recarregar"; "mais votadas" vira "ordem decrescente de votos, desempate pelo mais antigo". Palavra vaga é onde o gerador improvisa.
4. **Registre só os desvios em estados e interação.** A convenção global mora no Knowledge do workspace. Um texto próprio para o estado vazio é desvio; "mostrar carregando" não é.
5. **Escreva critérios de aceite observáveis.** Dado/quando/então, um comportamento por critério, cada um com o ID do requisito. Todo R tem pelo menos um. Inclua exceção e limite, não só o caminho feliz.
6. **Liste o fora de escopo.** O que alguém esperaria e o protótipo não pede (ex.: comentários no mural). Evita que o gerador acrescente por conta própria.
7. **Registre os padrões usados.** Se a funcionalidade usa um padrão do catálogo, anote o id.
8. **Declare dependências.** Funcionalidade que usa o que outra cria depende dela. O primeiro prompt que usa um padrão constrói o componente reutilizável; os seguintes o reutilizam pelo nome.
