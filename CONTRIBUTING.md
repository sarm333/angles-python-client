# Contributing

Thanks for contributing!

## Development setup

```bash
python -m venv .venv
source .venv/bin/activate
python -m pip install -U pip
pip install -e .
```

## Build locally

```bash
python -m pip install -U build twine
python -m build
python -m twine check dist/*
```

## Release (GitHub Actions + Trusted Publishing)

This repo is set up to publish to **PyPI** when you push a git tag matching `v*` (e.g. `v1.0.1`).

1. Bump `project.version` in `pyproject.toml`
2. Commit and push to `main`
3. Tag and push:

   ```bash
   git tag v1.0.1
   git push origin v1.0.1
   ```

The `Publish to PyPI` workflow will build and publish.

### TestPyPI (optional)

Push a tag `test-v1.0.1` (or trigger the workflow manually) to publish to TestPyPI first.
