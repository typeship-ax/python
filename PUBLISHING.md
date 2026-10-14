# Publishing typeship

How to build, release, and maintain this package. Its users need only [README.md](README.md).

## Build and test

Requires Python 3.11+. From this directory:

```sh
python3 -m venv .venv
source .venv/bin/activate
python -m pip install .
python -m unittest
```

On Windows, activate the environment with `.venv\Scripts\activate`.

`python -m unittest` runs the tests in `tests/` against a local stub server. They use only the standard library and are not part of the installed package.

## Name and version

`pyproject.toml` names this distribution `typeship` at version `0.26.0`. Raise `version` for every release.

## Publish

```sh
python -m pip install build twine
python -m build
python -m twine upload dist/*
```

## Customizing this package

- A custom file ships only when the package manifest, exports, build, and tests include it. Add a package check for every custom build or test step.
- Keep application-only wrappers outside this package. Code shipped from this package must pass the package's checks.
- When this package's repository receives reviewed regeneration pull requests, committed customizations are preserved and edits that overlap a generated change stop for review. Regenerating into a directory replaces its files.
