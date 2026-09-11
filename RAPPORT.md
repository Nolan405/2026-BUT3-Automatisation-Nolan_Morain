# TP1 Automatisation

## MORAIN
## Nolan
## BUT 31A

##  Etape 1

tp-api:tp1                          f99bb24ce176       2.01GB             0B

1. Quel est le nom de l'image créée et quel est son tag ?
NOM : tp-api
TAG : tp1 

## Etape 2

1. Pourquoi proxy_pass peut-il désigner l'hôte api alors que ce nom n'existe 
nulle part sur votre machine ?

	Grâce au DNS interne de Docker, qui fait correspondre le nom du conteneur à son IP sur le 
	réseau.

2. À quoi sert la ligne try_files $uri $uri/ /index.html ? Que se passerait-il
sans elle si l'utilisateur rechargeait la page sur /tasks ?

	Elle renvoie index.html pour que le routeur JavaScript gère la page. Sans elle, recharger 
	/tasks affiche une erreur 404.

tp-front:tp1                        56d31d2bfc05       69.5MB             0B
