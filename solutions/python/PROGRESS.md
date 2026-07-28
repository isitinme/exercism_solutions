# Python Track — Progress Spec

Snapshot date: 2026-07-28
Repo: exercism_solutions/solutions/python

## 1. Timeline

| Date       | Session focus                                  | Exercises touched |
|------------|-------------------------------------------------|--------------------|
| 2026-05-27 | First big batch — core "Learning" concept exercises | hello-world, guidos-gorgeous-lasagna, ghost-gobble-arcade-game, little-sisters-vocab, currency-exchange, card-games, making-the-grade, black-jack/1, chaitanas-colossal-coaster, inventory-management, meltdown-mitigation, tisbury-treasure-hunt, cater-waiter, little-sisters-essay, ellens-alien-game/1-2, plane-tickets, locomotive-engineer, mecha-munch-management/1-2 |
| 2026-06-03 | Revisions + first "practice" (non-concept) exercises | black-jack/2 (const naming fix), darts/1-2 (debug cleanup), grains, bob, pangram, leap |
| 2026-07-28 | Latest session (uncommitted in git status)      | resistor-color, resistor-color-trio, rna-transcription — first exercises pulled with full exercism CLI structure (`.exercism/`, real test files) and actually run through pytest |

**20/20 tests pass** on the 3 exercises that ship with test files (resistor-color, resistor-color-trio, rna-transcription). Everything before that was validated by the platform at submission time; no local test files were kept for those, so they can't be re-verified locally.

## 2. Concepts covered so far

Basics, booleans, strings, string formatting (`.format`), lists, list methods, loops, dicts, dict methods, tuples, sets, conditionals, multiple assignment / `*args` / `**kwargs`, classes (single, flat), generators, recursion (grains), string `translate`/`maketrans`.

## 3. Concepts not yet touched

Exceptions/custom error handling (only one `raise ValueError`, one bare `except...as e`), comprehensions as a deliberate concept (dict/set comprehensions never used; list comprehension used exactly once), multiple inheritance / composition, iterators (`__iter__`/`__next__`) vs. generators, decorators, context managers (writing your own, not just using `open()`), f-strings (mixed with `.format`, not standardized), type hints (used ad hoc in one file only), regex, packaging a multi-file program, CI.

## 4. Workflow shift worth noting

The two May/June batches were done in a lightweight style: one file per exercise, numbered by iteration (`1/`, `2/`), no test files kept, no `.exercism` metadata. The July batch switched to the real `exercism download` layout (metadata, config, official tests) and you're now running pytest locally. That's a better setup — keep using it going forward instead of the numbered-folder style.

## 5. Recurring patterns found in review

**Self-corrected already (good instinct, no action needed):**
- `black-jack/1→2`: renamed constants to `UPPER_SNAKE_CASE` (PEP8).
- `darts/1→2`, `mecha-munch-management/1→2`: removed leftover module-level `print()`/debug-call code before resubmitting.
- `ellens-alien-game/1→2`: replaced a manual accumulator loop with a list comprehension.

**Still present, worth a cleanup pass:**
- Leftover debug code left in the *kept* solution: `inventory-management/1/dicts.py` (`print(add_items(...))` at module level) and `plane-tickets/1/generators.py` (a `for i in g: print(i)` block at the bottom). Harmless at runtime but shouldn't ship in a "solution" file.
- Real bug: `plane-tickets/1/generators.py::generate_codes` — `postfix` is only assigned inside `if remain_space > 0:` but read unconditionally after. If a seat+flight_id string is ever exactly 12 chars, this raises `UnboundLocalError`. Not caught because the exercise's own test data never hits that branch.
- Redundant `list([...])` wrapper around list literals (`darts`, `resistor-color`, `resistor-color-trio`) — `list([...])` is always just `[...]`.
- `resistor-color/resistor_color.py::colors()` returns the live module-level list, not a copy — callers can mutate your internal state.
- Overengineered solution: `resistor-color-trio`'s `label()` builds the ohm value via `functools.reduce` + string concatenation + string-slicing instead of plain integer arithmetic (`idx1*10+idx2`, then `//1000`/`//1_000_000`). Works, but much harder to read than the problem calls for.
- Similar "clever over clear" pattern in `little-sisters-vocab::make_word_groups` (relies on a `str.join` side-effect trick) — revisit with a plain comprehension.
- `cater-waiter::categorize_dish` loops over *all* categories and keeps overwriting the match instead of breaking on the first (or most specific) hit — if a dish is a subset of more than one category set, you silently get the last one in dict order rather than the most specific. Couldn't be re-verified locally (no test file kept) — worth double-checking.
- String formatting is inconsistent: `.format()` in most of the May batch, f-strings only in `grains`. Pick one (f-strings — more readable, the modern default) and standardize.

## 6. Infra gaps (beyond the platform itself)

- No CI: `pytest.ini` + `requirements.txt` exist but nothing runs them automatically on push.
- `requirements.txt` includes `pytest-pylint` but no pylint config/invocation exists anywhere in the repo — either wire it up or drop the dependency.
- 5 files are currently untracked in git (`pytest.ini`, `requirements.txt`, `resistor-color/`, `resistor-color-trio/`, `rna-transcription/`) — not committed yet.
