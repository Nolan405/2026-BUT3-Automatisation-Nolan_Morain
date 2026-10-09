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


Lors du déploiement, comment est transmise l'information de quelle image doit être lancée ? Notez votre réponse dans le rapport.