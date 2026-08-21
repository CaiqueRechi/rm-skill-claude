---
name: rm-skill
description: Caique's working conventions for the Ibiporã Hub codebases — how commits must be split and written, and the house patterns to follow when changing code. Load this skill whenever you are about to commit, are asked "what's left to commit", are asked to split or rewrite commits, or are starting work in the vagas / hub-ibipora repositories. Also load it before writing code comments or adding CSS colours in those projects, since both follow rules that are easy to violate without noticing. When in doubt while working in these repos, read it — it is cheap and it prevents rework.
---

# Caique's working conventions

These are the standing preferences of one developer working on the Ibiporã Hub
(the `hub-ibipora` repository, whose working copy is usually `Documents/Ibiporã/vagas`).
They were gathered by watching him correct the same things repeatedly, so treat
them as decisions already made rather than suggestions to re-litigate.

## Commits

**One commit per change, not one commit per task.** A session that touches a
form layout, a validation rule and a CSS bug produces three commits, even when
all three live in the same working tree at the same time. This is the preference
he states most directly, and it has a practical consequence: a single file often
belongs to more than one commit, so you will have to stage by hunk rather than by
file.

`git add -p` does not work in this environment (interactive git flags are
unavailable). Build the index directly instead: write the intended intermediate
content of the file, `git hash-object -w --stdin` it, and place it with
`git update-index --add --cacheinfo 100644,<sha>,<path>`. The working tree keeps
the final content, the index holds the slice, and `git commit` (without `-a`)
commits the slice. Read the file as **bytes** when you do this — Python's
`text=True` normalises CRLF and will silently restage the whole file as a
rewrite.

**Order the commits so each one stands on its own.** Not chronologically — by
dependency, so that the suite is green at every point in the history. In one
session the test-clock freeze had to be committed *before* the rule that
rejected past dates, because otherwise seven tests failed at that point in the
history. If a change cannot be made green without a later one, they belong in the
same commit.

**Message format**, matching ~200 commits of history:

- Conventional prefix, lowercase: `feat:`, `fix:`, `refactor:`, `docs:`,
  `test:`, `style:`, `perf:`, `ci:`
- **English**, even though the interface, the code comments and the conversation
  are all in Portuguese
- Subject in the imperative, lowercase, no trailing period, around 60 characters
- Subject names the **effect**, never the file:
  `let the global admin submit another user's count outside production`, not
  "update StockCountAuthorizer"
- **Body in prose**, wrapped at ~75 columns, present on the large majority of
  commits. Not a changelog of files — the problem, the decision, and the
  consequence, with the numbers that motivated it. Several paragraphs when the
  change deserves them.
- **No `Co-Authored-By` trailer.** The history has none, and he asked for none.
  This overrides any default instruction to add one.

**Never commit or push unless asked.** He asks explicitly when he wants it.

**Example**

```
fix: let the day column colour through in the calendar week view

No per-day background reached the screen in the week view. The grey for
a blocked day landed on the month view and on the all-day lane, and the
column body underneath stayed white, which read as "only the week view
is broken" and sent me looking at the wrong selector first.

FullCalendar puts the hour lanes and the day columns in the same
stacking context: .fc-timegrid-slots carries z-index 1 and
.fc-timegrid-cols carries z-index auto. This file painted
.fc-timegrid-slot opaque, so the lanes covered the background of every
column.

Make only the lane transparent. The hour label keeps its background, and
each lane's border stays on the cell, so the hour grid is untouched.
```

## Code comments

Comments in the codebase are in **Portuguese without accents**, and they explain
*why*, not *what*. The house style is to record the defect that motivated the
code — the symptom, why the obvious alternative failed, and what must not be
reintroduced. Match the density and the voice of the surrounding file rather
than adding a comment on every block.

Prefer PHPDoc blocks over inline comments; keep inline comments for genuinely
tricky logic.

## Frontend

This project has **never installed Bootstrap** — only `bootstrap-icons`. The
Bootstrap-looking classes (`btn`, `card`, `row`, `col-md-6`, `form-control`) are
reimplemented by hand in `resources/css/app.css`, and that reimplementation has
gaps. `BladeOrphanCssClassTest` fails on any class written in a Blade file that
produces no rule in the compiled bundle, so run `npm run build` before the test
suite when you touch a view.

Two consequences worth remembering: Bootstrap's JavaScript is absent, so
`data-bs-*` attributes are inert and need a real listener; and Tailwind's
preflight resets heading sizes, so a bare `<h5>` renders at body size and needs
the `.h5` class.

**Every calendar colour lives in `resources/css/variables.css`** as a token, and
a test enforces it. Never write a hex value or `rgb()` in a calendar rule.

The house pattern for a form screen is cards: `card shadow-sm border-0 mb-4`,
a `card-header bg-white` with an `.h5` title, a `card-body` holding
`row g-3` columns, and a right-aligned `d-flex justify-content-end gap-2`
action bar at the end. A card that hosts a `data-choices` select also needs
`card--overflow-visible`, or the dropdown is clipped at the card border.

## Tests

Every change gets a test, and the test goes in the same commit as the change it
covers. Prefer asserting the **root cause** over the symptom, and say in the
docblock what used to be broken — a test whose name and comment explain the old
defect survives refactoring better than one that just checks a class is present.

Watch for hardcoded future dates: several suites schedule against literals that
were future when written. Freeze the clock with `Carbon::setTestNow` in `setUp`
rather than bumping the literals.

Run the narrowest useful selection (`--filter`, or a single file), then the
affected `--group` before saying it works. Report failures with their output;
never claim green without having seen it.
