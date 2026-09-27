# Mesurer avant de toucher à quoi que ce soit

### `docker images | grep -E 'tp-api|tp-front'`

```text
tp-api:mesure                  3d83c1e128b1       2.01GB             0B        
tp-front:mesure                8dc43584726b       63.4MB             0B 
```

## Backend

### `time docker build --no-cache -t tp-api:mesure .`

```text
real    0m22,870s
user    0m0,081s
sys     0m0,078s
```

### `time docker build -t tp-api:mesure .`

```text
real    0m27,929s
user    0m0,111s
sys     0m0,058s
```

### `docker run --entrypoint id --rm tp-api:mesure`

```text
uid=0(root) gid=0(root) groups=0(root)
```


## Frontend

### `time docker build --no-cache -t tp-front:mesure .`

```text
real    0m30,065s
user    0m0,109s
sys     0m0,069s
```

### `time docker build -t tp-front:mesure .`

```text
real    0m34,272s
user    0m0,106s
sys     0m0,097s
```

### `docker run --entrypoint id --rm tp-front:mesure`

```text
uid=0(root) gid=0(root) groups=0(root),0(root),1(bin),2(daemon),3(sys),4(adm),6(disk),10(wheel),11(floppy),20(dialout),26(tape),27(video)
```


# Refondre le Dockerfile de l'API

## Backend

### `time docker build --no-cache -t tp-api:mesure .`

```text
real    0m28,941s
user    0m0,113s
sys     0m0,079s
```

### `time docker build -t tp-api:mesure .`

```text
real    0m9,479s
user    0m0,074s
sys     0m0,058s
```

### `docker images | grep tp-api`

```text
tp-api:mesure                  a4ba444d8f48        349MB             0B        
tp-api:tp2                     12ddc1331c5c        349MB             0B
```

### `docker run --entrypoint id --rm tp-api:tp2`

```text
uid=1654(app) gid=1654(app) groups=1654(app)
```

### `time docker build -t tp-api:tp2 .`

```text
real    0m6,990s
user    0m0,071s
sys     0m0,053s
```

## Frontend

### `time docker build --no-cache -t tp-front:tp2 .`

```text
real    0m37,322s
user    0m0,125s
sys     0m0,087s
```

### `time docker build -t tp-front:tp2 .`

```text
real    0m8,129s
user    0m0,075s
sys     0m0,060s
```

### `docker images | grep tp-front`

```text
tp-front:mesure                dd5a0ad40e9d       63.4MB             0B        
tp-front:tp2                   57119e2cb928       63.4MB             0B 
```

### `docker run --entrypoint id --rm tp-front:tp2`

```text
uid=0(root) gid=0(root) groups=0(root),0(root),1(bin),2(daemon),3(sys),4(adm),6(disk),10(wheel),11(floppy),20(dialout),26(tape),27(video)
```

### `time docker build -t tp-front:tp2 .`

```text
real    0m8,402s
user    0m0,088s
sys     0m0,035s
```

# Casser volontairement, puis réparer

1. Quel job échoue ? L'autre s'exécute-t-il quand même ? Le job front a-t-il été affecté ?

```text
C'est le job api qui échoue. Le job front c'est exécuté qaund même et est passé au vert, et n'a pas été affecté car les deux jobs sont indépendants.
```
![alt text](image.png)

![alt text](image-2.png)

2. Après rétablissement du test : 

![alt text](image-1.png)

3. Combien de temps s'est écoulé entre votre push et le moment où vous avez su que c'était cassé ? C'est votre première mesure de boucle de rétroaction.

```text
Entre le push et le moment ou l'on sait que c'est cassé, il s'est passé 44s. Le job api a duré 42s avant d'échouer, pendant que le job front 36s en parralele.
```

# Rendu

1. Quelle modification a produit le plus gros gain de taille ? Et de durée ? Ce ne sont pas les mêmes — expliquez pourquoi.

```text
Séparer la construction en deux étapes (Fabrication / Exécution finale) a fait gagner de la place. Ranger l'ordre des étapes correctement a fait gagner du temps car pas besoins de retélécharger les modules à chaque modification. Le poids dépend de ce qu'on garde à la fin, alors que la vitesse dépend de ce qu'on évite de refaire.
```

2. Le délai entre le push et la détection de l'erreur (§6), et ce que vous feriez pour le réduire.

```text
Le délais est de 44s. Pour aller plus vite, on pourrait faire tourner les tests sur notre machine avant d'envoyer le code, ou ne lancer la vérification que sur la partie du projet qu'on a réellement modifiée.
```

3. Un échec de workflow que vous avez rencontré : le message, votre hypothèse, ce qui était réellement en cause.

```text
Le test a échoué avec l'erreur "Assert.NotEmpty() Failure: Collection was empty". On pouvait croire que l'API renvoyait n'importe quoi, mais c'était juste le test qu'on avait volontairement modifié pour attendre des données alors que la base était vide au départ.
```