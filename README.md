# Tradutor de especificações

Skill para o Claude que transforma a exportação de um protótipo do Lumora em uma especificação melhor e no kit para construir o app no Lovable: Knowledge, skills e uma sequência de prompts prontos para colar, um de cada vez.

```
Lumora (protótipo)  →  Claude + Tradutor  →  Lovable (app)
texto e .zip           especificação,         você cola o kit e os
                       perguntas de           prompts no Lovable
                       sim/não, kit e         e confere o resultado
                       prompts
```

> Versão 0.1.0 (outubro de 2026). Em teste: o tratamento do .zip do Lumora ainda é provisório, porque o adaptador foi escrito a partir do texto exportado.

## O que você precisa

- Uma conta do Claude (Free, Pro, Max, Team ou Enterprise) com **Code execution and file creation** ativado. Sem isso, a área de skills não aparece.
- O texto exportado do seu protótipo no Lumora e, se tiver, o arquivo .zip.
- Uma conta no Lovable. Para adicionar skills lá, é preciso ser owner ou admin do workspace.

## Instalar no Claude

1. **Baixe o arquivo.** Na página de [Releases](https://github.com/SEU-USUARIO/tradutor-de-especificacoes/releases/latest), baixe `tradutor-de-especificacoes.skill`. Não use o botão "Download ZIP" do repositório: o ZIP dele traz uma pasta com o nome do repositório, e o Claude recusa a skill quando o nome da pasta não bate com o nome da skill.
2. **Ative a execução de código.** Em Settings > Capabilities, ligue **Code execution and file creation**. Nos planos Team e Enterprise isso é decidido pelo owner da organização, em Organization settings > Plugins & skills, na aba Policy.
3. **Envie a skill.** Vá em Customize > Skills, clique em "+", depois em "+ Create skill" e em "Upload a skill", e escolha o arquivo baixado. O `.skill` é um ZIP: se o seletor de arquivo não aceitar a extensão, renomeie para `tradutor-de-especificacoes.zip`.
4. **Ligue a chave.** A skill aparece na lista com um botão liga/desliga. Deixe ligado.
5. **Teste.** Abra uma conversa nova e escreva:

   ```
   Tenho um protótipo no Lumora e quero transformá-lo em um app no Lovable, mas não sei por onde começar.
   ```

   O Claude deve explicar em poucas linhas o que faz e pedir o texto do protótipo.

## Como usar

**1. Comece a conversa.** Com a skill instalada, você pode usar a frase do teste acima ou ir direto ao material:

```
Quero usar o Tradutor de especificações. Segue o texto do meu protótipo no Lumora:

[cole o texto aqui]

(Anexei também o .zip.)
```

**2. Siga o caminho.** Ele é sempre o mesmo:

1. O Claude devolve um resumo do que leu e a **especificação melhorada** em um arquivo. Guarde esse arquivo: ele é a memória do projeto.
2. Ele faz uma tabela de **perguntas de sim/não**, cada uma com um sugerido. Responda em uma linha, por exemplo `1 S, 2 N, 3 ?`. O "?" aceita o sugerido.
3. Ele entrega o **kit** para o Lovable: Knowledge do workspace, skills e Knowledge do projeto. O Claude diz onde colar cada item. No Lovable, as skills entram em Configurações do workspace > Skills > Add, e o Knowledge do projeto em Configurações do projeto > Knowledge.
4. Ele entrega os **prompts, um por vez**, cada um com a lista do que conferir. Cole no chat do projeto no Lovable, teste e volte dizendo se passou ou qual critério falhou e o que você viu. Se falhar, ele entrega um prompt de correção.

**3. Dicas.**

- Em uma conversa nova, anexe de novo a especificação melhorada. Se o kit já existe, mande também o Knowledge do workspace atual e a lista de skills do workspace.
- Um prompt por vez: só peça o próximo depois de conferir o anterior.
- Para conferir o entendimento do Lovable antes de gerar código, rode o prompt primeiro no modo Plan.

## Se o Claude não usar a skill

- Confira em Customize > Skills se a chave está ligada.
- Abra uma conversa nova e peça de forma explícita: "Use a skill tradutor-de-especificacoes".
- Se a opção de enviar skill não existir, a organização pode ter desativado a criação de skills por usuários. Fale com o owner.
- Erros comuns no envio: ZIP grande demais, pasta com nome diferente do nome da skill, falta do `SKILL.md`, caracteres inválidos no nome ou na descrição.

## Para equipes (Team e Enterprise)

Envie a skill uma vez e compartilhe: em Customize > Skills, abra o menu (...) da skill e escolha "Share" para pessoas específicas (ou grupos, no Enterprise), ou "Publish to org" para a organização inteira. Um owner precisa ativar antes as opções de compartilhamento em Organization settings > Plugins & skills, na aba Policy; sem isso o botão não aparece. Quem recebe usa a skill sem poder editá-la e recebe as atualizações automaticamente.

## Atualizar

Baixe a release nova e envie de novo em Customize > Skills. Se a versão antiga continuar na lista, desligue-a ou exclua-a. Veja o [CHANGELOG](CHANGELOG.md) para saber o que mudou.

## Segurança

O pacote tem só arquivos de texto (Markdown), sem scripts nem código. Mesmo assim, instale skills apenas de fontes em que você confia e leia os arquivos antes de ligar. Comece pelo `SKILL.md`.

## O que tem no repositório

```
skill/tradutor-de-especificacoes/
├── SKILL.md                          fluxo em 6 etapas, princípios e conferência
└── references/
    ├── adaptador-lumora.md           lê a exportação do Lumora (depende da origem)
    ├── formato-canonico.md           formato da especificação melhorada
    ├── catalogo-perguntas.md         perguntas de sim/não e o efeito de cada resposta
    ├── gerador-lovable-kit.md        Knowledge, skills base e modelo do Knowledge do projeto
    └── gerador-lovable-prompts.md    molde dos prompts, correção e exemplo
```

A skill foi escrita para ser trocável nas pontas: outra ferramenta de prototipação pede outro adaptador, e outra ferramenta de código pede outro par de geradores. O restante não muda.

## Contribuir

O catálogo de perguntas e as skills base crescem com o uso. Para propor uma entrada nova, abra uma issue ou um pull request seguindo o formato do fim de `catalogo-perguntas.md`. Se você testou o Tradutor com um .zip real do Lumora, o mais útil é descrever o que havia dentro dele, para completar o adaptador.

## Licença

[MIT](LICENSE).

Projeto independente, sem vínculo com Lumora, Lovable ou Anthropic. Os nomes pertencem aos seus donos.
