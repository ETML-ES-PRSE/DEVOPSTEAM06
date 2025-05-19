# Apache

## Installation

- `sudo apt update`: Rafraîchit listes des logiciels disponibles.
- `sudo apt upgrade`: Met à jour logiciels installés.
- `sudo apt install apache2`: Installe serveur web Apache requis.
- `sudo systemctl start apache2 `: Démarre service serveur web Apache.
- `sudo systemctl status apache2 `: Vérifie état service Apache actif.

## Exposer le port 8000

1. Mettre à jour le port dans la configuration par défaut de Apache.

    - `sudo nano /etc/apache2/ports.conf`: Ouvrir le fichier des ports.
    - `Listen 80` ->  `Listen 8000`
    - Enregistrer les modifications

2. Modifier la configuration du VirtualHost

    - `sudo nano /etc/apache2/sites-enabled/000-default.conf ` : Modifier le fichier du VirtualHost par défaut
    - `<VirtualHost *:80>` -> `<VirtualHost *:8000>`
    - Sauvegarder le fichier

3. Reinitialiser le Daemon

    - `systemctl restart apache2` : Redémarre le serviche Apache.
    - `ss -tlpn| grep apache` : Affiche les connexions et ports d'écoutes Apache.
