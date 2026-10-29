# aeogeo lint: GitHub Action

Checks your HTML and Markdown against the [aeogeo](https://aeogeo.site) rulebook for AI-search visibility (AEO/GEO) on every pull request. The checks are deterministic, and no LLM reads your content.

```yaml
name: AEO check
on:
  pull_request:
    paths: ['**/*.html', '**/*.md', 'site/**']

jobs:
  aeogeo:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: aeogeosite/action@v1
        with:
          api_key: ${{ secrets.AEOGEO_API_KEY }}
          paths: site/dist
```

Failed checks show up as annotations on the pull request. The full report is uploaded as the `aeogeo-report` artifact, and the `json_report` output carries it for a coding agent to read.

## Inputs

| Input | Default | |
|---|---|---|
| `api_key` | (required) | Your aeogeo API key. Store it as the repository secret `AEOGEO_API_KEY`. |
| `paths` | `**/*.html **/*.htm **/*.md` | Files, folders or globs, space-separated. |
| `content_types` | (all) | Comma-separated content types to filter checks by, such as `blog,landing`. |
| `fail_on` | `error` | `error` or `warn`: the lowest severity that fails the job. |
| `allow_stale` | `false` | Use an expired cached ruleset when the API is unreachable. |
| `cli_version` | pinned | The [`aeogeo`](https://www.npmjs.com/package/aeogeo) CLI version from npm. |

## Outputs

- `exit_code`: 0 means passed, 1 means a check at or above `fail_on` failed, and 2 means an operational error.
- `artifact_path`: where the JSON report was written.
- `json_report`: the report as one line of JSON.

The Action wraps the `aeogeo` CLI, which also runs locally: `npx aeogeo lint site/dist`. Its source is maintained at aeogeo.site; this repository holds the released Action.
