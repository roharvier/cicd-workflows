# CI/CD Workflows

Ce dépôt contient une collection de workflows GitHub Actions réutilisables pour la validation des Pull Requests et des commits.

## 📋 Workflows disponibles

### 1. Require Assignee
Vérifie qu'une Pull Request a au moins un assignee.

**Fichier**: `.github/workflows/require-assignee.yml`

### 2. Require Conventional Commits
Vérifie que tous les commits d'une Pull Request respectent le format [Conventional Commits](https://www.conventionalcommits.org/).

**Fichier**: `.github/workflows/require-conventional-commits.yml`

**Format attendu**: `type(scope?): description`

**Types valides**: `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `chore`, `build`, `ci`, `revert`

**Exemples**:
- ✅ `feat(auth): add login functionality`
- ✅ `fix: resolve memory leak`
- ✅ `docs: update README`
- ❌ `Update code` (ne respecte pas le format)

### 3. Global Workflow
Workflow principal qui appelle tous les autres workflows de validation.

**Fichier**: `.github/workflows/global.yml`

## 🚀 Utilisation dans d'autres dépôts

### ⚠️ Prérequis

Pour utiliser ces workflows dans d'autres dépôts, vous devez :

1. **Rendre ce dépôt accessible** :
   - Si le dépôt est **public** : vous pouvez l'utiliser directement
   - Si le dépôt est **privé** : les autres dépôts doivent avoir accès (même organisation ou accès configuré)

2. **Remplacer le chemin** : Dans tous les exemples ci-dessous, remplacez `romainharvier/cicd-workflows` par :
   - Votre nom d'utilisateur ou organisation GitHub
   - Le nom de ce dépôt

3. **Spécifier la branche** : Utilisez `@main`, `@master`, ou une autre branche/tag selon votre configuration

### Méthode 1 : Utiliser le workflow global (recommandé)

Créez un fichier `.github/workflows/ci-checks.yml` dans votre dépôt :

```yaml
name: CI Checks

on:
  pull_request:
    types: [opened, reopened, edited, assigned, unassigned, synchronize, ready_for_review]

jobs:
  ci-checks:
    uses: romainharvier/cicd-workflows/.github/workflows/global.yml@main
    secrets: inherit
```

**Note** : Remplacez `romainharvier/cicd-workflows` par le nom de votre organisation/utilisateur GitHub et le nom de ce dépôt.

### Méthode 2 : Utiliser les workflows individuellement

#### Vérifier l'assignee de la PR

```yaml
name: Check PR Assignee

on:
  pull_request:
    types: [opened, synchronize, reopened]

jobs:
  check-assignee:
    uses: romainharvier/cicd-workflows/.github/workflows/require-assignee.yml@main
    secrets: inherit
```

#### Vérifier les conventional commits

```yaml
name: Check Conventional Commits

on:
  pull_request:
    types: [opened, synchronize, reopened]

jobs:
  check-commits:
    uses: romainharvier/cicd-workflows/.github/workflows/require-conventional-commits.yml@main
    secrets: inherit
```

### Méthode 3 : Créer votre propre workflow combiné

Vous pouvez créer votre propre workflow qui appelle les workflows individuels selon vos besoins :

```yaml
name: Custom CI Checks

on:
  pull_request:
    types: [opened, synchronize, reopened]

jobs:
  check-assignee:
    uses: romainharvier/cicd-workflows/.github/workflows/require-assignee.yml@main
    secrets: inherit

  check-commits:
    uses: romainharvier/cicd-workflows/.github/workflows/require-conventional-commits.yml@main
    secrets: inherit
```

## 🔐 Permissions requises

Les workflows nécessitent les permissions suivantes :
- `pull-requests: read` (pour lire les informations des PR)
- `contents: read` (pour le workflow de vérification des commits)

Ces permissions sont définies dans chaque workflow et seront automatiquement appliquées lors de l'utilisation.

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
2. Définissez le workflow avec `workflow_call` pour le rendre réutilisable :
   ```yaml
   on:
     workflow_call:
   ```
3. Ajoutez l'appel dans `global.yml` si vous voulez qu'il soit inclus dans le workflow global
4. Mettez à jour ce README

## 📁 Exemples

Des exemples d'utilisation sont disponibles dans le dossier `examples/` :
- `example-usage.yml` : Utilisation du workflow global
- `example-individual.yml` : Utilisation des workflows individuellement

## 📄 Licence

MIT
