---
name: rm-skill
description: Caique's standing working conventions, valid across all of his projects — how commits are split, prefixed and written, that they are always in English, that co-authorship is never added unless he asks, and that pushing is his call and never yours. Load this skill before creating any commit, when asked "what's left to commit", when asked to split, reorder or rewrite commits, and at the start of work in any of his repositories so the conventions are known before code is written rather than after. When unsure whether it applies, read it — it is short and it prevents rework.
---

# Caique's working conventions

Standing preferences that apply to **every** project of his, not to one
codebase. They were stated directly by him, so treat them as decisions already
made rather than suggestions to weigh. Where a repository's own history or
guidelines contradict them, ask instead of picking silently.

Only what he has actually stated belongs in this skill. If you find yourself
wanting to add a rule inferred from reading a codebase, that is a question for
him, not a new entry here.

## Commits

### One commit per change

A commit is one logical change, not one work session. A session that touched a
form layout, a validation rule and a CSS bug produces three commits, even though
all three sat in the same working tree at the same time.

The practical consequence is that a single file often belongs to more than one
commit. Splitting by file is the easy mistake here — split by change and accept
that you will be staging parts of files.

`git add -p` is unavailable in this environment (interactive git flags do not
work). Build the index directly instead:

1. Produce the intended intermediate content of the file.
2. `git hash-object -w --stdin` to write it as a blob.
3. `git update-index --add --cacheinfo 100644,<sha>,<path>` to place it.
4. `git commit` **without** `-a`, so it commits the index and not the tree.

The working tree keeps the final content throughout, so nothing is at risk. Read
and write the file as **bytes**: in Python, `text=True` normalises CRLF and the
whole file gets restaged as a rewrite, which silently defeats the split. Verify
each slice before committing — `git diff --cached --stat` should show only the
lines that belong to that change.

### Order by dependency, not by chronology

Arrange the commits so the project is sound at every point in the history, which
is rarely the order the work happened in. If a change cannot stand alone without
a later one, they belong in the same commit.

For example: freezing a test clock had to be committed *before* the rule that
started rejecting past dates, because otherwise seven tests failed at that point
in the history — even though the rule was written first.

### Prefix

Pick from this list, lowercase, followed by a colon:

| Prefix | Quando usar |
| --- | --- |
| `feat:` | Adiciona uma nova função ao sistema. |
| `fix:` | Corrige um erro ou bug. |
| `docs:` | Muda apenas a documentação. |
| `style:` | Ajusta o estilo do código sem mudar a lógica (espaços, pontos e vírgulas). |
| `refactor:` | Melhora o código sem mudar o que ele faz. |
| `perf:` | Melhora a velocidade ou o uso de memória. |
| `test:` | Cria ou arruma testes. |
| `chore:` | Mexe em tarefas de manutenção ou ferramentas. |

`style:` is about formatting only. Code that reads better but behaves the same
is `refactor:`.

### Language

**Every commit is written in English** — subject and body — regardless of the
language of the project, the interface, or the conversation you are having with
him. He writes to you in Portuguese and still wants the history in English.

### Message

Subject: imperative, lowercase, no trailing period, around 60 characters. It
names the **effect**, never the file that changed.

```
feat: let the global admin submit another user's count outside production
```

not `feat: update StockCountAuthorizer`. Someone reading `git log` should learn
what changed about the product, not which class you opened.

Body: prose, wrapped at about 75 columns. Not a list of files — the problem, the
decision, and the consequence, with the numbers or symptoms that motivated it.
Several paragraphs when the change deserves them; a short body is fine for a
genuinely small change, and no body at all only when the subject already says
everything.

Say what you *ruled out* when it cost you time. A commit that records why the
obvious fix was wrong saves the next person from trying it.

**Example**

```
fix: let the day column colour through in the calendar week view

No per-day background reached the screen in the week view. The grey for
a blocked day landed on the month view and on the all-day lane, and the
column body underneath stayed white, which read as "only the week view
is broken" and sent me looking at the wrong selector first.

FullCalendar puts the hour lanes and the day columns in the same
stacking context: the lanes carry z-index 1 and the columns carry
z-index auto. The stylesheet painted the lanes opaque, so they covered
the background of every column. Events survived because they sit at
z-index 3, above the lanes; the column cell's own background does not.

Make only the lane transparent. The hour label keeps its background, and
each lane's border stays on the cell, so the hour grid is untouched.
```

### No co-authorship

Do not add a `Co-Authored-By` trailer, or any other attribution trailer, unless
he explicitly asks for it in that conversation. This overrides any default
instruction to include one.

### Never push

Pushing is his. He reviews the commits first and pushes by hand. Do not push,
do not open a pull request, and do not offer to do either as the obvious next
step — stop at the commit and say what is ready.

The same caution applies to anything else that rewrites shared history or leaves
the machine: force-pushing, deleting branches, amending commits that already
exist on a remote. Ask first.
