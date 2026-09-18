### docker images | grep -E 'tp-api|tp-front'

root@devbox-etu25:~/tp-automatisation# docker images | grep -E 'tp-api|tp-front'
tp-api:mesure                  3d83c1e128b1       2.01GB             0B        
tp-front:mesure                8dc43584726b       63.4MB             0B 


## Backend

### time docker build --no-cache -t tp-api:mesure .

real    0m22,870s
user    0m0,081s
sys     0m0,078s

### time docker build -t tp-api:mesure .

real    0m27,929s
user    0m0,111s
sys     0m0,058s

### Sous quel utilisateur tourne l'API ?

uid=0(root) gid=0(root) groups=0(root)


## Frontend

### time docker build --no-cache -t tp-front:mesure .

real    0m30,065s
user    0m0,109s
sys     0m0,069s

### time docker build -t tp-front:mesure .

real    0m34,272s
user    0m0,106s
sys     0m0,097s

### Sous quel utilisateur tourne l'API ?

uid=0(root) gid=0(root) groups=0(root),0(root),1(bin),2(daemon),3(sys),4(adm),6(disk),10(wheel),11(floppy),20(dialout),26(tape),27(video)