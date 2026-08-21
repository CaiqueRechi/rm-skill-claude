# rm-skill-claude

Minhas convenções de trabalho, como uma skill do Claude Code. Valem para
**qualquer** projeto meu — não são documentação de um sistema específico.

É o conjunto de decisões que eu já tomei e não quero repetir a cada sessão:
design, nomenclatura, banco, comentários, testes, documentação, verificação e o
padrão de commits.

## Instalação

Clonar dentro de `~/.claude/skills`, que é onde o Claude Code procura skills
pessoais:

```bash
git clone https://github.com/CaiqueRechi/rm-skill-claude.git ~/.claude/skills/rm-skill-claude
```

O nome da pasta tem de bater com o `name:` do frontmatter — os dois são
`rm-skill-claude`. Se um dia a pasta for renomeada, o frontmatter muda junto,
senão a skill deixa de carregar sem dar erro nenhum.

Depois disso ela aparece na lista de skills disponíveis em qualquer projeto.

## Estrutura

- `SKILL.md` — a skill. O frontmatter (`name`, `description`) decide quando ela
  é carregada; o corpo é lido quando ela dispara.
- `docs/testing.md` — como a skill foi testada, com resultados e limitações.

## Editando

Duas regras para o que entra aqui:

1. **Tem de ser preferência minha, dita por mim.** Regra deduzida da leitura de
   um código é palpite, e palpite na skill se propaga para todos os projetos.
2. **Tem de valer em qualquer projeto.** Detalhe de uma base específica não
   pertence a esta skill — pertence ao `CLAUDE.md` ou ao `AGENTS.md` daquele
   repositório.

Vale escrever o *porquê* de cada regra. Sem ele o modelo cumpre a letra e perde
a intenção — e quando o caso não é exatamente o previsto, escolhe errado.
