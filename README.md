# Déploiement Apache avec Ansible et Docker

**Ce projet démontre le déploiement et la configuration automatisés d’un serveur Apache HTTP
à l’aide d’Ansible, exécuté depuis un conteneur Docker.**

**Objectif : remplacer les configurations manuelles par une approche Infrastructure as Code (IaC), reproductible et maintenable.**

## Lancer les conteneurs
```
cd ansible
docker compose up --build
```
Cette commande déploie et démarre les conteneurs définis dans le fichier **docker-compose.yml**.

Vérifiez que les 3 conteneurs sont correctement créés et démarrés : `docker ps`

## Vérifications dans le conteneur master

1. Accédez au conteneur master via Docker Desktop → Exec
2. Vérifiez la version d'Ansible : `ansible --version`
3. Vérifiez que le host web1 est bien présent dans le groupe web_servers en vérifiant l'inventaire : `ansible-inventory -i inventory-apache.yml --list`
4. Testez la connectivité avec le groupe web_servers : `ansible -i inventory-apache.yml web_servers -m ping`

## Déployer Apache

Toujours depuis le conteneur master : `ansible-playbook -i inventory-apache.yml playbook-apache.yml`

Si certaines tâches échouent, n'hésitez pas à relancer la commande.

## Vérifications dans le conteneur web

1. Accédez au conteneur web via Docker Desktop → Exec
2. Vérifiez la version d'Apache : `httpd -v`
3. Vérifiez la syntaxe de la configuration Apache : `httpd -t` (doit afficher "Syntax OK")
4. Vérifiez que le service Apache est installé : `apk info | grep apache`
5. Vérifiez la configuration générée : `cat /etc/apache2/httpd.conf`<br>
Le contenu doit correspondre à la template httpd.conf.j2, veuillez modifier le fichier en conséquence si ce n'est pas le cas.
6. Vérifiez que le contenu de la page web est le bon : `cat /var/www/localhost/htdocs/index.html`
7. Vérifiez que le service Apache est démarré : `rc-service apache2 status`
8. Vérifiez que le service Apache écoute sur le port 80 : `netstat -tulnp`

## Vérification finale

Depuis le conteneur master, effectuez un `curl 10.5.0.3:80`

Si le point ci-dessus est validé, vous pouvez désormais y accéder depuis un navigateur :

* [http://localhost:8080](http://localhost:8080)
