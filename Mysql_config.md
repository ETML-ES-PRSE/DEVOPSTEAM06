# MySQL

## Installation

* `sudo apt update` : Met à jour la liste des paquets.
* `sudo apt install mysql-server` : Installe la dernière version de MySQL.
* `sudo systemctl start mysql` : Démarre le service MySQL.
* `sudo systemctl enable mysql` : Active MySQL au démarrage.
* `sudo systemctl status mysql` : Vérifie que le service fonctionne.

---

## Exposer le port 3306

> ⚠️ On autorise uniquement l’accès distant depuis le serveur WordPress.

1. Éditer la configuration MySQL

   * `sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf`
   * Modifier la ligne `bind-address = 127.0.0.1` en :
     `bind-address = 0.0.0.0`
   * Sauvegarder et quitter

2. Autoriser l'accès au pare-feu uniquement pour l'IP du serveur WordPress

   * `sudo ufw allow from <IP_WP> to any port 3306`
   * `sudo ufw reload`

3. Redémarrer MySQL

   * `sudo systemctl restart mysql`

---

## Créer l’utilisateur `wordpress` avec accès restreint

> On donne accès uniquement à la base `wordpress_db` depuis l’IP du serveur WordPress.

```bash
sudo mysql -u root -p
```

Dans MySQL :

```sql
CREATE DATABASE wordpress_db;
CREATE USER 'wordpress'@'<IP_WP>' IDENTIFIED BY 'motDePasseFort';
GRANT ALL PRIVILEGES ON wordpress_db.* TO 'wordpress'@'<IP_WP>';
FLUSH PRIVILEGES;
EXIT;
```

---

## Activer les logs

1. Éditer la configuration MySQL

   * `sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf`
   * Ajouter/modifier les lignes suivantes :

     ```ini
     general_log = 1
     general_log_file = /var/log/mysql/mysql.log
     log_error = /var/log/mysql/error.log
     slow_query_log = 1
     slow_query_log_file = /var/log/mysql/mysql-slow.log
     long_query_time = 2
     ```

2. Redémarrer le service

   * `sudo systemctl restart mysql`
