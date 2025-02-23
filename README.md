# Manuel d'installation du serveur Raspberry Pi

Bienvenue dans le manuel d'installation complet pour configurer et déployer un serveur Raspberry Pi. Ce guide vous accompagnera étape par étape, de la configuration initiale à la mise en place de services supplémentaires sur votre serveur.

Cette configuration s'adresse au système d'exploitation RaspOS Lite 64 bits.

## Table des matières

1. [Prérequis](#prérequis)
2. [Installation du système d'exploitation](#installation-du-système-dexploitation)
3. [Configuration réseau](#configuration-réseau)
4. [Installation des dépendances](#installation-des-dépendances)
5. [Services supplémentaires](#services-supplémentaires)
6. [Sécurisation du serveur](#sécurisation-du-serveur)
7. [Dépannage](#dépannage)

## Prérequis

Avant de commencer, vous aurez besoin de :

- Un Raspberry Pi (modèle recommandé : [Raspberry Pi 4](https://www.raspberrypi.org/products/raspberry-pi-4-model-b/)).
- Une carte SD (minimum 8 Go, recommandée 16 Go ou plus).
- Une alimentation adaptée pour le Raspberry Pi.
- Un câble réseau ou une connexion Wi-Fi (selon votre configuration).
- Un clavier, une souris et un écran pour la configuration initiale (ou un accès SSH après configuration).
- Ouvrir les ports sur la Box (22 pour ssh, 445 pour samba, 80 et 443 pour http/https)

  
## Installation du système d'exploitation

1. Téléchargez [Raspberry Pi Imager](https://www.raspberrypi.org/software/) et installez-le sur votre ordinateur.
2. Choisissez le système d'exploitation souhaité, tel que [Raspberry Pi OS](https://www.raspberrypi.org/software/operating-systems/), et gravez-le sur votre carte SD.
Maintenant, dans les réglages de Pi Imager, on peut écrire différents réglages pour éviter d'avoir besoin d'une interface graphique sur le Raspberry. Activez SSH, configurez un identifiant pour le raspberry et pour internet

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
```
Remplacez <nom-des-paquets> par les dépendances spécifiques de votre serveur (par exemple, nginx, mysql-server, etc.).

## Services supplémentaires

Dans cette section, vous pouvez détailler les services supplémentaires que vous souhaitez configurer, comme :

- **Serveur web (Apache2)**
- **Serveur de bases de données (MySQL, PostgreSQL)**
- **Applications spécifiques (Node.js, Docker, etc.)**

### Exemple : Installation et configuration de apache2

Pour installer apache2 sur votre Raspberry Pi :

```bash
sudo apt install apache2
sudo systemctl enable apache2
sudo systemctl start apache2
```

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


## Montage du disque externe
Il faut tout d'abord créer le dossier où les fichiers seront accessibles

```bash
sudo  mkdir /mnt/usb
```

On peut ensuite ajouter les droits à l'utilisateur sur le dossier

```bash
sudo chown -R $user:$user /mnt/usb
```

On recherche l'identifiant de notre disque pour l'ajouter un fichier prochainement

```bash
sudo fdisk -l
```

On identifie notre disque (en général dev/sda1) puis avec la commande suivante, on récupère son UUID

```bash 
sudo blkid /dev/sda1
```

Maintenant que l'on a notre UUID, on va pouvoir éditer le fichier fstab pour y ajouter notre disque

```bash
sudo nano /etc/fstab
```

On ajoute cette ligne à la fin du fichier

```bash
UUID=$UUID /mnt/usb ext4 defaults,auto,rw,nofail 0 1
```

Et on exécute la commande pour monter le disque à cet emplacement

```bash
sudo mount /mnt/usb
```

On termine par redémarrer systemd
```bash
sudo systemctl daemon-reload
```


## Installation de Samba

```bash 
sudo apt install samba
```

Vous pouvez ajouter un utilisateur système, qui aura son accès samba (à votre bon vouloir)

```bash
 sudo adduser $USER
```

Ensuite, modifier le mot de passe de l'utilisateur 

```bash
sudo smbpasswd -a $USER
```

Ensuite, modifier le fichier de configuration de samba pour la lecture/écriture des dossiers à partage

```bash 
sudo nano /etc/samba/smb.conf
```

Puis ajouter ceci

```bash 
[partage]
   path = /mnt/usb
   browseable = yes
   writeable = yes
   guest ok = yes
   read only = no
   create mask = 0777
   directory mask = 0777
   valid users = $USER
   guest ok = no
```

Pensez à remplacer les $USER par l'utilisateur de votre choix.


## Déploiement du serveur


### 1. Configuration de Apache2

```bash
sudo apt install apache2
sudo systemctl enable apache2
sudo systemctl start apache2
```

Configurez votre site web :
```bash
sudo nano /etc/apache2/sites-available/mon-site

# Remplacez <VirtualHost *:80> en en-tête par un autre port
```
Pensez à ne pas utiliser les ports 80 (Traefik) et 83 (Emulateur Retro)

```bash 
sudo nano /etc/apache2/ports.conf
```

Remplacez ```Listen 80``` par ```Listen 81```

Pour ajouter de nouveaux sites, rajoutez autant de ports dans 'ports.conf' que désiré, à la suite de  ```Listen 81``` .

Il faut ensuite copier/coller la configuration de base dans sites-available : 

```bash
sudo cp /etc/apache2/sites-available/mon-site /etc/apache2/sites-available/mon-site2
```

Le modifier pour indiquer le bon dossier,

```bash
ServerAdmin webmaster@localhost
	DocumentRoot /$DIRECTORY #Mettez le dossier de votre choix
	<Directory /$DIRECTORY> #Same here
		AllowOverride all
	</Directory>
```

Puis activer le site

```bash 
sudo a2ensite mon-site2
```

Faites régulièrement ```sudo systemctl restart apache2``` pour ne pas avoir de soucis


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

Ceci est l'installation pour MON raspberry, un Pi4B et RaspOS en 64 bits. Adaptez selon la doc officielle de Docker

```bash
#!/bin/bash

# Mettre à jour les paquets existants
sudo apt-get update

# Installer les dépendances nécessaires
sudo apt-get install -y ca-certificates curl

# Créer le répertoire pour les clés Docker
sudo install -m 0755 -d /etc/apt/keyrings

# Télécharger la clé GPG de Docker
sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc

# Modifier les permissions de la clé GPG
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Ajouter le dépôt Docker à la liste des sources
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Mettre à jour les paquets après l'ajout du dépôt Docker
sudo apt-get update

# Installer Docker et ses composants
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Vérifier que Docker est installé et fonctionne correctement
sudo docker --version

echo "Docker a été installé avec succès!"
```

Créer le fichier script :

Ouvrez un terminal sur votre Raspberry Pi.

Créez un nouveau fichier avec l'extension .sh, par exemple install_docker.sh :

```bash
nano install_docker.sh
```
Copiez et collez le script ci-dessus dans ce fichier.

Enregistrez et fermez le fichier (Ctrl + O pour enregistrer, Ctrl + X pour quitter).

Rendre le script exécutable :

Dans le terminal, rendez le script exécutable avec la commande suivante :

```bash
chmod +x install_docker.sh
```

Exécuter le script :

Exécutez le script avec la commande suivante :

```bash
sudo ./install_docker.sh
```

Vérifier l'installation :

Une fois le script terminé, vous pouvez vérifier que Docker est installé en exécutant :

```bash
docker --version
```

Docker compose sera également installé dans ce pack
# Ajout de l'utilisateur courant au groupe docker

```sudo usermod -aG docker $USER```

### Initialisation du Docker Compose

Il faut télécharger les 3 fichiers suivants du repo

- [Docker Compose](https://github.com/benoitchocot/pi/blob/main/docker-compose.yml)
- [Services](https://github.com/benoitchocot/pi/blob/main/services.toml)
- [Traefik](https://github.com/benoitchocot/pi/blob/main/traefik.toml)

Placez ces 3 fichiers dans le même dossier (pour ma part, /mnt/usb/pi). Déplacez vous dans le dossier où sont téléchargés ces 3 fichiers, et faites

```sudo docker compose up -d```

Cela va installer les containers de dockers. En fonction de vos services crées dans services.toml et le docker compose, vous aurez accès à une multitude de serveurs webs !

### Installation du serveur vidéo

Pour faire ceci, je vais utiliser les containers suivants : [Flaresolverr](https://github.com/benoitchocot/pi/blob/main/flare/flaresolverr), [Sonarr](https://github.com/benoitchocot/pi/blob/main/sonarr/sonarr),  [Jackett](https://github.com/benoitchocot/pi/blob/main/jackett/jackett), [Wireguard](https://github.com/benoitchocot/pi/blob/main/vpn/vpn), [Transmission](https://github.com/benoitchocot/pi/blob/main/transmission/transmission) et [Jellyfin](https://github.com/benoitchocot/pi/blob/main/jelly/jelly). 

Il faut en premier lieu configurer Jackett, et ajouter un indexer pour télécharger des torrents. J'utilise YGG, qui est dépend de Flaresolverr. En bas de page de la configuration de Jackett, il faut ajouter le lien Docker de Flaresolverr. Ensuite, on peut ajouter un indexer en haut de la page et suivre les explications pour configurer l'indexer de son choix. Ensuite, on peut configurer Transmission pour y ajouter le chemin de téléchargement (en fonction de la configuration de votre docker-compose. On peut maintenant aller sur Sonarr pour ajouter Transmission et l'indexer ajouté sur Jackett, dans l'onglet "Settings". Attention lors de l'ajout de l'ip pour le client, de bien mettre le lien Docker (je peux retrouver facilement mes liens dans Traefik personnellement). Il ne faut pas oublier de configurer Wireguard et y ajouter un VPN (ProtonVPN pour ma part), sinon on n'aura pas accès à l'ajout d'YGG dans Jackett. Il faudra en effet ajouter un fichier wg0.conf dans le dossier de config avec les bonnes données pour le faire fonctionner (se trouver en général sur les sites des VPN). Pour finir, j'utilise Jellyfin en lecteur vidéo, mais vous pouvez très bien utiliser Plex. Il faut juste configurer les dossiers dans lesquels sont vos vidéos pour pouvoir les visionner.

ATTENTION : Ygg utilise Cloudflare, d'où l'utilisation de Flaresolverr pour pouvoir se connecter. Comme Cloudflare évolue, il faut parfois changer l'image Flaresolverr pour avoir une version plus récente.


## Sécurisation du serveur

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
Pensez également à ajouter une règle à chaque fois qu'un outil a besoin d'un nouveau port.


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
