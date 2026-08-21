---
name: rm-skill
description: Caique's standing working conventions, valid across all of his projects — how commits are split, prefixed and written, that they are always in English, that co-authorship is never added unless he asks, that pushing is his call and never yours, that code comments are sparse and in English, that identifiers are camelCase, classes PascalCase and database tables and columns snake_case, that every migration is reversible, that Clean Code and SOLID are the standing bar, and that a change is only finished once its tests and its documentation are in. Load this skill before creating any commit, when asked "what's left to commit", when asked to split, reorder or rewrite commits, and at the start of work in any of his repositories so the conventions are known before code is written rather than after. When unsure whether it applies, read it — it is short and it prevents rework.
---

# Caique's working conventions

Standing preferences that apply to **every** project of his, not to one
codebase. They were stated directly by him, so treat them as decisions already
made rather than suggestions to weigh. Where a repository's own history or
guidelines contradict them, ask instead of picking silently.

Only what he has actually stated belongs in this skill. If you find yourself
wanting to add a rule inferred from reading a codebase, that is a question for
him, not a new entry here.

## Design

Clean Code and SOLID are the standing bar, in every project. Both names are
broad enough to be agreed with and then ignored, so what follows is what they
buy in practice — the things whose absence he would notice.

**One reason to change per unit.** A function does one thing and its name says
which. A class with two reasons to change is two classes. A boolean parameter
that makes a function behave two different ways is two functions.

**Name by intent, not by type or mechanism.** `isRegisteredForDiscounts`, not
`discount()`. `WarehouseNormalizer`, not `StringHelper`. Avoid abbreviations —
the reader is never the person who just wrote it.

**Depend on abstractions, and inject them.** Take a collaborator through the
constructor instead of reaching for a global, a facade or a singleton in the
middle of a method. That is the difference between a unit you can test and one
you can only run.

**Guard clauses over nesting.** Handle the exceptional case and return early;
keep the happy path at the left margin.

**Do not build abstraction for a single case.** This is where SOLID gets
misapplied most: an interface per class, a factory with one implementation, a
strategy pattern for two branches. Two similar things are not duplication until
the third one shows up. Open/closed pays off when the axis of change is known,
not when it is imagined.

**Apply this to code you write.** Code you are only passing through gets
*proposed*, not restructured. An unrelated refactor buried inside a feature is
precisely what one-commit-per-change exists to prevent — if the cleanup is worth
doing it is worth its own `refactor:` commit, and worth saying so before doing
it.

## Naming

- variables, properties, parameters and methods: `camelCase`
- classes, interfaces, enums, traits and types: `PascalCase`

This is already what PHP (PSR-12) and JavaScript/TypeScript expect, which is
where most of his code lives, so in those projects it is simply the rule.

**A name that arrives from somewhere else keeps the spelling it arrives with.**
This is his rule too, not an exception to it. Database columns, request fields,
config keys, a payload from an external API, a third-party library's methods —
you do not get to rename what you do not own, and quietly "correcting" one of
them is a contract change dressed up as a style fix. `cost_center_id` stays
`cost_center_id`.

So the camelCase rule governs identifiers you declare, not data keys you
receive. See **Database** below for the schema side.

The one case still worth raising with him: a language whose own convention is
the opposite — Python and Rust use snake_case, enforced by their formatters — so
camelCase there fights the tooling on every commit. Ask rather than resolving it
silently in either direction.

Existing code written to another convention is left as it is unless he asks for
a rename. New code follows the rule.

## Database

**Tables and columns are `snake_case`.** Always, including a column you are
adding to a table that got it wrong before — one inconsistent name is cheaper
than a rename.

Do not extend this into pluralisation or prefixes. Whether tables are singular
or plural is the project's existing choice, and projects are often inconsistent
about it; look at the neighbouring tables and match them rather than imposing a
scheme. A schema that is half-renamed is worse than one that is consistently
odd.

**Every migration is reversible.** A real `down()` that undoes what `up()` did —
never empty, never a stub, never a comment explaining why it was skipped.

Then run it, because an untested `down()` is usually a broken one. Migrate,
roll back, migrate again. The common failure is dropping a column an index still
references, and it does not surface until someone tries to reverse it.

The cost of getting this wrong lands far from the migration. In one of his own
projects a `down()` that cannot run on SQLite is why the browser suite prepares
its database once and truncates between tests instead of migrating and rolling
back — the migration looked fine for a year, and the bill arrived as a
constraint on the test suite.

If a reversal genuinely destroys data that cannot be reconstructed, that is
worth telling him before writing it, not worth papering over with an empty
`down()` that claims a reversibility the schema does not have.

## Code comments

Write a comment only when it is genuinely necessary, and write it in **English**
— in every project, whatever language the surrounding code, the interface or the
conversation happens to use.

The bar is high on purpose. The code already says *what* it does; a comment earns
its place only when the *why* cannot be recovered by reading the code, and
getting it wrong would cost someone real time. Everything else is a second thing
to maintain that nobody updates, and a stale comment is worse than no comment
because it is believed.

Worth writing:

- a constraint that lives outside the code — an API quirk, a browser bug, a
  business or legal rule the code cannot state on its own
- a decision where the obvious alternative is the wrong one, so the next person
  does not helpfully "fix" it back
