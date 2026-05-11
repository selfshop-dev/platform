# platform
Centralized CI/CD platform for the [`selfshop-dev`](https://github.com/selfshop-dev) organization. Contains reusable workflows and rulesets for branch protection.

## Rulesets
Ready-to-use rulesets for new repositories are stored in [`rulesets/`](rulesets/).

| Ruleset | Branch | Purpose |
|---|---|---|
| [`protect-main-branch`](rulesets/protect-main-branch.json) | `main` | Full protection: CI + Security + CodeQL + Trivy |
| [`protect-main-branch-minimal`](rulesets/protect-main-branch-minimal.json) | `main` | Minimal protection: PR only + linear history, no CI checks |

### Rule Comparison
| Rule | `protect-main-branch` | `protect-main-branch-minimal` |
|---|---|---|
| Deletion prevention | ✅ | ✅ |
| Force push prevention | ✅ | ✅ |
| Linear history | ✅ | ✅ |
| Required PR | ✅ | ✅ |
| Thread resolution | ✅ | ✅ |
| Lint + Test | ✅ | ❌ |
| CodeQL | ✅ | ❌ |
| Trivy | ✅ | ❌ |
| Strict status checks | ✅ | ❌ |
| Allowed merge methods | squash, rebase | squash, rebase |

**Apply to a new repository:**
```bash
gh api repos/selfshop-dev/REPO-NAME/rulesets \
  --method POST \
  --input <(curl -s https://raw.githubusercontent.com/selfshop-dev/platform/main/rulesets/protect-main-branch.json)
```
> Replace `REPO-NAME` with the repository name.

## Reusable Workflows
All workflows follow a Platform Engineering approach — a single source of truth for the entire organization.

```yaml
jobs:
  ci:
    uses: selfshop-dev/platform/.github/workflows/ci.yml@v1
    secrets: inherit
```

### Versioning
Workflows are versioned via Git tags using the `vMAJOR.MINOR.PATCH` scheme with floating major tags.

| Tag | Behavior |
|---|---|
| `@v1` | Floating tag — automatically receives all patches and minor updates |
| `@v1.2.3` | Exact version — never changes |
| `@main` | Unstable — do not use in service repositories |

A breaking change in a workflow triggers a new major tag (`v2`). Everything else is a patch/minor update under the same `v1`.

Floating tags (`v1`, `v1.2`) are updated automatically on release via [`update-floating-tags.yml`](.github/workflows/update-floating-tags.yml).

## License
[`MIT`](LICENSE) © 2026-present [`selfshop-dev`](https://github.com/selfshop-dev)