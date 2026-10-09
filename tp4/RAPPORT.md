etape 1 : 

etu25/api : sha256:ddc65
etu25/web : sha256:fb84d




etape 2 : 

ssh-keyscan récupère l'empreinte de l'hôte au moment de la connexion, donc accepte n'importe quel serveur qui répond. Quelle attaque cela rend-il possible ? Comment pourrait-on sécuriser ?




etape 3 :

Notez dans le rapport pourquoi ce fichier ne peut pas être dans le dépôt Git, ni dans l'image.




Notez les problèmes rencontrés dans le rapport et corrigez.

Run scp -P *** deploy/compose.test.yml \
scp: stat local "deploy/compose.test.yml": No such file or directory

faut mettre ca : - uses: actions/checkout@v4


Run scp -P *** deploy/compose.test.yml \
Host key verification failed.
scp: Connection closed

faut inversé les deux blocks.


Run ssh -p *** ***@*** \
 Image docker.iut.arcanit.io/etu25/api:d6967840b3d9496904cef57a1c2f3d5e6c4a6780 Pulling 
 Image mariadb:12 Pulling 
 Image docker.iut.arcanit.io/etu25/web:d6967840b3d9496904cef57a1c2f3d5e6c4a6780 Pulling 
 Image docker.iut.arcanit.io/etu25/api:d6967840b3d9496904cef57a1c2f3d5e6c4a6780 Error Head "https://docker.iut.arcanit.io/v2/etu25/api/manifests/d6967840b3d9496904cef57a1c2f3d5e6c4a6780": no basic auth credentials
 Image docker.iut.arcanit.io/etu25/web:d6967840b3d9496904cef57a1c2f3d5e6c4a6780 Interrupted 
 Image mariadb:12 Interrupted 
Error response from daemon: Head "https://docker.iut.arcanit.io/v2/etu25/api/manifests/d6967840b3d9496904cef57a1c2f3d5e6c4a6780": no basic auth credentials
Error response from daemon: No such image: docker.iut.arcanit.io/etu25/web:d6967840b3d9496904cef57a1c2f3d5e6c4a6780
Error: Process completed with exit code 1

la VM ne peut pas télécharger les images car elle n'est pas connectée au registre Docker.
Cause : Échec du docker compose up sur la VM par manque d'authentification au registre privé docker.iut.arcanit.io (no basic auth credentials).
Solution : Exécution d'un docker login avec DOCKER_USER et DOCKER_PASSWORD en SSH sur la VM juste avant le déploiement.






Lors du déploiement, comment est transmise l'information de quelle image doit être lancée ? Notez votre réponse dans le rapport.







Sur github le commit que j'ai fusionné : 06d6b8449589cf2c78033a80c9d21034b036dcb6 

root@devbox-etu25:~# docker compose -f ~/apps/test/compose.yml ps
WARN[0000] The "IMAGE_TAG" variable is not set. Defaulting to a blank string. 
WARN[0000] The "IMAGE_TAG" variable is not set. Defaulting to a blank string. 
NAME           IMAGE                                                                      COMMAND                  SERVICE   CREATED          STATUS                            PORTS
test-api-1     docker.iut.arcanit.io/etu25/api:06d6b8449589cf2c78033a80c9d21034b036dcb6   "dotnet TaskList.Api…"   api       2 minutes ago    Up 2 minutes (health: starting)   8080/tcp
test-db-1      mariadb:12                                                                 "docker-entrypoint.s…"   db        10 minutes ago   Up 10 minutes (healthy)           3306/tcp
test-front-1   docker.iut.arcanit.io/etu25/web:06d6b8449589cf2c78033a80c9d21034b036dcb6   "/docker-entrypoint.…"   front     2 minutes ago    Up 2 minutes                      0.0.0.0:52599->80/tcp, [::]:52599->80/tcp
root@devbox-etu25:~# docker inspect --format '{{.Config.Image}}' $(docker compose -f ~/apps/test/compose.yml ps -q api)
WARN[0000] The "IMAGE_TAG" variable is not set. Defaulting to a blank string. 
WARN[0000] The "IMAGE_TAG" variable is not set. Defaulting to a blank string. 
docker.iut.arcanit.io/etu25/api:06d6b8449589cf2c78033a80c9d21034b036dcb6






