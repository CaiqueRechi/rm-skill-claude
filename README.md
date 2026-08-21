# rm-skill

Minhas convenções de trabalho para os projetos do Hub Ibiporã, como uma skill do
Claude Code.

Não é documentação do sistema: é o conjunto de decisões que eu já tomei e não
quero repetir a cada sessão — como os commits devem ser divididos e escritos,
como os comentários de código são feitos, e os padrões de tela e de teste que a
base já usa.

## Instalação

A skill é reconhecida quando fica em `~/.claude/skills/rm-skill`. Como este
repositório é a própria pasta, basta cloná-lo lá:

```bash
git clone <url> ~/.claude/skills/rm-skill
```

Depois disso ela aparece na lista de skills disponíveis em qualquer projeto.

## Estrutura

- `SKILL.md` — a skill. O frontmatter (`name`, `description`) decide quando ela
  é carregada; o corpo é lido quando ela dispara.

## Editando

O que entra aqui tem de ser uma preferência **estável e reutilizável**. Detalhe
que só valeu para uma tarefa não pertence à skill. Vale escrever o *porquê* de
cada regra: sem ele o modelo cumpre a letra e perde a intenção.
