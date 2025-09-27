# Initier un dépot GIT en ligne de commande

## Résumé en une seule suite de commandes :
```BASH
git init
echo ".vs/" >> .gitignore
echo "bin/" >> .gitignore
echo "obj/" >> .gitignore
echo "appsettings.Development.json" >> .gitignore
echo "appsettings.Local.json" >> .gitignore
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin https://github.com/*ton-user*/mon-projet.git
git push -u origin main
```

## commit "safe"
```BASH
git status
git add -u
git add DTOs/TacheCreateDto.cs DTOs/TacheUpdateDto.cs DTOs/TacheReadDto.cs -- ajout des fichiers créer
git diff --cached --name-only
git commit -m "DTOs: Create/Update/Read + mapping + contrôleur ajusté"
git push origin main
```

## Vérifier Git et configurer ton identité
``` BASH 
git --version
```

### Configurer ton identité (si pas déjà fait) :
``` BASH
git config --global user.name "TonNom" 
git config --global user.email "ton.email@example.com"
```

## Initialiser
Dans ton projet :
``` BASH
cd mon-projet
git init
```
Cela crée le dossier caché .git.

## Créer un fichier .gitignore
Exemple (pour un projet Node.js) :
``` BASH
echo "node_modules/" >> .gitignore
echo ".env" >> .gitignore
```
Tu peux aussi utiliser un modèle depuis gitignore.io

## Ajouter et valider les fichiers
``` BASH
git add .
git commit -m "Initial commit"
```

## Connecter ton dépôt local au dépôt distant
``` BASH
git remote add origin https://github.com/ton-user/mon-projet.git
```

## Envoyer ton code
``` BASH
git branch -M main     # renomme master en main
git push -u origin main
```

## verifier qu'un fichier n'est pas suivi
```BASH
git ls-files appsettings.json
```

## verifier qu 'un fichier est bien ignoré
```BASH
git check-ignore -v appsettings.json
```
##  Ne stage QUE les fichiers déjà suivis (modifiés/supprimés)
```BASH
git add -u
```

## retire les chemins des fichiers que tu ne veux plus suivre
```BASH
git rm -r --cached -- ".vs/GestionTachesAPI"
git commit -m "stop tracking .vs restants"
git push origin main
```
## 
```BASH

```