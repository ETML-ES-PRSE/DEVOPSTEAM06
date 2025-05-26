# Apache

## Installation

- `sudo apt update`: Rafraîchit listes des logiciels disponibles.
- `sudo apt upgrade`: Met à jour logiciels installés.
- `sudo apt install apache2`: Installe serveur web Apache requis.
- `sudo systemctl start apache2 `: Démarre service serveur web Apache.
- `sudo systemctl status apache2 `: Vérifie état service Apache actif.
- `sudo systemctl enable apache2`: Active le démarrage automatique au boot. --> Apache is a service

## Exposer le port 8000

1. Mettre à jour le port dans la configuration par défaut de Apache.

    - `sudo nano /etc/apache2/ports.conf`: Ouvrir le fichier des ports.
    - `Listen 80` ->  `Listen 8000`
    - Enregistrer les modifications

2. Modifier la configuration du VirtualHost

    - `sudo nano /etc/apache2/sites-enabled/000-default.conf ` : Modifier le fichier du VirtualHost par défaut
    - `<VirtualHost *:80>` -> `<VirtualHost *:8000>`
    - Sauvegarder le fichier

3. Reinitialiser Apache

    - `systemctl restart apache2` : Redémarre le serviche Apache.
    - `ss -tlpn| grep apache` : Affiche les connexions et ports d'écoutes Apache.

## Installation des prérequis pour WordPress

1. Installation de PHP, et des extensions nécessaires
   -
   `sudo apt install php php-mysql php-curl php-gd php-mbstring php-xml php-xmlrpc php-soap php-intl php-zip libapache2-mod-php -y`

## Utilisation d'un VHost

Créez un répertoire pour votre site :

```sudo mkdir -p /var/www/monsitewp```

Créez un nouveau fichier de configuration VHost :

```sudo nano /etc/apache2/sites-available/monsitewp.conf```

Collez et adaptez la configuration suivante (en utilisant le port 8000) :
(LOGS ACTIVES AND CLEAN)

```
<VirtualHost *:8000>
    ServerAdmin webmaster@localhost
    ServerName monsitewp.local
    DocumentRoot /var/www/monsitewp

    <Directory /var/www/monsitewp>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/monsitewp_error.log
    CustomLog ${APACHE_LOG_DIR}/monsitewp_access.log combined
</VirtualHost>
```

Activez le nouveau VHost et le module de réécriture d'URL d'Apache (utile pour WordPress), puis désactivez le site par
défaut :

```
sudo a2ensite monsitewp.conf
sudo a2dissite 000-default.conf
sudo a2enmod rewrite
```

Redémarrez Apache pour que les changements soient pris en compte :
```sudo systemctl restart apache2```

## Run with restricted permissions
