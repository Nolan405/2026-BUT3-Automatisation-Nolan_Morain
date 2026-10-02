corepack enable && yarn && yarn run lint
-> ✖ 3 problems (2 errors, 1 warning)

avec --fix
-> ✖ 1 problem (0 errors, 1 warning)

Question pour le rapport : parmi les erreurs de lint que vous avez corrigées, y en avait-il au moins une qui était un vrai bug potentiel, et pas seulement du style ? Laquelle ?
-> Oui, l'erreur côté front sur l'égalité faible == (règle eqeqeq). En JavaScript, le transtypage implicite (comme 0 == "" ou null == undefined) peut fausser des conditions logiques et créer des comportements inattendus à l'exécution, contrairement aux simples règles de formatage ou au nommage IDE1006 en C#.



