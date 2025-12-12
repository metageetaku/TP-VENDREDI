- Pour commencer à utiliser GitFlow, il faut d'abord l'initialiser dans un dépôt git :

```bash
git flow init
```

# Branches de fonctionnalités (features)

- Les branches features sont utilisées pour développer des fonctionnalités spécifiques.

## Création d'une nouvelle feature

```bash
git flow feature start <nom_feature>
```

## Finir une feature

```bash
git flow feature finish <nom_feature>
```

# Branche de release

- Les branches de release sont utilisées pour préparer une nouvelle version de notre application. Correction de bugs, test ou la documentation.

## Création d'une release

```bash
git flow release start <version>
```

## Finir une release

```bash
git flow release finish <version>
```

# Branches de hotfix

- Les branches de hotfix sont utilisées pour corriger rapidement des bugs en production.

## Création d'un hotfix

```bash
git flow hotfix start <version>
```

## Finir un hotfix

```bash
git flow hotfix finish <version>
```

# Publier une feature ou une release

```bash
git flow feature publish <nom_feature>
```

# Récupérer une feature ou une release

```bash
git flow feature pull origin <nom_feature>
```
