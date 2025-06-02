# MySQL

## Installation

* `sudo apt update` : Met à jour la liste des paquets.
* `sudo apt install mysql-server` : Installe la dernière version de MySQL.
* `sudo systemctl start mysql` : Démarre le service MySQL.
* `sudo systemctl enable mysql` : Active MySQL au démarrage.
* `sudo systemctl status mysql` : Vérifie que le service fonctionne.

---

## Exposer le port 3306 (accès distant restreint)

> ⚠️ On autorise uniquement l’accès distant depuis le serveur WordPress.

1. Éditer la configuration MySQL

```bash
sudo nano /etc/mysql/mariadb.conf.d/50-server.cnf
````

Modifier la ligne :

```ini
bind-address = 0.0.0.0
```

2. Redémarrer MySQL :
```bash
sudo systemctl restart mysql
```

3. Sécuriser le port avec UFW :
```bash
sudo ufw allow from <IP_WP> to any port 3306
sudo ufw deny 3306
sudo ufw allow ssh
sudo ufw enable
```

---

## Créer l’utilisateur `wordpress` avec accès restreint

> On donne accès uniquement à la base `wordpress_db` depuis l’IP du serveur WordPress (`192.168.93.135` dans notre cas).

```bash
sudo mysql -u root -p
```

Dans MySQL :

```sql
CREATE DATABASE IF NOT EXISTS wordpress_db;
DROP USER IF EXISTS 'wordpress'@'192.168.93.135';
CREATE USER 'wordpress'@'192.168.93.135' IDENTIFIED BY 'motDePasseFort';
GRANT ALL PRIVILEGES ON wordpress_db.* TO 'wordpress'@'192.168.93.135';
FLUSH PRIVILEGES;
EXIT;
```

---

## Activer les logs

1. Éditer la configuration :

```bash
sudo nano /etc/mysql/mariadb.conf.d/50-server.cnf
```

Ajouter/modifier dans la section `[mysqld]` :

```ini
general_log = 1
general_log_file = /var/log/mysql/mysql.log

log_error = /var/log/mysql/error.log

slow_query_log = 1
slow_query_log_file = /var/log/mysql/mysql-slow.log
long_query_time = 2
```

2. Créer les fichiers de log s’ils n’existent pas :

```bash
sudo mkdir -p /var/log/mysql
sudo touch /var/log/mysql/mysql.log /var/log/mysql/error.log /var/log/mysql/mysql-slow.log
sudo chown mysql:mysql /var/log/mysql/*
```

3. Redémarrer MySQL :

```bash
sudo systemctl restart mysql
```
---