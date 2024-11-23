# Manuel d'installation du serveur Raspberry Pi

Bienvenue dans le manuel d'installation complet pour configurer et déployer un serveur Raspberry Pi. Ce guide vous accompagnera étape par étape, de la configuration initiale à la mise en place de services supplémentaires sur votre serveur.

## Table des matières

1. [Prérequis](#prérequis)
2. [Installation du système d'exploitation](#installation-du-système-dexploitation)
3. [Configuration réseau](#configuration-réseau)
4. [Installation des dépendances](#installation-des-dépendances)
5. [Services supplémentaires](#services-supplémentaires)
6. [Sécurisation du serveur](#sécurisation-du-serveur)
7. [Dépannage](#dépannage)
8. [Contribuer](#contribuer)
9. [Licence](#licence)

## Prérequis

Avant de commencer, vous aurez besoin de :

- Un Raspberry Pi (modèle recommandé : [Raspberry Pi 4](https://www.raspberrypi.org/products/raspberry-pi-4-model-b/)).
- Une carte SD (minimum 8 Go, recommandée 16 Go ou plus).
- Une alimentation adaptée pour le Raspberry Pi.
- Un câble réseau ou une connexion Wi-Fi (selon votre configuration).
- Un clavier, une souris et un écran pour la configuration initiale (ou un accès SSH après configuration).

## Installation du système d'exploitation

1. Téléchargez [Raspberry Pi Imager](https://www.raspberrypi.org/software/) et installez-le sur votre ordinateur.
2. Choisissez le système d'exploitation souhaité, tel que [Raspberry Pi OS](https://www.raspberrypi.org/software/operating-systems/), et gravez-le sur votre carte SD.
3. Une fois l'image écrite, insérez la carte SD dans votre Raspberry Pi et démarrez-le.
4. Suivez les instructions à l'écran pour configurer la langue, le fuseau horaire et la connexion réseau.

## Configuration réseau

### Connexion Wi-Fi

Si vous utilisez une connexion Wi-Fi :

1. Ouvrez le menu des paramètres et sélectionnez "Wi-Fi".
2. Choisissez votre réseau et entrez le mot de passe.

### Connexion Ethernet

Si vous utilisez une connexion Ethernet :

1. Branchez un câble Ethernet entre le Raspberry Pi et votre routeur.
2. La connexion réseau sera automatiquement établie.

## Installation des dépendances

Pour installer les outils nécessaires à la configuration de votre serveur, exécutez les commandes suivantes dans le terminal :

```bash
sudo apt update
sudo apt upgrade -y
sudo apt install -y <nom-des-paquets>

Remplacez <nom-des-paquets> par les dépendances spécifiques de votre serveur (par exemple, nginx, mysql-server, etc.).

## Services supplémentaires

Dans cette section, vous pouvez détailler les services supplémentaires que vous souhaitez configurer, comme :

- **Serveur web (NGINX)**
- **Serveur de bases de données (MySQL, PostgreSQL)**
- **Applications spécifiques (Node.js, Docker, etc.)**

### Exemple : Installation et configuration de NGINX

Pour installer NGINX sur votre Raspberry Pi :

```bash
sudo apt install nginx
sudo systemctl enable nginx
sudo systemctl start nginx


# Guide de déploiement d'un serveur sur Raspberry Pi

## Table des matières
- [Prérequis](#prérequis)
- [Installation des dépendances](#installation-des-dépendances)
- [Déploiement du serveur](#déploiement-du-serveur)
- [Services supplémentaires](#services-supplémentaires)
- [Sécurisation](#sécurisation)
- [Dépannage](#dépannage)
- [Contribution](#contribution)
- [Licence](#licence)

## Prérequis
- Un Raspberry Pi fonctionnel avec une distribution Linux installée
- Une connexion Internet stable
- Accès SSH ou clavier/écran connecté au Raspberry Pi
- Droits administrateur (sudo)

## Installation des dépendances

Mettez à jour votre système avant de commencer :

```bash
sudo apt update
sudo apt upgrade -y
```

Installez les paquets nécessaires en remplaçant `<nom-des-paquets>` par vos besoins spécifiques :

```bash
sudo apt install <nom-des-paquets>
```

Exemples de paquets courants :
- `nginx` : Serveur web
- `mysql-server` : Base de données MySQL
- `postgresql` : Base de données PostgreSQL
- `nodejs` : Runtime Node.js
- `docker.io` : Docker

## Déploiement du serveur

### 1. Configuration de NGINX

```bash
sudo apt install nginx
sudo systemctl enable nginx
sudo systemctl start nginx
```

Configurez votre site web :
```bash
sudo nano /etc/nginx/sites-available/mon-site
```

### 2. Configuration de la base de données

Pour MySQL :
```bash
sudo mysql_secure_installation
```

Créez une nouvelle base de données :
```sql
CREATE DATABASE mabase;
CREATE USER 'monutilisateur'@'localhost' IDENTIFIED BY 'monmotdepasse';
GRANT ALL PRIVILEGES ON mabase.* TO 'monutilisateur'@'localhost';
FLUSH PRIVILEGES;
```

### 3. Déploiement des fichiers d'application

```bash
# Créez le répertoire de déploiement
sudo mkdir -p /var/www/mon-application
sudo chown -R $USER:$USER /var/www/mon-application

# Copiez vos fichiers
cp -r /chemin/vers/votre/application/* /var/www/mon-application/
```

## Services supplémentaires

### Docker
```bash
# Installation de Docker
sudo apt install docker.io
sudo systemctl enable docker
sudo systemctl start docker

# Ajout de l'utilisateur courant au groupe docker
sudo usermod -aG docker $USER
```

### Node.js
```bash
# Installation via NodeSource
curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash -
sudo apt install -y nodejs

# Installation des dépendances globales courantes
sudo npm install -g pm2
```

## Sécurisation

### 1. Modification du mot de passe par défaut
```bash
sudo passwd pi
```

### 2. Configuration du pare-feu UFW
```bash
sudo apt install ufw
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow ssh
sudo ufw allow http
sudo ufw allow https
sudo ufw enable
```

### 3. Sécurisation SSH
Éditez le fichier de configuration SSH :
```bash
sudo nano /etc/ssh/sshd_config
```

Modifications recommandées :
```
PermitRootLogin no
PasswordAuthentication no
MaxAuthTries 3
```

Redémarrez le service SSH :
```bash
sudo systemctl restart ssh
```

### 4. Installation de Fail2Ban
```bash
sudo apt install fail2ban
```

Créez une configuration locale :
```bash
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
sudo nano /etc/fail2ban/jail.local
```

Configuration recommandée :
```ini
[sshd]
enabled = true
bantime = 3600
findtime = 600
maxretry = 3
```

## Dépannage

### Problèmes réseau
1. Vérifiez la connexion :
```bash
ping -c 4 google.com
```

2. Vérifiez la configuration réseau :
```bash
ip addr show
```

3. Redémarrez les services réseau :
```bash
sudo systemctl restart networking
```

### Problèmes de services
Vérifiez les logs système :
```bash
sudo journalctl -xe
```

Vérifiez le statut d'un service spécifique :
```bash
sudo systemctl status nom-du-service
```

## Contribution
Les contributions sont les bienvenues ! Pour contribuer :
1. Fork le projet
2. Créez une branche pour votre fonctionnalité
3. Committez vos changements
4. Poussez vers la branche
5. Ouvrez une Pull Request

## Licence
Ce projet est sous licence MIT. Voir le fichier `LICENSE` pour plus de détails.
