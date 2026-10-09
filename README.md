# rm-skill-claude

Skill do Claude Code com as minhas convenções de trabalho — as decisões que eu
já tomei e não quero repetir a cada sessão. Valem para qualquer projeto meu, e
não descrevem sistema nenhum em específico.

**Autor:** Caique Rechi Mehret

## O que ela decide

| Assunto | Regra |
| --- | --- |
| Commits | um por alteração, em inglês, com prefixo convencional e sem co-autoria |
| Erro já commitado | corrigido em commit novo; reescrever só quando eu pedir |
| Branch | uma por tarefa, a partir da `main`, no padrão `cm-titulo-resumido` |
| Push | meu, sempre — o agente para no commit |
| Testes | tudo testado; no mesmo commit quando é desenvolvimento, separado quando é alteração não-trivial |
| Documentação | sem ela o trabalho não está pronto, e vai sempre num commit `docs:` próprio |
| Comentários | só o necessário, e em inglês |
| Nomes | `camelCase` no código, `PascalCase` em classe, `snake_case` no banco |
| Migration | sempre reversível, e o `down()` rodado |
| Design | Clean Code e SOLID sempre, sem abstração para um caso só |
| Segurança | toda alteração checada contra bot criando registro, XSS e dado sensível exposto |
| Desempenho | cache onde o dado permitir; navegador e servidor escolhidos de propósito |
| Subagentes | só quando eu pedir |
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

### Instalação por release

As [Releases](https://github.com/CaiqueRechi/rm-skill-claude/releases) oferecem
um ZIP pronto para instalação e o respectivo checksum SHA-256. Baixe os dois
arquivos da versão desejada, valide o checksum e extraia o ZIP dentro de
`~/.claude/skills`. O arquivo já contém a pasta `rm-skill-claude` na raiz.

No Linux:

```bash
sha256sum -c rm-skill-claude-v1.0.0.zip.sha256
unzip rm-skill-claude-v1.0.0.zip -d ~/.claude/skills
```

No PowerShell, compare o resultado abaixo com o valor presente no arquivo
`.sha256` antes de extrair:

```powershell
Get-FileHash .\rm-skill-claude-v1.0.0.zip -Algorithm SHA256
Expand-Archive .\rm-skill-claude-v1.0.0.zip -DestinationPath "$HOME\.claude\skills"
```

## Releases

A versão a publicar fica em [VERSION](VERSION) e segue versionamento semântico.
Todo pull request valida a estrutura e a criação do ZIP. Quando uma alteração
entra na `main` com uma versão ainda não publicada, o workflow cria
automaticamente:

- a tag `vX.Y.Z`;
- a Release com notas geradas a partir dos commits;
- o pacote `rm-skill-claude-vX.Y.Z.zip`;
- o checksum `rm-skill-claude-vX.Y.Z.zip.sha256`.

Depois de uma versão publicada, a próxima alteração que deva gerar Release
precisa atualizar o arquivo `VERSION`.

## Verificação

[docs/testing.md](docs/testing.md) registra como a skill foi testada — três
tarefas executadas duas vezes cada, com a skill e sem, e o que saiu diferente.
Os resultados e as limitações estão lá com o mesmo peso.

## O que entra aqui

Preferência minha, dita por mim, e que valha em qualquer projeto.

Regra deduzida da leitura de um código é palpite, e palpite aqui se propaga para
todo projeto que eu abrir. Detalhe de uma base específica não é assunto desta
skill — é do `CLAUDE.md` ou do `AGENTS.md` daquele repositório.
