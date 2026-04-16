# Python Project Template

A minimal starting point for a Python project wired up to the shared CI/CD workflows. Versioning is driven entirely by
git tags — no version number is stored in any source file. On every push to `main`, python-semantic-release analyses
commits, cuts a release if warranted, and dispatches a build and publish run automatically.

## Project structure

```none
.
├── src/
├── .github/
│   ├── workflows/
│   │   └── release.yaml        # main end-to-end workflow
│   └── sample_app/
│       ├── __init__.py
│       └── cli.py              # entry point: `sample-app`
├── tests/
│   └── test_cli.py
├── pyproject.toml              # Python project config
└── releaserc.toml              # Optional python-semantic-release config
```

## Configuration

### `pyproject.toml`

Edit the `[project]` section to set your package name, description, Python version constraint, and dependencies.
The `version` field is left as `dynamic` — setuptools-scm reads it from git tags at build time.

### `releaserc.toml` (optional)

> [!NOTE]
> **This is not required.** If you omit the file, PSR runs with its built-in defaults, which are reasonable for most projects.

`releaserc.toml` configures python-semantic-release (PSR): commit message format, changelog settings, branch patterns,
and pre-release tokens.

To use it, commit the file to your repo root and set `config-file: releaserc.toml` in the `release` job of your `ci.yaml`:

```yaml
  release:
    uses: stairwaytowonderland/python-reusable-workflows/.github/workflows/release.yaml@main
    permissions:
      contents: write
    with:
      config-file: releaserc.toml   # remove this line to use PSR defaults
    secrets: inherit
```

You can also point `config-file` at `pyproject.toml` if you prefer to keep PSR settings under `[tool.semantic_release]`
there instead of a separate file.

### `ci.yaml`

The template `ci.yaml` wires together three reusable workflows:

| Job       | Calls          | Runs when                                             |
| --------- | -------------- | ----------------------------------------------------- |
| `test`    | `test.yaml`    | Every push to `main`                                  |
| `release` | `release.yaml` | After `test` passes                                   |
| `publish` | `publish.yaml` | After `release`, only when a new version was released |

Adjust the `with:` inputs as needed — see the [workflow reference](https://github.com/stairwaytowonderland/python-reusable-workflows/blob/main/README.md#reusable-workflows-reference)
for all available options.
