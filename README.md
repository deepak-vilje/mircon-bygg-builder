# Mircon Bygg Public Builder

This directory is the public builder-repo payload for `deepak-vilje/mircon-bygg`.

The source repo stays private. The public builder repo only contains a workflow
that clones the private source with `SOURCE_REPO_TOKEN`, builds on free public
GitHub-hosted runners, and uploads release assets back to the private source
repo.

## One-time setup

1. Create a public repo, for example `deepak-vilje/mircon-bygg-builder`.
2. Copy `.github/workflows/build-private-source.yml` from this directory into
   that public repo.
3. Add a builder-repo secret named `SOURCE_REPO_TOKEN`.

Use a fine-grained PAT scoped only to `deepak-vilje/mircon-bygg` with:

- `Contents: Read and write` (clone source and create/upload releases)
- `Metadata: Read`

Optional builder-repo secrets, used only to attach a runtime `.env` release
asset:

- `ANTHROPIC_API_KEY`
- `ANTHROPIC_BASE_URL`
- `ANTHROPIC_MODEL`
- `OPENAI_API_KEY`
- `OPENAI_BASE_URL`
- `OPENAI_MODEL`

Do not add `pull_request`, `pull_request_target`, or automatic `push` triggers
to the public builder workflow. Keep it manual-dispatch only so public
contributions cannot run with the private source token.

## Run a build

From the builder repo UI, run **Build Private Mircon Bygg Source** and set:

- `source_ref`: `main`, a tag, or a commit SHA from the private source repo.
- `release_tag`: optional. Leave blank for `build-YYYYMMDD-HHMMSS`.
- `run_tests`: keep enabled unless you need a packaging-only rebuild. This runs
  backend and frontend checks; the LLM contract guard remains a private-repo PR
  policy rather than a manual release-build gate.

Equivalent CLI:

```bash
gh workflow run build-private-source.yml \
  -R deepak-vilje/mircon-bygg-builder \
  -f source_ref=main \
  -f run_tests=true
```

The workflow uploads binaries to the private source repo release, not to the
public builder repo.
