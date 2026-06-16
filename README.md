# action-example

Example repo demonstrating [`shieldly-io/action`](https://github.com/shieldly-io/action) — AI-Powered AWS security analysis in GitHub Actions.

This repo intentionally contains IAM and CloudFormation misconfigurations so you can see Shieldly catch them in CI.

## What's in here

| File | What it is |
| --- | --- |
| `iac/policy.json` | IAM policy with overly broad `s3:*` on `*` |
| `iac/template.yaml` | CloudFormation template with a public S3 bucket and a broad Lambda IAM role |
| `.github/workflows/shieldly.yml` | Shieldly GitHub Action — runs on every push and PR |

## How to use this in your own repo

1. Get a free API key at **[shieldly.io/app/api](https://www.shieldly.io/app/api)**

2. Add it as a repository secret named `SHIELDLY_API_KEY`

3. Add the workflow:

```yaml
# .github/workflows/shieldly.yml
name: Shieldly Security Check
on: [push, pull_request]

jobs:
  security:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: AI-Powered IAM & CloudFormation security analysis
        uses: shieldly-io/action@v1
        with:
          api-key: ${{ secrets.SHIELDLY_API_KEY }}
          scan-path: ./iac          # directory or single file
          fail-on-severity: High    # Critical | High | Medium | Low | none
```

## Action inputs

| Input | Required | Default | Description |
| --- | --- | --- | --- |
| `api-key` | Yes | — | Shieldly API key (`sk_live_...`) |
| `scan-path` | Yes | — | Path to IAM JSON or CloudFormation YAML/JSON (file or directory) |
| `fail-on-severity` | No | `High` | Fail the workflow if findings at or above this severity are found |

## Action outputs

| Output | Description |
| --- | --- |
| `score` | Security score (0–100) |
| `risk-level` | `Critical` / `High` / `Medium` / `Low` / `None` |
| `findings-count` | Total number of findings |
| `critical-count` | Count of Critical findings |
| `high-count` | Count of High findings |

## Links

- [shieldly-io/action](https://github.com/shieldly-io/action) — GitHub Action source
- [shieldly.io](https://www.shieldly.io) — web-based IAM Advisor (free demo)
- [@shieldly/cli](https://www.npmjs.com/package/@shieldly/cli) — analyze from any terminal
- [@shieldly/cdk-guard](https://www.npmjs.com/package/@shieldly/cdk-guard) — CDK Construct for pre-deploy analysis
- [REST API reference](https://www.shieldly.io/docs/api)
