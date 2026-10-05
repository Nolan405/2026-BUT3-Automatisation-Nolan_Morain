corepack enable && yarn && yarn run lint
-> ✖ 3 problems (2 errors, 1 warning)

avec --fix
-> ✖ 1 problem (0 errors, 1 warning)

Question pour le rapport : parmi les erreurs de lint que vous avez corrigées, y en avait-il au moins une qui était un vrai bug potentiel, et pas seulement du style ? Laquelle ?
-> Oui, l'erreur côté front sur l'égalité faible == (règle eqeqeq). En JavaScript, le transtypage implicite (comme 0 == "" ou null == undefined) peut fausser des conditions logiques et créer des comportements inattendus à l'exécution, contrairement aux simples règles de formatage ou au nommage IDE1006 en C#.



La couverture du code fourni est en dessous de 60 %.
option choisie : B.
justification : J'ai modifier le code, c'est pas a moi de fair les tests


Pour la partie scanner l'image, Le scan Trivy du frontend a bloqué à cause de deux paquets Alpine pas à jour (libexpat / pcre2). J'ai réglé le problème en ajoutant un RUN apk update && apk upgrade --no-cache dans le Dockerfile pour forcer leur mise à jour.

masque les vulnérabilités pour lesquelles aucun correctif n'existe. Sans cette option, vous seriez bloqués par des problèmes que vous ne pouvez pas résoudre. Est-ce une bonne idée en production ?
-> Non, cela évite juste de bloquer le déploiement, mais les failles restent présentes. En production, il faudra surveiller ces failles et y mettre en place plusieurs protections (règles de pare-feu, restriction des accès réseau, etc).