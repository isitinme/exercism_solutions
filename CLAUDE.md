# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

Personal solutions to [Exercism](https://exercism.org) exercises, currently Python-only (a `c` track existed briefly and was dropped). Exercise solutions are synced into this repo automatically by Exercism's GitHub sync integration — commits with the message `[Sync Iteration] python/<exercise>/<n>` and branches named `exercism-sync/<hash>` (merged via PR) are bot-generated, not hand-authored. Don't be surprised by that commit pattern in `git log`.

## Directory layout — two exercise formats

`solutions/python/<exercise-name>/` holds each exercise, but exercises exist in one of two shapes depending on when/how they were pulled in:

1. **Numbered-iteration format** (older, from the Exercism sync bot): `<exercise>/<n>/<module>.py`, e.g. `black-jack/1/black_jack.py`, `black-jack/2/black_jack.py`. Each numbered folder is one submitted iteration. **No test files are kept for these** — they were validated by the Exercism platform at submission time and cannot be re-run locally.
2. **Full CLI-download format** (newer, e.g. `resistor-color`, `resistor-color-trio`, `rna-transcription`): produced by `exercism download`, containing `.exercism/{config.json,metadata.json}`, `README.md`, `HELP.md`, `<module>.py`, and `<module>_test.py`. These **can** be run locally with pytest. Prefer this format going forward.

`solutions/python/PROGRESS.md` tracks learning progress, concepts covered, and a running list of cleanup/style items found during review — check it for current state before assuming an exercise is "done" or bug-free.

## Commands

All commands run from `solutions/python/`:

```bash
# one-time setup
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt

# run every exercise that has tests
pytest

# run a single exercise
pytest resistor-color-trio

# run a single test file / test
pytest resistor-color-trio/resistor_color_trio_test.py
pytest resistor-color-trio/resistor_color_trio_test.py::ResistorColorTrioTest::test_orange_orange_black
```

`pytest.ini` (in `solutions/python/`) declares one custom marker, `task`, used by Exercism's concept-exercise test suites — no other project-wide pytest config exists. There is no lint/format command wired up despite `pytest-pylint` being in `requirements.txt`.

Since exercises in the numbered-iteration format ship no test file, `pytest` only ever collects the CLI-download-format exercises; this is expected, not a missing-test bug.