- a workaround that looks removable and is not

Not worth writing:

- restating what the line or the block does
- headers announcing self-evident groups of code
- explaining language or framework features
- docblocks that only repeat the signature

Before reaching for a comment, try making the code say it instead: a clearer
name, an extracted function, a named constant. Those cannot go stale.

This overrides the usual instinct to match the comment density of the
surrounding file. A heavily commented file is not permission to add more — write
what is necessary and nothing beyond it, even there. Leave existing comments
alone unless he asks: rewriting or translating them is its own job, separate
from the change in front of you.

## Tests

**Every change must be tested** — and the test is **its own commit**, separate
from the change it covers. A feature that needs coverage is therefore at least
two commits: the change, then a `test:` commit for the tests.

The order is change first, test second. That keeps the history sound at both
points: green before, because the test does not exist yet, and green after,
because the change is already in. Committing the test first would leave a red
commit in the middle, which defeats the point of ordering commits at all.

The exception is a change to the test *infrastructure* — a helper, a frozen
clock, a factory. That is a `test:` commit in its own right and it goes wherever
the dependency puts it, which is often before the change that needs it.

## Documentation

Every piece of development carries its own documentation, and **the work is not
complete until the documentation is**. Treat an undocumented change the way you
would treat an untested one: not done yet, whatever the code looks like.

When something changes, the documentation that describes it changes with it. This
is the half that rots, and it rots quietly — nobody notices a stale page until
somebody trusts it. If a change makes an existing page wrong, fixing that page is
part of the change, not a follow-up.

**Follow the project's standard when it has one.** Look before writing: a `docs/`
directory, architecture decision records, a README section, docblocks, a wiki.
Match its location, structure, depth and voice, even where you would have chosen
differently — consistency is worth more here than your preference.

**Choose one when the project has none.** Pick a form that fits the project's
size and stack, apply it consistently, and say which form you chose and why, so
he can correct it once instead of watching it drift.

Complete means someone who was not in the conversation can act on it: what the
thing does, how to use it, the decisions that are not visible in the code, and
what goes wrong if it is used incorrectly. A page that narrates the diff is not
documentation — the diff already exists.

Documentation is its own commit, `docs:`, on the same reasoning that tests get
theirs. It lands last, once the change and its tests are in.

Note that this reverses the common instruction to avoid creating documentation
unless asked. Some repositories say exactly that in their own agent guidelines.
His standing preference is the opposite, so write the documentation — and tell
him when a repository's guidelines contradict this, because the guidelines are
his to fix.

## Before saying it works

Run the tests the change affects, and show him the result. Not the whole suite
every time — the narrowest useful selection, then the affected group or file.

The rule behind it: never state that something works without having seen it
pass. If you could not run the tests — no environment, a missing dependency, a
browser that would not start — say that plainly instead of quietly assuming.
"I could not verify this" is useful to him; a confident claim that turns out to
be wrong costs him a round trip and some trust.

The same applies to a diagnosis. If you are reasoning about why something
behaves the way it does and cannot observe it, say which part is measured and
which part is inference, and be willing to be wrong about the second.

## When to ask, and when to decide

Decide. Ask only when the readings of a request lead to genuinely different
work — different files, a different approach, work that would be wasted if the
guess is wrong. Anything a careful colleague would settle on their own, settle,
and say which assumption you took.

The test is not "am I certain", it is "would the other reading change what I
build". A vague request with one sensible interpretation gets built. A request
like "the week view still shows the days", which could mean the styling failed
or that the columns should not be there at all, gets a question — those are two
different jobs and one of them would have been thrown away.

When you do ask, do everything that does not depend on the answer first, so the
question arrives with work already done behind it rather than instead of it.

## Commits

### One commit per change

A commit is one logical change, not one work session. A session that touched a
form layout, a validation rule and a CSS bug produces three commits, even though
all three sat in the same working tree at the same time.

The practical consequence is that a single file often belongs to more than one
commit. Splitting by file is the easy mistake here — split by change and accept
that you will be staging parts of files.

The rule cuts both ways, though. One change that genuinely touches twenty files
— a rename, a signature change, a new rule and the call sites it needs — is
still one commit. Do not fragment a single change to look thorough; the test is
whether each piece stands on its own and means something by itself.

Interactive git is usually unavailable in this environment, so `git add -p` is
not an option. If it does work, use it. Otherwise build the index directly:

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

| Prefix | Use it when the commit |
| --- | --- |
| `feat:` | adds a new capability to the system |
| `fix:` | corrects an error or a bug |
| `docs:` | changes documentation only |
| `style:` | adjusts formatting without touching behaviour (whitespace, semicolons) |
| `refactor:` | improves the code without changing what it does |
| `perf:` | improves speed or memory use |
| `test:` | creates or repairs tests |
| `chore:` | deals with maintenance tasks or tooling |

This list is closed — no other prefix, and no scope in parentheses unless he
asks for one.

Two boundaries that get blurred in practice. `style:` is formatting only: code
that reads better but behaves the same is `refactor:`. And a bug fixed as a side
effect of restructuring is still `fix:` if the fix is the point of the commit —
if it is incidental, split it out, because that is the whole reason commits are
one change each.

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
