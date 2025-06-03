# Nginx Reverse Proxy

## Installation

* `sudo apt update` : Met à jour la liste des paquets.
* `sudo apt install nginx` : Installe Nginx.
* `sudo systemctl start nginx` : Démarre le service Nginx.

* `sudo systemctl enable nginx` : Active Nginx au démarrage.
* `sudo systemctl status nginx` : Vérifie que le service fonctionne.

---

## Exposer le port 8080 (accès au reverse proxy)

> ⚠️ On autorise uniquement l’accès distant sur le port 8080 si nécessaire.

1. Modifier le pare-feu avec UFW :

```bash
sudo ufw allow 8080
sudo ufw allow ssh
sudo ufw enable
````

---

## Configurer un virtual host Nginx

> On redirige le trafic reçu sur `http://<ip>:8080` vers l’instance WordPress située en local sur le port `8000`.

1. Créer le fichier de configuration :

```bash
sudo nano /etc/nginx/sites-available/wordpress_proxy
```

2. Ajouter le contenu suivant :

```nginx
server {
    listen 8080;
    server_name _;

    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    access_log /var/log/nginx/wordpress_proxy.access.log;
    error_log  /var/log/nginx/wordpress_proxy.error.log;
}
```

3. Activer le site :

```bash
sudo ln -s /etc/nginx/sites-available/wordpress_proxy /etc/nginx/sites-enabled/
```

4. Vérifier la configuration :

```bash
sudo nginx -t
```

5. Redémarrer Nginx :

```bash
sudo systemctl restart nginx
```

---

## Vérifications et bonnes pratiques

* Nginx écoute bien sur le port `8080` (commande : `ss -tulpn | grep 8080`)
* Le service `nginx` est bien actif et redémarre automatiquement au démarrage
* Les logs sont stockés proprement dans :

  * `/var/log/nginx/wordpress_proxy.access.log`
  * `/var/log/nginx/wordpress_proxy.error.log`
* Le reverse proxy redirige bien vers `http://127.0.0.1:8000`

---

## Documentation utile

* [Nginx as Reverse Proxy](https://docs.nginx.com/nginx/admin-guide/web-server/reverse-proxy/)
* [Where are Nginx logs](https://www.nginx.com/resources/wiki/start/topics/examples/full/)
* [Nginx and vhost](https://docs.nginx.com/nginx/admin-guide/web-server/server-config/)
* [Change listening port on Nginx](https://nginx.org/en/docs/http/ngx_http_core_module.html#listen)

```

---

Souhaites-tu que je te le génère aussi en `.md` prêt à être téléchargé ou copié dans ton repo ?
```
