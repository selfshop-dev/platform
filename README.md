# platform

Централизованная CI/CD-платформа организации [`selfshop-dev`](https://github.com/selfshop-dev). Содержит переиспользуемые workflows и rulesets для защиты веток.

## Rulesets

Готовые rulesets для новых репозиториев хранятся в [`rulesets/`](rulesets/).

| Ruleset | Ветка | Назначение |
|---|---|---|
| [`protect-main-branch`](rulesets/protect-main-branch.json) | `main` | Полная защита: CI + Security + CodeQL + Trivy |
| [`protect-main-branch-minimal`](rulesets/protect-main-branch-minimal.json) | `main` | Минимальная защита: только PR + линейная история, без CI чеков |

### Сравнение правил

| Правило | `protect-main-branch` | `protect-main-branch-minimal` |
|---|---|---|
| Запрет удаления | ✅ | ✅ |
| Запрет force push | ✅ | ✅ |
| Линейная история | ✅ | ✅ |
| Обязательный PR | ✅ | ✅ |
| Thread resolution | ✅ | ✅ |
| Lint + Test | ✅ | ❌ |
| CodeQL | ✅ | ❌ |
| Trivy | ✅ | ❌ |
| Strict status checks | ✅ | ❌ |
| Разрешённые методы merge | squash, rebase | squash, rebase |

**Применить к новому репозиторию:**

```bash
gh api repos/selfshop-dev/REPO-NAME/rulesets \
  --method POST \
  --input <(curl -s https://raw.githubusercontent.com/selfshop-dev/platform/main/rulesets/protect-main-branch.json)
```

> Заменить `REPO-NAME` на имя репозитория.

## Reusable Workflows

Все workflows используются через Platform Engineering подход — один источник правды для всей организации.

```yaml
jobs:
  ci:
    uses: selfshop-dev/platform/.github/workflows/ci.yml@v1
    secrets: inherit
```

### Версионирование

Workflows версионируются через Git tags по схеме `vMAJOR.MINOR.PATCH` с floating major tags.

| Tag | Поведение |
|---|---|
| `@v1` | Floating tag — получает все патчи и минорные обновления автоматически |
| `@v1.2.3` | Точная версия — никогда не меняется |
| `@main` | Нестабильный — не использовать в сервисных репо |

Breaking change в workflow → новый major tag (`v2`). Всё остальное — patch/minor под тем же `v1`.

Floating tags (`v1`, `v1.2`) обновляются автоматически при публикации релиза через [`update-floating-tags.yml`](.github/workflows/update-floating-tags.yml).

### Доступные workflows

| Workflow | Назначение | Конфиг в репо |
|---|---|---|
| [`ci.yml`](.github/workflows/ci.yml) | Lint + Test с coverage upload | `.golangci.yml` |
| [`codeql.yml`](.github/workflows/codeql.yml) | Статический анализ кода (Go) | `.github/codeql.yml` |
| [`labeler.yml`](.github/workflows/labeler.yml) | Автоматическая расстановка меток PR и issues | `.github/labeler.yml` |
| [`release-drafter.yml`](.github/workflows/release-drafter.yml) | Автогенерация release notes | `.github/release-drafter.yml` |
| [`security.yml`](.github/workflows/security.yml) | Trivy filesystem scan (CRITICAL/HIGH) | — |

> Конфигурационные файлы уникальны для каждого репозитория и не наследуются.

## Лицензия

[`MIT`](LICENSE) © 2026-present [`selfshop-dev`](https://github.com/selfshop-dev)