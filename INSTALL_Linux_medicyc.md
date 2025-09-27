# INSTALLATION DU POSTE DE TRAVAIL DEBIAN

## Conventions
- `$` commande sous utilisateur lambda
- `#` commande avec root

## Commandes utiles de base
```bash
whoami          # Confirmer sous quel utilisateur je suis
env             # Détails de l'environnement
history         # Historique des commandes SHELL
```

## Vérification des mises à jour disponibles
```bash
apt-get update      # Mise à jour des paquets disponibles
apt-get upgrade     # Installer la mise à jour
```

## Vérification des connexions réseau

### État des interfaces
```bash
ip link                         # Lister les interfaces
ip link set enp0s31f6 up        # Activer ethernet
ip link set wlp113s0 up         # Activer wifi
```

### Gestion du blocage radio
```bash
rfkill list                     # Afficher la liste et le statut
rfkill unblock all              # Débloquer les services fermés
```

### Configuration réseau
```bash
nano /etc/network/interfaces    # Modifier le fichier interface
systemctl restart networking.service   # Redémarrer le service réseau
```

## Création des utilisateurs
```bash
adduser medicyc
adduser moodle
```

### Déplacer le répertoire utilisateur
```bash
# Créer le répertoire parent si nécessaire
mkdir -p /home/users

# Déplacer le répertoire utilisateur (pour les paramètres Apache)
usermod -d /home/users/medicyc -m medicyc
```

## Surveillance système
```bash
cat /proc/interrupts            # Observer les interruptions systèmes
```

## ⚠️ Attention - Substitution de commandes

**Important :** `medicyc` est interprété par le shell comme une commande à exécuter

### Différence entre guillemets et substitution :
```bash
echo "ls"       # Les guillemets protègent le texte, affiche : ls
echo `ls`       # Les backticks exécutent la commande ls et substituent le résultat
echo $(ls)      # Syntaxe moderne équivalente aux backticks
```

## Gestion des sources APT
```bash
nano /etc/apt/sources.list      # Vérification et édition des sources utilisées
```

## Installation de Google Chrome
```bash
# Télécharger le fichier depuis chrome.google.com
dpkg -i google-chrome-stable_current_amd64.deb
```

## Niveaux d'installation des programmes
1. **dpkg** - niveau bas (installation directe)
2. **apt** - niveau moyen (gestion des dépendances)
3. **aptitude** - niveau haut (résolution intelligente des conflits)

```bash
apt-get install aptitude        # Installation d'aptitude
```

## Configuration SSH

### Création d'une clé SSH
```bash
ssh-keygen                      # Générer une paire de clés
```

### Sauvegarde et copie des clés
```bash
cp id_rsa id_rsa.rebecca                    # Copie de la clé privée
cp id_rsa.pub id_rsa.rebecca.pub           # Copie de la clé publique
```

### Tests de connectivité
```bash
ping wp.pve.office.medicyc.local
ping moodle.pve.office.medicyc.local
```

## Installation d'OpenSSH
```bash
aptitude search ^openssh        # Rechercher les paquets OpenSSH
aptitude install openssh-server # Installer le serveur SSH
```

### Connexions SSH
```bash
# Une fois OpenSSH configuré, connexion à distance :
ssh utilisateur@adresse_ip
ssh medicyc@wp.pve.office.medicyc.local
ssh moodle@wp.pve.office.medicyc.local
```

## Interface graphique GNOME
```bash
apt install gnome              # Installation de l'interface graphique Debian
```

## Base de données MariaDB
```bash
aptitude search ^mariadb       # Rechercher les paquets disponibles
aptitude install mariadb-client mariadb-server  # Installation des paquets sélectionnés
```

## Base de données PostgreSQL
```bash
aptitude search ^postgrey      # Rechercher PostgreSQL
aptitude install postgresql-client postgresql-15  # Installation PostgreSQL
```

## Serveur web Apache2
```bash
aptitude install apache2       # Installation d'Apache2
```

## Configuration des modules Apache

### Activer les modules sur le serveur
Se placer dans le fichier conf du serveur :
```bash
cd /etc/apache2/mods-enabled/
```

### Récupérer la liste des modules sans doublons
```bash
# Lister tous les modules actifs
ls | sed -e 's/\.conf//g' -e 's/\.load//g' | awk '{printf "%s ", $1}'

# Version améliorée - sans doublons et triée
ls | sed -e 's/\.conf//g' -e 's/\.load//g' | sort -u | awk '{printf "%s ", $1}'
```

**Résultat obtenu :**
```
access_compat alias auth_basic authn_core authn_file authz_core authz_host 
authz_user autoindex deflate dir env filter mime mpm_itk mmp_prefork 
negotiation php8.2 proxy proxy_http reqtimeout rewrite setenvif status
```

