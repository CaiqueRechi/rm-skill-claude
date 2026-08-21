# rm-skill

Minhas convenções de trabalho, como uma skill do Claude Code. Valem para
**qualquer** projeto meu — não são documentação de um sistema específico.

É o conjunto de decisões que eu já tomei e não quero repetir a cada sessão. Hoje
cobre o padrão de commits; vai crescer conforme outras preferências forem sendo
fixadas.

## Instalação

A skill é reconhecida quando fica em `~/.claude/skills/rm-skill`. Como este
repositório é a própria pasta, basta cloná-lo lá — **informando o destino**:

```bash
git clone https://github.com/CaiqueRechi/rm-skill-claude.git ~/.claude/skills/rm-skill
```

O destino explícito não é detalhe: o repositório se chama `rm-skill-claude` e um
`git clone` sem destino criaria uma pasta com esse nome, que não corresponde ao
`name: rm-skill` do frontmatter.

Depois disso ela aparece na lista de skills disponíveis em qualquer projeto.

## Estrutura

- `SKILL.md` — a skill. O frontmatter (`name`, `description`) decide quando ela
  é carregada; o corpo é lido quando ela dispara.

## Editando

Duas regras para o que entra aqui:

1. **Tem de ser preferência minha, dita por mim.** Regra deduzida da leitura de
   um código é palpite, e palpite na skill se propaga para todos os projetos.
2. **Tem de valer em qualquer projeto.** Detalhe de uma base específica não
   pertence a esta skill — pertence ao `CLAUDE.md` ou ao `AGENTS.md` daquele
   repositório.

Vale escrever o *porquê* de cada regra. Sem ele o modelo cumpre a letra e perde
a intenção — e quando o caso não é exatamente o previsto, escolhe errado.