etape 4


La variable IMAGE_TAG doit être passée devant la commande Docker en manuel car elle est normalement injectée par GitHub Actions avec le SHA du commit lors des déploiements automatiques.



root@devbox-etu25:~/apps/test# docker images --digests | grep api
docker.iut.arcanit.io/etu25/api   06d6b8449589cf2c78033a80c9d21034b036dcb6   sha256:acf00dd77e8bf2924142703280fc9898145da0b54d157d79f1204728570dad70   4d9b46b9c0c3   11 minutes ago   349MB
docker.iut.arcanit.io/etu25/api   93d6ddf9bee582aa0ffe614ad696b754d74dbcdb   sha256:c3baa6101806fd0ede960d10b5ad06612fbca60753fc9472b466c32db07f237b   a9eb6a4bb9fe   19 minutes ago   349MB
tp-automatisation-api             latest                                     <none>                                                                    26351b257ba1   7 days ago       349MB
root@devbox-etu25:~/apps/test# IMAGE_TAG=06d6b8449589cf2c78033a80c9d21034b036dcb6 docker compose -f ~/apps/test/compose.yml up -d
[+] up 3/3
 ✔ Container test-front-1 Running                                                                                                                                                            0.0s
 ✔ Container test-db-1    Healthy                                                                                                                                                            6.7s
 ✔ Container test-api-1   Started                                                                                                                                                            6.4s
root@devbox-etu25:~/apps/test# docker images --digests | grep api
docker.iut.arcanit.io/etu25/api   06d6b8449589cf2c78033a80c9d21034b036dcb6   sha256:acf00dd77e8bf2924142703280fc9898145da0b54d157d79f1204728570dad70   4d9b46b9c0c3   13 minutes ago   349MB
docker.iut.arcanit.io/etu25/api   93d6ddf9bee582aa0ffe614ad696b754d74dbcdb   sha256:c3baa6101806fd0ede960d10b5ad06612fbca60753fc9472b466c32db07f237b   a9eb6a4bb9fe   21 minutes ago   349MB
tp-automatisation-api             latest                                     <none>                                                                    26351b257ba1   7 days ago       349MB







root@devbox-etu25:~/apps/test# docker images --digests | grep web
docker.iut.arcanit.io/etu25/web   99ec47f9a9e6b51b76b22c94854247a3185f47c6   sha256:d2440c4a750fcb4cbd603c672d883e0a2f605cffe915e5170f23f08e0fdee85f   316ec2697d2d   About a minute ago   75.1MB
docker.iut.arcanit.io/etu25/web   06d6b8449589cf2c78033a80c9d21034b036dcb6   sha256:324cb627cd3219d020b87bd13a0f0b17126acff406dcacb70f7d7719ac30882d   357f74e89ef2   26 minutes ago       75.1MB
docker.iut.arcanit.io/etu25/web   93d6ddf9bee582aa0ffe614ad696b754d74dbcdb   sha256:124df6f0199c2253fb3b9d62540626bbf5ef108e5bd4e3eeebaf6dff5c5a4731   c97853904ef9   35 minutes ago       75.1MB
root@devbox-etu25:~/apps/test# ls -l ~/apps/test/.env
-rw------- 1 root root 32 oct.   9 16:32 /home/etu25/work/apps/test/.env







### 1. Informations de l'environnement de test
* **URL de l'application :** `https://nolan-morain-test.iut.arcanit.io`
* **SHA des images actuellement déployées :** `99ec47f9a9e6b51b76b22c94854247a3185f47c6` # a modfiier apres commit final

