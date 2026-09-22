# Class 07: repository, trigger and image evidence

Use actual output. Replace each blank; do not copy the acceptance text as a result.

## Step 1 — Create the repository and pipeline
- Repository URL: https://github.com/UriPros/class07-python-app
- Workflow path: `.github/workflows/ci.yml`
- First passing run URL and source commit: https://github.com/UriPros/class07-python-app/actions/runs/35757726650 and the commit `8dfa6aa679629cbad731e64f54cc19aaa510d8d4`
- Actual unit-test result: job `test`, step `Source contract:` `Ran 5 tests` and they all were `OK`

## Step 2 — Run only on pushes to main
- Commit/run that installed the main-only trigger: https://github.com/UriPros/class07-python-app/actions/runs/35759069602 and the commit `1782bd645b6a4f079572dba547c5390dd1326a28`
- `trigger-check` branch commit SHA: `8fd5b9b1d872b911cc2cbdc1d9b872df3f41c3c2`
- What the Actions page showed for that branch/SHA: There was no run for the push of `8fd5b9b` to `trigger-check`, the last one was the `step 2`
- Run URL after the same commit was pushed to `main`: https://github.com/UriPros/class07-python-app/actions/runs/35761612709
- Explain why a local commit alone does not start GitHub Actions: a commit exists in the local repository until it us pushed. Until the command `git push` is not sent, github does not know that the commit exists

## Step 3 — Publish and retrieve the Python image
- Package page URL (GHCR, linked to this repository): https://github.com/UriPros/class07-python-app/pkgs/container/class07-python-app
- Source commit, run URL and attempt: `d58782065d32e445769059eb7c63d505ccd9c634` — https://github.com/UriPros/class07-python-app/actions/runs/35762811509 — attempt `1`
- Actual source-test and packaged HTTP test results: job `test`: `Ran 5 tests` `OK`; job `package`, step *Test the packaged application*: `PASS: health + 3 HTTP scoring cases` (both passed before publish)
- Complete registry reference (`repository@sha256:` plus 64 hexadecimal digits): `ghcr.io/uripros/class07-python-app@sha256:5e56123aa192992f88b59cd3652f157a4a88e4eac697f8e276c08dc4864e135c`
- Platform: `linux/amd64`
- Exact pull command: `docker pull --platform linux/amd64 ghcr.io/uripros/class07-python-app@sha256:5e56123aa192992f88b59cd3652f157a4a88e4eac697f8e276c08dc4864e135c` (package set to public; no local login needed). Output ended with `Digest: sha256:5e56123aa192992f88b59cd3652f157a4a88e4eac697f8e276c08dc4864e135c` and `Status: Downloaded newer image`. No local rebuild.
- Actual pulled-image HTTP test output (`bash ci/verify_image.sh ghcr.io/uripros/class07-python-app@sha256:5e56123aa192992f88b59cd3652f157a4a88e4eac697f8e276c08dc4864e135c` on a MacBook Apple Silicon, amd64 emulated by Docker Desktop 4.90.0):
  ```text
  PASS: health + 3 HTTP scoring cases
  172.17.0.1 - - [22/Sep/2026 17:53:41] "GET /health HTTP/1.1" 200 -
  172.17.0.1 - - [22/Sep/2026 17:53:41] "GET /score?value=0.2 HTTP/1.1" 200 -
  172.17.0.1 - - [22/Sep/2026 17:53:41] "GET /score?value=0.5 HTTP/1.1" 200 -
  172.17.0.1 - - [22/Sep/2026 17:53:41] "GET /score?value=0.9 HTTP/1.1" 200 -
  ```

## Limits and explanation
- One thing these tests do not establish: that the image is secure or trustworthy. The labels are unsigned metadata (no signed provenance), the base image is not scanned for vulnerabilities, and the smoke test only checks 3 values plus `/health`, so it says nothing about load, concurrency or other inputs.
- Explain the difference between the Git repository and its linked image package: the repository (`github.com/UriPros/class07-python-app`) stores the source code and its history as commits. The package (`ghcr.io/uripros/class07-python-app`) stores the built, runnable container image, identified by its digest. They are separate stores with separate visibility and permissions; the `org.opencontainers.image.source` label links the package to the repository and the `revision` label records which commit it was built from.
- AI assistance used (tool, task, verification), or `none`: Claude (Anthropic, Cowork mode). Tasks: step-by-step guidance through the lab, explaining Git/Actions/Docker commands, completing the 4 TODOs of the package job, and drafting/filling parts of this evidence file from my real outputs. Verification: I ran every command myself; the workflow was validated as YAML before pushing; all run URLs, SHAs, the digest and test outputs were copied from GitHub Actions and my terminal, not generated. No credentials were shared with the AI.

## Optional failure-and-repair extension
- Failed commit/run, useful assertion and skipped package job:
- Repaired commit/run and recovered digest:

Save the successful package job summary, or paste its release record above.
If using a fallback, explicitly mark the uncompleted hosted checks and local
simulation results. Do not invent a repository, run URL or successful GHCR push.

## Package job summary (release record)
```text
source=d58782065d32e445769059eb7c63d505ccd9c634
run=https://github.com/UriPros/class07-python-app/actions/runs/35762811509
attempt=1
checks=source unit tests + packaged HTTP smoke test passed before publish
image=ghcr.io/uripros/class07-python-app@sha256:5e56123aa192992f88b59cd3652f157a4a88e4eac697f8e276c08dc4864e135c
platform=linux/amd64
```
