# Instructions
## variables d'environnement à faire pour sécurisé le mot de passe
- A la racine créer un fichier .env 
```
SA_PASSWORD=votreMotDePasseSécurisé
```

- Premier lancement 
```
docker compose build
docker compose up -d
```
Attendre que le service db soit prêt (vérifier avec `docker compose logs -f db`)

- Creer la bdd et le jeu d'essai
 ``` 
# Copier les scripts
docker compose cp .\init.sql db:/tmp/init.sql
docker compose cp .\jeuDessai.sql db:/tmp/jeuDessai.sql   #pour les tests

# Exécuter les scripts
docker compose exec db sh -lc '/opt/mssql-tools18/bin/sqlcmd -S localhost -U sa -P "$MSSQL_SA_PASSWORD" -C -b -i /tmp/init.sql'
docker compose exec db sh -lc '/opt/mssql-tools18/bin/sqlcmd -S localhost -U sa -P "$MSSQL_SA_PASSWORD" -C -d LearnPlay -l 120 -b -i /tmp/jeuDessai.sql'  # optionnel
```
## pour verifier 
```
docker compose exec db sh -lc '/opt/mssql-tools18/bin/sqlcmd -S localhost -U sa -P "$MSSQL_SA_PASSWORD" -C -Q "SELECT name FROM sys.databases WHERE name = ''LearnPlay'';" -b'
docker compose exec db sh -lc '/opt/mssql-tools18/bin/sqlcmd -S localhost -U sa -P "$MSSQL_SA_PASSWORD" -C -d LearnPlay -Q "SELECT name FROM sys.tables;" -b'
```

# apres des modifications
```
docker compose up -d --build api
```
ou 
```
docker compose up -d --build web
```
# verifier les logs 
```
docker compose up -d gateway
docker compose logs -f gateway
```

## tester l'API
```
curl http://localhost:8080/utilisateurs
```

## Lancer 
```
http://localhost:8080/index.html
```


# tout supprimer : SURPPRIME TOUTES LES DONNEES
``` 
# 1. Arrêter et supprimer TOUT (conteneurs, réseaux, volumes)
docker compose down -v

# 2. Optionnel : supprimer les images pour forcer un rebuild complet
docker compose down --rmi all -v

# 3. Reconstruire depuis zéro
docker compose build --no-cache

# 4. Redémarrer
docker compose up -d

# 5. Attendre que la DB soit prête
docker compose logs -f db
# (Ctrl+C pour sortir une fois "ready" affiché)

# 6. Recréer la base de données
docker compose cp .\init.sql db:/tmp/init.sql
docker compose cp .\jeuDessai.sql db:/tmp/jeuDessai.sql
docker compose exec db sh -lc '/opt/mssql-tools18/bin/sqlcmd -S localhost -U sa -P "$MSSQL_SA_PASSWORD" -C -b -i /tmp/init.sql'
docker compose exec db sh -lc '/opt/mssql-tools18/bin/sqlcmd -S localhost -U sa -P "$MSSQL_SA_PASSWORD" -C -d LearnPlay -l 120 -b -i /tmp/jeuDessai.sql'
```
## dépannage rapide

- Mot de passe avec ! : quote-le toujours dans le shell ("$SA_PASSWORD").
- DB non prête : ajoute le healthcheck (plus haut) et/ou attends le log “ready”.
- sqlcmd introuvable : ton image db doit contenir mssql-tools18. Sinon, utilise une image utilitaire :
```
docker compose run --rm db /opt/mssql-tools18/bin/sqlcmd -S db -U sa -P "Nov!CDA04" -C -d LearnPlay -Q "SELECT name FROM sys.tables"
```
- Conflit de ports : vérifie docker compose ps et modifie les mappings (1433:1433, 8080:8080, etc.).
- Perte de données : n’utilise pas down -v si tu veux garder la base.
- Connexion API → DB : utilise Server=db,1433 (nom du service compose = DNS) + Encrypt=True;TrustServerCertificate=True avec SQLCMD v18.

## verifier le status
```
docker compose ps
```

## Commandes utiles
```
# Voir tous les conteneurs
docker compose ps

# Entrer dans un conteneur
docker compose exec api sh
docker compose exec db bash

# Voir les volumes
docker volume ls

# Supprimer un volume spécifique
docker volume rm learnplay_sqlserverdata

# Nettoyer Docker complètement (⚠️ supprime TOUT)
docker system prune -a --volumes
```
