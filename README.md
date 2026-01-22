# CI/CD Workflows

Ce dépôt contient une collection de workflows GitHub Actions réutilisables pour la validation des Pull Requests et des commits.

## 📋 Workflows disponibles

### 1. Check PR Assignee
Vérifie qu'une Pull Request a au moins un assignee avant d'être mergée.

**Fichier**: `.github/workflows/check-pr-assignee.yml`

### 2. Check Conventional Commits
Vérifie que tous les commits d'une Pull Request respectent le format [Conventional Commits](https://www.conventionalcommits.org/).

**Fichier**: `.github/workflows/check-conventional-commits.yml`

**Format attendu**: `type(scope?): description`

**Types valides**: `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `chore`, `build`, `ci`, `revert`

**Exemples**:
- ✅ `feat(auth): add login functionality`
- ✅ `fix: resolve memory leak`
- ✅ `docs: update README`
- ❌ `Update code` (ne respecte pas le format)

### 3. CI/CD Wrapper
Workflow principal qui appelle tous les autres workflows de validation.

**Fichier**: `.github/workflows/ci-cd-wrapper.yml`

## 🚀 Utilisation dans d'autres dépôts

### Méthode 1 : Utiliser le workflow wrapper (recommandé)

Créez un fichier `.github/workflows/ci-cd.yml` dans votre dépôt :

```yaml
name: CI/CD Checks

on:
  pull_request:
    types: [opened, synchronize, reopened]

jobs:
  ci-cd-checks:
    uses: romainharvier/cicd-workflows/.github/workflows/ci-cd-wrapper.yml@main
    with:
      check_assignee: true
      check_assignee_fail: true
      check_commits: true
      check_commits_fail: true
```

### Méthode 2 : Utiliser les workflows individuellement

#### Vérifier l'assignee de la PR

```yaml
name: Check PR Assignee

on:
  pull_request:
    types: [opened, synchronize, reopened]

jobs:
  check-assignee:
    uses: romainharvier/cicd-workflows/.github/workflows/check-pr-assignee.yml@main
    with:
      fail_if_no_assignee: true
```

#### Vérifier les conventional commits

```yaml
name: Check Conventional Commits

on:
  pull_request:
    types: [opened, synchronize, reopened]

jobs:
  check-commits:
    uses: romainharvier/cicd-workflows/.github/workflows/check-conventional-commits.yml@main
    with:
      fail_if_invalid: true
```

## ⚙️ Paramètres du wrapper

Le workflow wrapper accepte les paramètres suivants :

| Paramètre | Description | Défaut |
|-----------|-------------|--------|
| `check_assignee` | Activer la vérification de l'assignee | `true` |
| `check_assignee_fail` | Échouer si pas d'assignee | `true` |
| `check_commits` | Activer la vérification des commits | `true` |
| `check_commits_fail` | Échouer si commits invalides | `true` |

### Exemple avec paramètres personnalisés

```yaml
name: CI/CD Checks

on:
  pull_request:
    types: [opened, synchronize, reopened]

jobs:
  ci-cd-checks:
    uses: romainharvier/cicd-workflows/.github/workflows/ci-cd-wrapper.yml@main
    with:
      check_assignee: true
      check_assignee_fail: false  # Avertissement seulement, ne bloque pas
      check_commits: true
      check_commits_fail: true
```

## 🔧 Développement local

Pour tester les workflows localement, vous pouvez utiliser [act](https://github.com/nektos/act) :

```bash
# Installer act
brew install act

# Tester un workflow
act pull_request
```

## 📝 Ajouter un nouveau workflow

1. Créez un nouveau fichier dans `.github/workflows/` avec le suffixe `.yml`
2. Définissez le workflow avec `workflow_call` pour le rendre réutilisable
3. Ajoutez l'appel dans `ci-cd-wrapper.yml`
4. Mettez à jour ce README

## 📄 Licence

MIT
