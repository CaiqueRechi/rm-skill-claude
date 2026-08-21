# rm-skill-claude

Skill do Claude Code com as minhas convenções de trabalho — as decisões que eu
já tomei e não quero repetir a cada sessão. Valem para qualquer projeto meu, e
não descrevem sistema nenhum em específico.

**Autor:** Caique Rechi Mehret

## O que ela decide

| Assunto | Regra |
| --- | --- |
| Commits | um por alteração, em inglês, com prefixo convencional e sem co-autoria |
| Push | meu, sempre — o agente para no commit |
| Testes | tudo testado; no mesmo commit quando é desenvolvimento, separado quando é alteração não-trivial |
| Documentação | sem ela o trabalho não está pronto |
| Comentários | só o necessário, e em inglês |
| Nomes | `camelCase` no código, `PascalCase` em classe, `snake_case` no banco |
| Migration | sempre reversível, e o `down()` rodado |
| Design | Clean Code e SOLID, sem abstração para um caso só |
| Discordância | falar antes de fazer, nunca depois |
| Hooks | não se pula, nem com `--no-verify` |

O detalhe e o **porquê** de cada uma estão em [SKILL.md](SKILL.md). O porquê é a
parte que importa: sem ele a letra é cumprida e a intenção se perde no primeiro
caso que não estava previsto.

## Instalação

Clonar dentro de `~/.claude/skills`, que é onde o Claude Code procura skills
pessoais:

```bash
git clone https://github.com/CaiqueRechi/rm-skill-claude.git ~/.claude/skills/rm-skill-claude
```

No PowerShell o `~` chega literal no `git.exe` e o clone falha — use `$HOME`:

```powershell
git clone https://github.com/CaiqueRechi/rm-skill-claude.git "$HOME\.claude\skills\rm-skill-claude"
```

O nome da pasta tem de bater com o `name:` do frontmatter. Se um dia mudar, muda
nos dois: quando divergem, a skill simplesmente não carrega e não dá erro
nenhum.

## Verificação

[docs/testing.md](docs/testing.md) registra como a skill foi testada — três
tarefas executadas duas vezes cada, com a skill e sem, e o que saiu diferente.
Os resultados e as limitações estão lá com o mesmo peso.

## O que entra aqui

Preferência minha, dita por mim, e que valha em qualquer projeto.

Regra deduzida da leitura de um código é palpite, e palpite aqui se propaga para
todo projeto que eu abrir. Detalhe de uma base específica não é assunto desta
skill — é do `CLAUDE.md` ou do `AGENTS.md` daquele repositório.