### Copier la configuration en local
```bash
# Activer les mêmes modules sur le poste local
a2enmod access_compat alias auth_basic authn_core authn_file authz_core \
        authz_host authz_user autoindex deflate dir env filter mime \
        mpm_itk mpm_prefork negotiation php8.2 proxy proxy_http \
        reqtimeout rewrite setenvif status
```

## Installation de PHP

### Récupération de la liste des composants du serveur
```bash
# Installation complète des modules PHP nécessaires
aptitude install -y \
    libapache2-mod-php8.2 libphp-adodb php-bz2 php-common \
    php-composer-ca-bundle php-composer-class-map-generator \
    php-composer-metadata-minifier php-composer-pcre php-composer-semver \
    php-composer-spdx-licenses php-composer-xdebug-handler php-curl \
    php-fig-http-message-util php-gd php-getallheaders php-google-recaptcha \
    php-intl php-json-schema php-mariadb-mysql-kbs php-mbstring php-mcrypt \
    php-mysql php-nikic-fast-route php-phpmyadmin-motranslator \
    php-phpmyadmin-shapefile php-phpmyadmin-sql-parser php-psr-cache \
    php-psr-container php-psr-http-factory php-psr-http-message php-psr-log \
    php-react-promise php-seld-signal-handler php-slim-psr7 php-symfony-cache \
    php-symfony-cache-contracts php-symfony-config php-symfony-console \
    php-symfony-dependency-injection php-symfony-deprecation-contracts \
    php-symfony-expression-language php-symfony-filesystem \
    php-symfony-finder php-symfony-polyfill-php80 php-symfony-process \
    php-symfony-service-contracts php-symfony-string php-symfony-var-exporter \
    php-tcpdf php-twig php-twig-i18n-extension php-webmozart-assert \
    php-xml php-zip php8.2-bz2 php8.2-cli php8.2-common php8.2-curl \
    php8.2-gd php8.2-intl php8.2-mbstring php8.2-mcrypt php8.2-mysql \
    php8.2-opcache php8.2-pgsql php8.2-readline php8.2-soap php8.2-xml php8.2-zip
```

## Outils d'administration des bases de données

### phpMyAdmin
```bash
aptitude search ^php           # Rechercher phpMyAdmin
aptitude install phpmyadmin    # Installation
```

**Configuration requise :**
- Le paquet nécessite Apache configuré pour fonctionner correctement
- Une base de données doit être installée et configurée avant utilisation
- Peut être géré par dbconfig-common
- **Important :** Toujours créer un utilisateur principal avec mot de passe

### pgAdmin4 (pour PostgreSQL)
Installation depuis le site officiel selon le tutoriel :

```bash
# Configuration du dépôt
curl -fsS https://www.pgadmin.org/static/packages_pgadmin_org.pub | \
    sudo gpg --dearmor -o /usr/share/keyrings/packages-pgadmin-org.gpg

# Création du fichier de configuration du dépôt
sudo sh -c 'echo "deb [signed-by=/usr/share/keyrings/packages-pgadmin-org.gpg] \
    https://ftp.postgresql.org/pub/pgadmin/pgadmin4/apt/$(lsb_release -cs) \
    pgadmin4 main" > /etc/apt/sources.list.d/pgadmin4.list && apt update'

# Installation - choisir selon vos besoins :
sudo apt install pgadmin4              # Mode bureau ET web
sudo apt install pgadmin4-desktop      # Mode bureau uniquement  
sudo apt install pgadmin4-web          # Mode web uniquement

# Configuration du serveur web (si pgadmin4-web installé)
sudo /usr/pgladmin4/bin/setup-web.sh
```

## Configuration de l'application Moodle

### Connexion locale
```
http://moodle.local/
```

### Copie des fichiers de données
```bash
# Synchronisation des données depuis le serveur vers local
moodle@rebecca-laptop:~$ rsync -auv moodle@wp.pve.office.medicyc.local:data/ data/
```

### Paramétrage de config.php
Le fichier se trouve dans le répertoire `www` :

```bash
# Sauvegarde du fichier original
cp config.php config.prod.php          # Configuration production
cp config.php config.dev.php           # Configuration développement

# Création du lien symbolique vers l'environnement choisi
ln -sf config.dev.php config.php       # Pointer vers développement
```

### Modification de la configuration
```bash
nano config.dev.php
```

**Paramètre important à modifier :**
```php
$CFG->wwwroot = 'http://moodle.local';
```

## Création des bases de données

### Pour MariaDB
Voir documentation "parametresMedicyc"

### Pour PostgreSQL  
Voir documentation "parametresMoodle"

---

## Notes importantes

- ⚠️ Vérifier les permissions après déplacement des répertoires utilisateurs
- ⚠️ Toujours sauvegarder les fichiers de configuration avant modification
- ⚠️ Tester les connexions après chaque étape de configuration
- ⚠️ Utiliser des mots de passe forts pour tous les comptes utilisateurs et bases de données