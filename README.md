# Analiza sustava za pohranu podataka ArangoDB

**Autori:** Toni Baskijera i Martina Sirotić

**Datum:** 16. prosinca 2022.

## Tablica sadržaja

1. [ArangoDB](#arangodb)
2. [Korišteni hardver](#korišteni-hardver)
3. [Jednoprocesni rad](#jednoprocesni-rad)
4. [Višeprocesni rad](#višeprocesni-rad)
5. [Analiza testiranja i mjerenja](#analiza-testiranja-i-mjerenja)
6. [Literatura](#literatura)

## ArangoDB

[ArangoDB](https://www.arangodb.com/) je skalabilni sustav za upravljanje bazom podataka temeljenoj na grafovima, sa širokim rasponom značajki i bogatim ekosustavom. ArangoDB omogućuje rad sa strukturiranim, polustrukturiranim i nestrukturiranim podacima u obliku JSON objekata bez specificirane sheme, bez potrebe za povezivanjem tih objekata u graf. Ovisno o potrebama, mogu se kombinirati grafovi i nepovezani podaci. ArangoDB je osmišljen od temelja kako bi podržao više podatkovnih modela s jednim jezikom upita. Također dolazi s integriranom tražilicom za pronalaženje informacija, kao što je pretraživanje cijelog teksta s rangiranjem po relevantnosti. Napisan je u C++ s ciljem visokih performansi, u oblaku ili lokalno.

## Korišteni hardver

Huawei Matebook 13 2019

- Procesor: Intel Core i5-8265u 1.6 GHz (Quad core  sa hyperthreadingom - 8 niti)
- RAM: 8GB
- Pohrana: 256 GB SSD
- Grafika: Intel HD Graphics 620

## Jednoprocesni rad

### Konfiguracija sustava

Za početak, pomoću naredbe `docker run`, pokrenuti ćemo Docker kontejner sa željenom slikom. Uz parametar `-e` navodimo varijable okoline, od kojih u našem slučaju imamo `ARANGO_NO_AUTH=1`, kojom osiguravamo da sustav isključi autentifikaciju, koja nam za svrhe testiranja nije potrebna (kod upita za lozinku unesemo prazno). Nadalje, navođenjem parametra `-d` kontejner će se izvesti u pozadini, dok parametar `--name` koristimo za dodjelu imena kontejneru. Na kraju dodamo ime slike koju želimo pokrenuti unutar kontejnera, dakle [arangodb](https://hub.docker.com/_/arangodb):

```shell

toni@toni-WRT-WX9:~$ docker run -e ARANGO_NO_AUTH=1 -d --name arangodb-instance arangodb
Unable to find image 'arangodb:latest' locally
latest: Pulling from library/arangodb
ca7dd9ec2225: Pull complete 
9c59d62bcf05: Pull complete 
5593a2d79a12: Pull complete 
b222d86fd2a5: Pull complete 
10434909b701: Pull complete 
Digest: sha256:b5522683f4b8b8f8e3909fb1effa08c3ed8dfcbf2c1b50269429a623f8fbb1b4
Status: Downloaded newer image for arangodb:latest
620ba3c1fc654a8c38e46b85238962f33dfdf237d1d9bb629e161f964ce1d5a2

```

### Testiranje i mjerenja

Za testiranje performansi sustava za pohranu podataka koristiti ćemo ugrađeni alat [`arangobench`](https://www.arangodb.com/docs/stable/programs-arangobench.html). Arangobench je klijentski alat koji uspostavlja mrežne veze s ArangoDB poslužiteljem na otprilike isti način kao što bi to radila klijentska aplikacija. Na taj način često daje dovoljno dobre procjene propusnosti i performansi. Pruža različite testne slučajeve koji se mogu izvršiti, a koji odražavaju širi skup slučajeva upotrebe. Korisno je odabrati i pokrenuti testne slučajeve koji su najsličniji tipičnim ili očekivanim radnim opterećenjima.

Najprije ćemo iskoristiti naredbu `docker exec` uz parametre `-i` i `-t` kako bismo mogli interaktivno pokretati naredbe unutar navedenog kontejnera, koji je u našem slučaju `arangodb-instance`:

```shell

toni@toni-WRT-WX9:~$ docker exec -it arangodb-instance /bin/sh

```

Tada pokrenemo naredbu `arangobench` s sljedećim parametrima i vrijednostima:

- `--test-case crud` - izvodi kombinaciju operacija umetanja, ažuriranja, čitanja i uklanjanja dokumenata. 20% operacija biti će umetanje, 20% operacija biti će ažuriranje, 40% operacija biti će zahtjevi za čitanje, a preostalih 20% operacija otpada na uklanjanje dokumenata.

- `requests 1500000` - ukupni broj operacija iznositi će 1500000

- `complexity 10` - broj atributa za stvaranje novih dokumenata i ažuriranje postojećih iznositi će 10

- `histogram.generate` - generiranje histograma testa

```shell
/ # arangobench --test-case crud --requests 1500000 --complexity 10 --histogram.generate
Please specify a password: 
2023-01-08T10:47:00Z [441] INFO [69091] {bench} Running test case 'crud': will perform a mix of insert, update, get and remove operations for documents. 20% of the operations will be single-document inserts, 20% of the operations will be single-document updates, 40% of the operations are single-document read requests, and 20% of the operations will be single-document removals. There will be a total of --requests operations. The --complexity parameter can be used to control the number of attributes for the inserted and updated documents.
2023-01-03T17:49:57Z [350] INFO [a6905] {bench} starting threads...
2023-01-03T17:49:57Z [350] INFO [a6905] {bench} executing tests...
2023-01-03T17:49:58Z [350] INFO [c3604] {bench} number of operations: 75000
2023-01-03T17:50:00Z [350] INFO [c3604] {bench} number of operations: 150000
2023-01-03T17:50:02Z [350] INFO [c3604] {bench} number of operations: 225000
2023-01-03T17:50:04Z [350] INFO [c3604] {bench} number of operations: 300000
2023-01-03T17:50:06Z [350] INFO [c3604] {bench} number of operations: 375000
2023-01-03T17:50:08Z [350] INFO [c3604] {bench} number of operations: 450000
2023-01-03T17:50:10Z [350] INFO [c3604] {bench} number of operations: 525000
2023-01-03T17:50:11Z [350] INFO [c3604] {bench} number of operations: 600000
2023-01-03T17:50:13Z [350] INFO [c3604] {bench} number of operations: 675000
2023-01-03T17:50:15Z [350] INFO [c3604] {bench} number of operations: 750000
2023-01-03T17:50:17Z [350] INFO [c3604] {bench} number of operations: 825000
2023-01-03T17:50:19Z [350] INFO [c3604] {bench} number of operations: 900000
2023-01-03T17:50:21Z [350] INFO [c3604] {bench} number of operations: 975000
2023-01-03T17:50:23Z [350] INFO [c3604] {bench} number of operations: 1050000
2023-01-03T17:50:25Z [350] INFO [c3604] {bench} number of operations: 1125000
2023-01-03T17:50:27Z [350] INFO [c3604] {bench} number of operations: 1200000
2023-01-03T17:50:29Z [350] INFO [c3604] {bench} number of operations: 1275000
2023-01-03T17:50:31Z [350] INFO [c3604] {bench} number of operations: 1350000
2023-01-03T17:50:33Z [350] INFO [c3604] {bench} number of operations: 1425000

Interval/Percentile:        50.00%        80.00%        85.00%        90.00%        95.00%        99.00%        99.99%
            0.0035ms      0.1909ms      0.2604ms      0.2847ms      0.3298ms      0.4200ms      0.7706ms      3.4679ms
            0.0048ms      0.1909ms      0.2625ms      0.2864ms      0.3293ms      0.4200ms      0.7589ms      4.7684ms
            0.0049ms      0.1902ms      0.2585ms      0.2878ms      0.3268ms      0.4195ms      0.7707ms      4.8732ms
            0.0040ms      0.1883ms      0.2604ms      0.2844ms      0.3284ms      0.4206ms      0.7690ms      4.0014ms
            0.0050ms      0.1908ms      0.2611ms      0.2862ms      0.3314ms      0.4218ms      0.7682ms      5.0161ms
            0.0057ms      0.1888ms      0.2632ms      0.2861ms      0.3262ms      0.4234ms      0.7725ms      5.7163ms
            0.0048ms      0.1891ms      0.2619ms      0.2861ms      0.3298ms      0.4219ms      0.7614ms      4.8446ms
            0.0058ms      0.1918ms      0.2616ms      0.2848ms      0.3255ms      0.4185ms      0.7673ms      5.8068ms

Total number of operations: 1500000, runs: 1, keep alive: yes, async: no, batch size: 0, replication factor: 1, number of shards: 1, wait for sync: false, concurrency level (threads): 8
Test case: crud, complexity: 10, database: '_system', collection: 'ArangoBenchmark'
Total request/response duration (sum of all threads): 335.815899 s
Request/response duration (per thread): 41.976987 s
Time needed per operation: 0.000028 s
Time needed per operation per thread: 0.000227 s
Operations per second rate: 35277.750092
Elapsed time since start: 42.519718 s

Min request time: 0.0601ms
Avg request time: 0.2239ms
Max request time: 20.0541ms

```

Možemo vidjeti da nam uz zatraženi histogram arangobench pri završetku benchmarka također ispisuje:

- Postavke koje su korištene pri sprovedbi testa
- vrstu sprovedenenog testa i njegovu kompleksnost, kao i bazu te kolekciju unutar nje na kojima se test izvršavao
- Ukupno trajanje zahtjeva/odgovora svih niti
- Ukupno trajanje zahtjeva/odgovora po pojedinačnoj niti
- Vrijeme potrebno za operaciju
- Vrijeme potrebno za operaciju po niti
- Stopu operacija po sekundi
- Proteklo vrijeme od početka do kraja testa
- Minimalno vrijeme zahtjeva
- Prosječno vrijeme zahtjeva
- Maksimalno vrijeme zahtjeva

## Praćenje naredbom `top`

Naredbom `top` interaktivno se prate Linux procesi. Omogućuje dinamičan prikaz sustava u stvarnom vremenu. Prikazuje sažetak informacija o sustavu i popis procesa i niti kojima trenutno upravlja jezgra Linuxa. Upotrijebili smo je prije, tijekom i nakon provedbe testiranja. Kada pokrenemo naredbu unutar interaktivnog sučelja komande top potrebno je unijeti `o` ili `O` te zatim upisati `COMMAND=arango` čime će se prikazati svi procesi koji unutar sebe sadrže string "arango":

### Prije testa

```shell
toni@toni-WRT-WX9:~$ top

top - 18:46:02 up 1 day,  5:28,  1 user,  load average: 0,31, 0,24, 0,31
Tasks:   296 total,   2 running,   294 sleeping,   0 stopped,   0 zombie
%Cpu(s):  2,4 us,  1,2 sy,  0,0 ni, 96,2 id,  0,2 wa,  0,0 hi,  0,0 si,  0,0 st
MiB Mem :   7694,4 total,   1945,2 free,   2477,7 used,   3271,5 buff/cache
MiB Swap:   2048,0 total,   2019,4 free,     28,6 used.   3762,6 avail Mem 

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND 
  13887 root      20   0  943344 198452  47160 S   1,3   2,5   0:17.45 arangod

```

### Tijekom testa

```shell
toni@toni-WRT-WX9:~$ top

top - 18:58:12 up 1 day,  5:32,  1 user,  load average: 1,99, 0,69, 0,45
Tasks:   296 total,   2 running,   294 sleeping,   0 stopped,   0 zombie
%Cpu(s):  56,5 us,  18,2 sy,  0,0 ni, 20,9 id,  0,1 wa,  0,0 hi,  4,3 si,  0,0 st
MiB Mem :   7694,4 total,   1767,1 free,   2545,8 used,   3381,4 buff/cache
MiB Swap:   2048,0 total,   2019,4 free,     28,6 used.   3657,8 avail Mem 

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND 
  13887 root      20   0 1122080 306980  47672 S 456,3   4,1   1:34.34 arangod
  13870 root      20   0  141148   3788   3352 S 144,5   0,0   0:34.39 arangobench


```

### Nakon testa

```shell
toni@toni-WRT-WX9:~$ top

top - 19:00:07 up 1 day,  5:42,  1 user,  load average: 0,73, 0,73, 0,59
Tasks:   296 total,   2 running,   294 sleeping,   0 stopped,   0 zombie
%Cpu(s):  1,9 us,  1,1 sy,  0,0 ni, 96,7 id,  0,2 wa,  0,0 hi,  0,0 si,  0,0 st
MiB Mem :   7694,4 total,   1467,0 free,   2872,3 used,   3355,2 buff/cache
MiB Swap:   2048,0 total,   2019,4 free,     28,6 used.   3337,7 avail Mem 

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND 
  13887 root      20   0 1235800 322384  47736 S   1,7   4,1   3:25.53 arangod

```

## Praćenje naredbom `docker stats`

Naredba `docker stats` prikazuje tok i konzumaciju podataka u stvarnom vremenu za pokrenute kontejnere. Uz ime pokrenutog kontejnera, dodati ćemo i parametar `--no-stream` kojim ćemo isključiti kontinuirani ispis statistike te umjesto toga povući samo prvi rezultat:

### Prije testa

```shell
toni@toni-WRT-WX9:~$ docker stats arangodb-instance --no-stream
CONTAINER ID   NAME                CPU %     MEM USAGE / LIMIT     MEM %     NET I/O          BLOCK I/O         PIDS
620ba3c1fc65   arangodb-instance   1.19%     164.4MiB / 7.514GiB   2.14%     23.1kB / 5.3kB   12.3kB / 3.87MB   60

```

### Tijekom teksta

```shell
toni@toni-WRT-WX9:~$ docker stats arangodb-instance --no-stream
CONTAINER ID   NAME                CPU %     MEM USAGE / LIMIT     MEM %     NET I/O        BLOCK I/O        PIDS
620ba3c1fc65   arangodb-instance   636.59%   313.8MiB / 7.514GiB   4.08%     28.2kB / 7kB   69.6kB / 120MB   63

```

### Nakon testa

```shell
toni@toni-WRT-WX9:~$ docker stats arangodb-instance --no-stream
CONTAINER ID   NAME                CPU %     MEM USAGE / LIMIT     MEM %     NET I/O        BLOCK I/O        PIDS
620ba3c1fc65   arangodb-instance   1.67%     279.2MiB / 7.514GiB   3.63%     28.4kB / 7kB   69.6kB / 186MB   47

```

## Višeprocesni rad

### Konfiguracija sustava

Sustav za pohranu podataka ArangoDB moguće je skalirati horizontalno povezivanjem nekoliko poslužitelja baze podataka u klaster. Klaster omogućuje bolje performanse i veći kapacitet, te pruža otpornost kroz replikaciju i automatsko prebacivanje u slučaju greške. Isto tako omogućuje implementaciju sustava koji se dinamički povećava i smanjuje prema zahtjevu.

ArangoDB klasteri sastoje se od određenog broja ArangoDB instanci koje međusobno komuniciraju preko mreže. Svaka instanca ima svoju ulogu u klasteru, jednu od sljedećih:

- Agenti - instance unutar agencije, središnjeg mjesta koje se koristi za pohranjivanje konfiguracije klastera, određivanje koordinatora i pružanje usluga sinkronizacije. Kako bi postigli potrebnu toleranciju na pogreške, agenti koriste algoritam Raft Consensus kako bi osigurali upravljanje konfiguracijom bez sukoba unutar klastera.

- Koordinator - kada klijenti komuniciraju s ArangoDB klasterom, komuniciraju s koordinatorima. Ove instance koordiniraju zadatke poput izvršavanja upita i pokretanja Foxx usluga. Oni znaju gdje su podaci pohranjeni i optimiziraju gdje pokrenuti upite koje klijent postavlja. Koordinatori ne sadrže nikakvo stanje, tako da ih se može jednostavno isključiti i ponovno pokrenuti prema potrebi.

- Server s bazom podataka - instance unutar kojih su zapravo pohranjeni podaci. Svaki od ovih poslužitelja pohranjuje fragmente podataka i koristi sinkronu replikaciju da bi funkcionirao kao voditelj ili sljedbenik za dati fragment. Zatim izvršavaju upite djelomično ili u cjelini kada to zatraži koordinator.

Postoji mnogo mogućih konfiguracija ArangoDB klastera, sama arhitektura je vrlo fleksibilna i može se prilagoditi po želji i potrebi. Postoji i više načina za postavljanje klastera. Mi ćemo koristiti ArangoDB starter za postavljanje klastera uz Docker.

Najprije je potrebno doznati IP adresu Docker hosta naredbom `ip` uz parametar `a`, a zatim je i exportati u varijablu okoline:

```shell
toni@toni-WRT-WX9:~$ ip a | grep docker
5: docker0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
7: veth3b5d88d@if6: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP group default
toni@toni-WRT-WX9:~$ export IP=172.17.0.1

```

Nakon toga, sve je spremno za pokretanje prve starter instance (što nije isto što i ArangoDB klaster instanca), koja će postati voditelj unutar starter konfiguracije:

Starter instanca `adb1`:

```shell
toni@toni-WRT-WX9:~$ docker run -it --name=adb1 --rm -p 8528:8528     -v arangodb1:/data     -v /var/run/docker.sock:/var/run/docker.sock     arangodb/arangodb-starter     --starter.address=$IP
2023-01-08T18:12:31Z |INFO| Starting arangodb version 0.15.5, build 7832707 component=arangodb
2023-01-08T18:12:33Z |INFO| Using storage engine 'rocksdb' component=arangodb
2023-01-08T18:12:33Z |INFO| Serving as master with ID '37190687' on 172.17.0.1:8528... component=arangodb
2023-01-08T18:12:33Z |INFO| Waiting for 3 servers to show up.
 component=arangodb
2023-01-08T18:12:33Z |INFO| ArangoDB Starter listening on 0.0.0.0:8528 (172.17.0.1:8528) component=arangodb
2023-01-08T18:12:33Z |INFO| Use the following commands to start other servers: component=arangodb

docker volume create arangodb2 && \
    docker run -it --name=adb2 --rm -p 8538:8528 -v arangodb2:/data \
    -v /var/run/docker.sock:/var/run/docker.sock arangodb/arangodb-starter:latest \
    --starter.address=172.17.0.1 --starter.join=172.17.0.1

docker volume create arangodb3 && \
    docker run -it --name=adb3 --rm -p 8548:8528 -v arangodb3:/data \
    -v /var/run/docker.sock:/var/run/docker.sock arangodb/arangodb-starter:latest \
    --starter.address=172.17.0.1 --starter.join=172.17.0.1

```

Starter nam je sam ispisao naredbe koje je potrebno pokrenuti za pokretanje sljedeće dvije instance, pa ćemo to i učiniti (naravno svaku u zasebnom terminalu):

Starter instanca `adb2`:

```shell
toni@toni-WRT-WX9:~$ docker volume create arangodb2 && \
>     docker run -it --name=adb2 --rm -p 8538:8528 -v arangodb2:/data \
>     -v /var/run/docker.sock:/var/run/docker.sock arangodb/arangodb-starter:latest \
>     --starter.address=172.17.0.1 --starter.join=172.17.0.1
arangodb2
2023-01-08T18:12:49Z |INFO| Starting arangodb version 0.15.5, build 7832707 component=arangodb
2023-01-08T18:12:51Z |INFO| Contacting master http://172.17.0.1:8528... component=arangodb
2023-01-08T18:12:51Z |INFO| Waiting for 3 servers to show up... component=arangodb
2023-01-08T18:12:51Z |INFO| ArangoDB Starter listening on 0.0.0.0:8528 (172.17.0.1:8538) component=arangodb
```

Starter instanca `adb3`:

```shell
toni@toni-WRT-WX9:~$ docker volume create arangodb3 && \
>     docker run -it --name=adb3 --rm -p 8548:8528 -v arangodb3:/data \
>     -v /var/run/docker.sock:/var/run/docker.sock arangodb/arangodb-starter:latest \
>     --starter.address=172.17.0.1 --starter.join=172.17.0.1
arangodb3
2023-01-08T18:13:02Z |INFO| Starting arangodb version 0.15.5, build 7832707 component=arangodb
2023-01-08T18:13:05Z |INFO| Contacting master http://172.17.0.1:8528... component=arangodb
2023-01-08T18:13:05Z |INFO| Waiting for 3 servers to show up... component=arangodb
2023-01-08T18:13:05Z |INFO| Serving as slave with ID 'b489c618' on 172.17.0.1:8548... component=arangodb
2023-01-08T18:13:05Z |INFO| ArangoDB Starter listening on 0.0.0.0:8528 (172.17.0.1:8548) component=arangodb
2023-01-08T18:13:05Z |INFO| Using storage engine 'rocksdb' component=arangodb
2023-01-08T18:13:05Z |INFO| agent starting routine component=arangodb type=agent
2023-01-08T18:13:05Z |INFO| agent started routine component=arangodb type=agent
2023-01-08T18:13:05Z |INFO| Looking for a running instance of agent on port 8551 component=arangodb type=agent
2023-01-08T18:13:05Z |INFO| Starting agent on port 8551 component=arangodb type=agent
2023-01-08T18:13:06Z |INFO| dbserver starting routine component=arangodb type=dbserver
2023-01-08T18:13:06Z |INFO| dbserver started routine component=arangodb type=dbserver
2023-01-08T18:13:06Z |INFO| Looking for a running instance of dbserver on port 8550 component=arangodb type=dbserver
2023-01-08T18:13:06Z |INFO| Starting dbserver on port 8550 component=arangodb type=dbserver
2023-01-08T18:13:07Z |INFO| server started cid=58fe8153 component=arangodb type=agent
2023-01-08T18:13:07Z |INFO| coordinator starting routine component=arangodb type=coordinator
2023-01-08T18:13:07Z |INFO| coordinator started routine component=arangodb type=coordinator
2023-01-08T18:13:07Z |INFO| Looking for a running instance of coordinator on port 8549 component=arangodb type=coordinator
2023-01-08T18:13:07Z |INFO| Starting coordinator on port 8549 component=arangodb type=coordinator
2023-01-08T18:13:07Z |INFO| agent up and running (version 3.10.2). cid=58fe8153 component=arangodb type=agent
2023-01-08T18:13:08Z |INFO| server started cid=3e9dc161 component=arangodb type=dbserver
2023-01-08T18:13:09Z |INFO| server started cid=188f9215 component=arangodb type=coordinator
2023-01-08T18:13:10Z |INFO| Just became master component=arangodb
2023-01-08T18:13:13Z |INFO| dbserver up and running (version 3.10.2). cid=3e9dc161 component=arangodb type=dbserver
2023-01-08T18:13:15Z |INFO| coordinator up and running (version 3.10.2). cid=188f9215 component=arangodb type=coordinator
2023-01-08T18:13:15Z |INFO| Your cluster can now be accessed with a browser at `http://172.17.0.1:8549` or cid=188f9215 component=arangodb type=coordinator
2023-01-08T18:13:15Z |INFO| using `arangosh --server.endpoint tcp://172.17.0.1:8549`. cid=188f9215 component=arangodb type=coordinator

```

Nakon pokretanja starter instance `adb3`, terminal za instancu `adb1` i `adb2` izgleda ovako:

Starter instanca `adb1`:

```shell
toni@toni-WRT-WX9:~$ docker run -it --name=adb1 --rm -p 8528:8528     -v arangodb1:/data     -v /var/run/docker.sock:/var/run/docker.sock     arangodb/arangodb-starter     --starter.address=$IP
2023-01-08T18:12:31Z |INFO| Starting arangodb version 0.15.5, build 7832707 component=arangodb
2023-01-08T18:12:33Z |INFO| Using storage engine 'rocksdb' component=arangodb
2023-01-08T18:12:33Z |INFO| Serving as master with ID '37190687' on 172.17.0.1:8528... component=arangodb
2023-01-08T18:12:33Z |INFO| Waiting for 3 servers to show up.
 component=arangodb
2023-01-08T18:12:33Z |INFO| ArangoDB Starter listening on 0.0.0.0:8528 (172.17.0.1:8528) component=arangodb
2023-01-08T18:12:33Z |INFO| Use the following commands to start other servers: component=arangodb

docker volume create arangodb2 && \
    docker run -it --name=adb2 --rm -p 8538:8528 -v arangodb2:/data \
    -v /var/run/docker.sock:/var/run/docker.sock arangodb/arangodb-starter:latest \
    --starter.address=172.17.0.1 --starter.join=172.17.0.1

docker volume create arangodb3 && \
    docker run -it --name=adb3 --rm -p 8548:8528 -v arangodb3:/data \
    -v /var/run/docker.sock:/var/run/docker.sock arangodb/arangodb-starter:latest \
    --starter.address=172.17.0.1 --starter.join=172.17.0.1

2023-01-08T18:12:51Z |INFO| Added new peer 'fe9c1e58': 172.17.0.1, portOffset: 0 component=arangodb
2023-01-08T18:13:05Z |INFO| Added new peer 'b489c618': 172.17.0.1, portOffset: 0 component=arangodb
2023-01-08T18:13:05Z |INFO| Starting service... component=arangodb
2023-01-08T18:13:05Z |INFO| agent starting routine component=arangodb type=agent
2023-01-08T18:13:05Z |INFO| agent started routine component=arangodb type=agent
2023-01-08T18:13:05Z |INFO| Looking for a running instance of agent on port 8531 component=arangodb type=agent
2023-01-08T18:13:05Z |INFO| Starting agent on port 8531 component=arangodb type=agent
2023-01-08T18:13:06Z |INFO| dbserver starting routine component=arangodb type=dbserver
2023-01-08T18:13:06Z |INFO| dbserver started routine component=arangodb type=dbserver
2023-01-08T18:13:06Z |INFO| Looking for a running instance of dbserver on port 8530 component=arangodb type=dbserver
2023-01-08T18:13:06Z |INFO| Starting dbserver on port 8530 component=arangodb type=dbserver
2023-01-08T18:13:07Z |INFO| server started cid=75e7d361 component=arangodb type=agent
2023-01-08T18:13:07Z |INFO| coordinator starting routine component=arangodb type=coordinator
2023-01-08T18:13:07Z |INFO| coordinator started routine component=arangodb type=coordinator
2023-01-08T18:13:07Z |INFO| Looking for a running instance of coordinator on port 8529 component=arangodb type=coordinator
2023-01-08T18:13:07Z |INFO| Starting coordinator on port 8529 component=arangodb type=coordinator
2023-01-08T18:13:08Z |INFO| agent up and running (version 3.10.2). cid=75e7d361 component=arangodb type=agent
2023-01-08T18:13:08Z |INFO| server started cid=a2445552 component=arangodb type=dbserver
2023-01-08T18:13:09Z |INFO| server started cid=6af4fe4b component=arangodb type=coordinator
2023-01-08T18:13:13Z |INFO| dbserver up and running (version 3.10.2). cid=a2445552 component=arangodb type=dbserver
2023-01-08T18:13:15Z |INFO| coordinator up and running (version 3.10.2). cid=6af4fe4b component=arangodb type=coordinator
2023-01-08T18:13:15Z |INFO| Your cluster can now be accessed with a browser at `http://172.17.0.1:8529` or cid=6af4fe4b component=arangodb type=coordinator
2023-01-08T18:13:15Z |INFO| using `arangosh --server.endpoint tcp://172.17.0.1:8529`. cid=6af4fe4b component=arangodb type=coordinator

```

Starter instanca `adb2`:

```shell

toni@toni-WRT-WX9:~$ docker volume create arangodb2 && \
>     docker run -it --name=adb2 --rm -p 8538:8528 -v arangodb2:/data \
>     -v /var/run/docker.sock:/var/run/docker.sock arangodb/arangodb-starter:latest \
>     --starter.address=172.17.0.1 --starter.join=172.17.0.1
arangodb2
2023-01-08T18:12:49Z |INFO| Starting arangodb version 0.15.5, build 7832707 component=arangodb
2023-01-08T18:12:51Z |INFO| Contacting master http://172.17.0.1:8528... component=arangodb
2023-01-08T18:12:51Z |INFO| Waiting for 3 servers to show up... component=arangodb
2023-01-08T18:12:51Z |INFO| ArangoDB Starter listening on 0.0.0.0:8528 (172.17.0.1:8538) component=arangodb
2023-01-08T18:13:05Z |INFO| Serving as slave with ID 'fe9c1e58' on 172.17.0.1:8538... component=arangodb
2023-01-08T18:13:05Z |INFO| Using storage engine 'rocksdb' component=arangodb
2023-01-08T18:13:05Z |INFO| agent starting routine component=arangodb type=agent
2023-01-08T18:13:05Z |INFO| agent started routine component=arangodb type=agent
2023-01-08T18:13:05Z |INFO| Looking for a running instance of agent on port 8541 component=arangodb type=agent
2023-01-08T18:13:05Z |INFO| Starting agent on port 8541 component=arangodb type=agent
2023-01-08T18:13:06Z |INFO| dbserver starting routine component=arangodb type=dbserver
2023-01-08T18:13:06Z |INFO| dbserver started routine component=arangodb type=dbserver
2023-01-08T18:13:06Z |INFO| Looking for a running instance of dbserver on port 8540 component=arangodb type=dbserver
2023-01-08T18:13:06Z |INFO| Starting dbserver on port 8540 component=arangodb type=dbserver
2023-01-08T18:13:07Z |INFO| server started cid=f6cdaf0e component=arangodb type=agent
2023-01-08T18:13:07Z |INFO| coordinator starting routine component=arangodb type=coordinator
2023-01-08T18:13:07Z |INFO| coordinator started routine component=arangodb type=coordinator
2023-01-08T18:13:07Z |INFO| Looking for a running instance of coordinator on port 8539 component=arangodb type=coordinator
2023-01-08T18:13:07Z |INFO| Starting coordinator on port 8539 component=arangodb type=coordinator
2023-01-08T18:13:07Z |INFO| agent up and running (version 3.10.2). cid=f6cdaf0e component=arangodb type=agent
2023-01-08T18:13:08Z |INFO| server started cid=6b2ec12f component=arangodb type=dbserver
2023-01-08T18:13:09Z |INFO| server started cid=94daa9ef component=arangodb type=coordinator
2023-01-08T18:13:13Z |INFO| dbserver up and running (version 3.10.2). cid=6b2ec12f component=arangodb type=dbserver
2023-01-08T18:13:14Z |INFO| coordinator up and running (version 3.10.2). cid=94daa9ef component=arangodb type=coordinator
2023-01-08T18:13:14Z |INFO| Your cluster can now be accessed with a browser at `http://172.17.0.1:8539` or cid=94daa9ef component=arangodb type=coordinator
2023-01-08T18:13:14Z |INFO| using `arangosh --server.endpoint tcp://172.17.0.1:8539`. cid=94daa9ef component=arangodb type=coordinator

```

Svaka starter instanca ispisala je i adresu preko koje možemo pristupiti ArangoDB klasteru:

> 2023-01-08T18:13:15Z |INFO| Your cluster can now be accessed with a browser at `http://172.17.0.1:8529` or cid=6af4fe4b component=arangodb type=coordinator

Moguće ju je posjetiti unutar web preglednika za pristup web sučelju ArangoDB klaster konfiguracije, a kasnije ćemo je i proslijediti *arangobenchu* kao parametar.

Da pojasnimo, pokrenuli smo tri različite ***starter*** instance u tri različita Docker kontejnera, te smo na taj način simulirali tri različita hosta. Nakon toga, svaka od starter instanca pokrenula je još tri Docker kontejnera od kojih svaki pokreće jednu klaster instancu, i to s ulogom agenta, koordinatora i baze podataka, respektivno.

Zaključujemo da bismo trenutno trebali imati pokrenuto ukupno dvanaest Docker kontejnera (tri starter instance + devet klaster instanci), a to možemo i provjeriti naredbom `docker ps`:

```shell
toni@toni-WRT-WX9:~$ docker ps
CONTAINER ID   IMAGE                              COMMAND                  CREATED          STATUS          PORTS                                       NAMES
6af4fe4b7a74   arangodb/arangodb:latest           "/usr/sbin/arangod -…"   24 minutes ago   Up 24 minutes   0.0.0.0:8529->8529/tcp                      adb1-coordinator-37190687-0-172.17.0.1-8529
94daa9ef34a2   arangodb/arangodb:latest           "/usr/sbin/arangod -…"   24 minutes ago   Up 24 minutes   8529/tcp, 0.0.0.0:8539->8539/tcp            adb2-coordinator-fe9c1e58-0-172.17.0.1-8539
188f92154aee   arangodb/arangodb:latest           "/usr/sbin/arangod -…"   24 minutes ago   Up 24 minutes   8529/tcp, 0.0.0.0:8549->8549/tcp            adb3-coordinator-b489c618-0-172.17.0.1-8549
a2445552ca27   arangodb/arangodb:latest           "/usr/sbin/arangod -…"   24 minutes ago   Up 24 minutes   8529/tcp, 0.0.0.0:8530->8530/tcp            adb1-dbserver-37190687-0-172.17.0.1-8530
6b2ec12fdf4a   arangodb/arangodb:latest           "/usr/sbin/arangod -…"   24 minutes ago   Up 24 minutes   8529/tcp, 0.0.0.0:8540->8540/tcp            adb2-dbserver-fe9c1e58-0-172.17.0.1-8540
3e9dc16111d6   arangodb/arangodb:latest           "/usr/sbin/arangod -…"   24 minutes ago   Up 24 minutes   8529/tcp, 0.0.0.0:8550->8550/tcp            adb3-dbserver-b489c618-0-172.17.0.1-8550
75e7d3616601   arangodb/arangodb:latest           "/usr/sbin/arangod -…"   24 minutes ago   Up 24 minutes   8529/tcp, 0.0.0.0:8531->8531/tcp            adb1-agent-37190687-0-172.17.0.1-8531
f6cdaf0ec7e6   arangodb/arangodb:latest           "/usr/sbin/arangod -…"   24 minutes ago   Up 24 minutes   8529/tcp, 0.0.0.0:8541->8541/tcp            adb2-agent-fe9c1e58-0-172.17.0.1-8541
58fe81536472   arangodb/arangodb:latest           "/usr/sbin/arangod -…"   24 minutes ago   Up 24 minutes   8529/tcp, 0.0.0.0:8551->8551/tcp            adb3-agent-b489c618-0-172.17.0.1-8551
c2fc39664a28   arangodb/arangodb-starter:latest   "/app/arangodb --sta…"   24 minutes ago   Up 24 minutes   0.0.0.0:8548->8528/tcp, :::8548->8528/tcp   adb3
f4b91c364728   arangodb/arangodb-starter:latest   "/app/arangodb --sta…"   24 minutes ago   Up 24 minutes   0.0.0.0:8538->8528/tcp, :::8538->8528/tcp   adb2
392b57907d3a   arangodb/arangodb-starter          "/app/arangodb --sta…"   24 minutes ago   Up 24 minutes   0.0.0.0:8528->8528/tcp, :::8528->8528/tcp   adb1
```

### Testiranje

Arangobench, kada se koristi za testiranje klastera, potrebno je pokrenuti u jednom od koordinatora, pa ćemo se mi spojiti na koordinator `adb1`:

```shell
toni@toni-WRT-WX9:~$ docker exec -it 6af4fe4b7a74 /bin/sh
```

Sada možemo ponoviti testiranje koje smo proveli i na ArangoDB jednoprocesnom sustavu, uz neke dodatne parametre s vrijednostima:

- `--server.endpoint tcp://172.17.0.1:8549` - adresa klastera nad kojim želimo vršiti testiranja

- `--number-of-shards 10` - : broj fragmenata za kolekcije koje je stvorio arangobench iznositi će 10

- `--replication-factor 3` - broj replika za svaki fragment u kolekcijama koje je izradio arangobench iznositi će 3

```shell
/ # arangobench \
> --server.endpoint tcp://172.17.0.1:8549 \
> --number-of-shards 10 \
> --replication-factor 3 \
> --test-case crud \
> --requests 1500000 \
> --complexity 10 \
> --histogram.generate
Please specify a password: 
2023-01-08T18:57:34Z [104] INFO [69091] {bench} Running test case 'crud': will perform a mix of insert, update, get and remove operations for documents. 20% of the operations will be single-document inserts, 20% of the operations will be single-document updates, 40% of the operations are single-document read requests, and 20% of the operations will be single-document removals. There will be a total of --requests operations. The --complexity parameter can be used to control the number of attributes for the inserted and updated documents.
2023-01-08T18:57:34Z [104] INFO [a6905] {bench} starting threads...
2023-01-08T18:57:36Z [104] INFO [a6905] {bench} executing tests...
2023-01-08T18:57:51Z [104] INFO [c3604] {bench} number of operations: 75000
2023-01-08T18:58:07Z [104] INFO [c3604] {bench} number of operations: 150000
2023-01-08T18:58:22Z [104] INFO [c3604] {bench} number of operations: 225000
2023-01-08T18:58:37Z [104] INFO [c3604] {bench} number of operations: 300000
2023-01-08T18:58:52Z [104] INFO [c3604] {bench} number of operations: 375000
2023-01-08T18:59:07Z [104] INFO [c3604] {bench} number of operations: 450000
2023-01-08T18:59:23Z [104] INFO [c3604] {bench} number of operations: 525000
2023-01-08T18:59:38Z [104] INFO [c3604] {bench} number of operations: 600000
2023-01-08T18:59:57Z [104] INFO [c3604] {bench} number of operations: 675000
2023-01-08T19:00:15Z [104] INFO [c3604] {bench} number of operations: 750000
2023-01-08T19:00:34Z [104] INFO [c3604] {bench} number of operations: 825000
2023-01-08T19:00:54Z [104] INFO [c3604] {bench} number of operations: 900000
2023-01-08T19:01:13Z [104] INFO [c3604] {bench} number of operations: 975000
2023-01-08T19:01:30Z [104] INFO [c3604] {bench} number of operations: 1050000
2023-01-08T19:01:44Z [104] INFO [c3604] {bench} number of operations: 1125000
2023-01-08T19:01:59Z [104] INFO [c3604] {bench} number of operations: 1200000
2023-01-08T19:02:14Z [104] INFO [c3604] {bench} number of operations: 1275000
2023-01-08T19:02:28Z [104] INFO [c3604] {bench} number of operations: 1350000
2023-01-08T19:02:43Z [104] INFO [c3604] {bench} number of operations: 1425000

Interval/Percentile:        50.00%        80.00%        85.00%        90.00%        95.00%        99.00%        99.99%
            1.3827ms      1.3827ms      2.7655ms      2.7655ms      2.7655ms      2.7655ms      5.5310ms     17.9757ms
            1.6663ms      1.6663ms      1.6663ms      1.6663ms      3.3326ms      3.3326ms      4.9988ms     16.6628ms
            1.3751ms      1.3751ms      2.7502ms      2.7502ms      2.7502ms      2.7502ms      5.5003ms     16.5009ms
            1.5802ms      1.5802ms      1.5802ms      3.1605ms      3.1605ms      3.1605ms      4.7407ms     17.3827ms
            1.4504ms      1.4504ms      2.9009ms      2.9009ms      2.9009ms      2.9009ms      5.8017ms     17.4052ms
            1.4537ms      1.4537ms      2.9073ms      2.9073ms      2.9073ms      2.9073ms      5.8147ms     18.8977ms
            1.4541ms      1.4541ms      2.9082ms      2.9082ms      2.9082ms      2.9082ms      5.8164ms     17.4493ms
            1.5502ms      1.5502ms      1.5502ms      3.1005ms      3.1005ms      3.1005ms      4.6507ms     17.0527ms

Total number of operations: 1500000, runs: 1, keep alive: yes, async: no, batch size: 0, replication factor: 3, number of shards: 10, wait for sync: false, concurrency level (threads): 8
Test case: crud, complexity: 10, database: '_system', collection: 'ArangoBenchmark'
Total request/response duration (sum of all threads): 2566.142340 s
Request/response duration (per thread): 320.767793 s
Time needed per operation: 0.000214 s
Time needed per operation per thread: 0.001715 s
Operations per second rate: 4663.466314
Elapsed time since start: 321.649155 s

Min request time: 0.2856ms
Avg request time: 1.7108ms
Max request time: 83.3139ms
```

## Praćenje naredbom `top`

U višeprocesnom radu unutar interaktivnog sučelja komande top potrebno je unijeti `o` ili `O` te zatim upisati `COMMAND=arango` čime će se prikazati svi procesi koji unutar sebe sadrže string "arango".

### Prije testa

```shell
toni@toni-WRT-WX9:~$ top

top - 20:31:32 up 5 days,  3:09,  1 user,  load average: 1,00, 0,95, 0,74
Tasks: 328 total,   1 running, 327 sleeping,   0 stopped,   0 zombie
%Cpu(s):  2,9 us,  2,2 sy,  0,0 ni, 94,7 id,  0,1 wa,  0,0 hi,  0,1 si,  0,0 st
MiB Mem :   7694,4 total,   1896,0 free,   2596,0 used,   3202,4 buff/cache
MiB Swap:   2048,0 total,   2047,2 free,      0,8 used.   3476,8 avail Mem 

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND                                                                                
  62413 root      20   0  694548 156412  30732 S   2,0   2,0   0:05.63 arangod                                                                                
  62802 root      20   0  831940 159824  35056 S   2,0   2,0   0:04.93 arangod                                                                                
  62796 root      20   0  779684 153260  30892 S   1,6   1,9   0:04.35 arangod                                                                                
  62926 root      20   0  827844 155304  32052 S   1,6   2,0   0:04.76 arangod                                                                                
  62421 root      20   0  695572 152984  31168 S   1,3   1,9   0:03.85 arangod                                                                                
  62522 root      20   0  694548 152800  29552 S   1,3   1,9   0:04.47 arangod                                                                                
  63062 root      20   0  694096  98968  43316 S   1,3   1,3   0:03.88 arangod                                                                                
  63080 root      20   0  719712 102148  43884 S   1,3   1,3   0:04.55 arangod                                                                                
  63173 root      20   0  687696  97320  42508 S   1,3   1,2   0:03.72 arangod                                                                                
  61913 root      20   0  715136  15152   9096 S   0,3   0,2   0:00.57 arangodb                                                                               
  61652 root      20   0  715392  15552   9204 S   0,0   0,2   0:00.81 arangodb                                                                               
  62154 root      20   0  714880  15432   9096 S   0,0   0,2   0:00.53 arangodb     
```

### Tijekom testa

```shell
toni@toni-WRT-WX9:~$ top

top - 20:34:14 up 5 days,  3:12,  1 user,  load average: 10,01, 4,36, 2,01
Tasks: 313 total,   1 running, 312 sleeping,   0 stopped,   0 zombie
%Cpu(s): 39,5 us, 25,5 sy,  0,0 ni, 27,6 id,  0,1 wa,  0,0 hi,  7,3 si,  0,0 st
MiB Mem :   7694,4 total,   1486,2 free,   2824,8 used,   3383,3 buff/cache
MiB Swap:   2048,0 total,   2047,2 free,      0,8 used.   3288,0 avail Mem 

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND                                                                                
  63062 root      20   0  714592 103592  44092 S 105,3   1,3   2:05.86 arangod                                                                                
  62802 root      20   0  920004 232256  35064 S  99,7   2,9   1:57.59 arangod                                                                                
  62796 root      20   0  927684 243128  31956 S  92,7   3,1   1:48.97 arangod                                                                                
  62926 root      20   0  914884 229216  32296 S  87,4   2,9   1:47.62 arangod                                                                                
  63660 root      20   0  141148   3776   3352 S  26,6   0,0   0:30.14 arangobench                                                                            
  62413 root      20   0  702740 158400  30796 S   1,0   2,0   0:07.69 arangod                                                                                
  63080 root      20   0  719712 103556  44120 S   0,7   1,3   0:05.83 arangod                                                                                
  63173 root      20   0  687696  98108  42836 S   0,7   1,2   0:04.96 arangod                                                                                
  62421 root      20   0  695572 153688  31228 S   0,3   2,0   0:05.15 arangod                                                                                
  62522 root      20   0  694548 153396  29676 S   0,3   1,9   0:05.77 arangod                                                                                
  61652 root      20   0  715392  16304   9204 S   0,0   0,2   0:00.95 arangodb                                                                               
  61913 root      20   0  715136  15064   9096 S   0,0   0,2   0:00.69 arangodb                                                                               
  62154 root      20   0  714880  15388   9096 S   0,0   0,2   0:00.62 arangodb  
```

### Nakon testa

```shell
toni@toni-WRT-WX9:~$ top

top - 20:39:18 up 5 days,  3:17,  1 user,  load average: 2,07, 5,56, 3,48
Tasks: 313 total,   1 running, 312 sleeping,   0 stopped,   0 zombie
%Cpu(s):  2,1 us,  1,7 sy,  0,0 ni, 95,9 id,  0,1 wa,  0,0 hi,  0,1 si,  0,0 st
MiB Mem :   7694,4 total,   1430,5 free,   3024,5 used,   3239,4 buff/cache
MiB Swap:   2048,0 total,   2047,2 free,      0,8 used.   3157,4 avail Mem 

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND                                                                                
  62413 root      20   0  702740 156160  28628 S   2,0   2,0   0:11.67 arangod                                                                                
  62926 root      20   0 1100228 294004  30664 S   2,0   3,7   4:42.25 arangod                                                                                
  62796 root      20   0 1097668 297628  29972 S   1,7   3,8   4:46.00 arangod                                                                                
  63080 root      20   0  719712 102168  42036 S   1,7   1,3   0:08.42 arangod                                                                                
  62421 root      20   0  695572 151640  29048 S   1,3   1,9   0:07.80 arangod                                                                                
  62522 root      20   0  694548 150916  27432 S   1,3   1,9   0:08.44 arangod                                                                                
  62802 root      20   0 1118148 297440  33328 S   1,3   3,8   5:08.17 arangod                                                                                
  63062 root      20   0  714592 101420  41868 S   1,3   1,3   5:30.70 arangod                                                                                
  63173 root      20   0  687696  96468  40724 S   1,3   1,2   0:07.46 arangod                                                                                
  61652 root      20   0  715392  15988   9212 S   0,0   0,2   0:01.33 arangodb                                                                               
  61913 root      20   0  715136  15344   9096 S   0,0   0,2   0:00.89 arangodb                                                                               
  62154 root      20   0  714880  15372   9096 S   0,0   0,2   0:00.83 arangodb    
```

## Praćenje naredbom `docker stats`

### Prije testa

```shell
toni@toni-WRT-WX9:~$ docker stats --no-stream
CONTAINER ID   NAME                                          CPU %     MEM USAGE / LIMIT     MEM %     NET I/O           BLOCK I/O         PIDS
6af4fe4b7a74   adb1-coordinator-37190687-0-172.17.0.1-8529   1.29%     66.76MiB / 7.514GiB   0.87%     4.77MB / 10.1MB   1.37MB / 12.3kB   38
94daa9ef34a2   adb2-coordinator-fe9c1e58-0-172.17.0.1-8539   1.26%     55.57MiB / 7.514GiB   0.72%     1.3MB / 2.21MB    131kB / 12.3kB    38
188f92154aee   adb3-coordinator-b489c618-0-172.17.0.1-8549   1.30%     64.97MiB / 7.514GiB   0.84%     1.44MB / 2.31MB   77.8kB / 12.3kB   37
a2445552ca27   adb1-dbserver-37190687-0-172.17.0.1-8530      1.44%     129.1MiB / 7.514GiB   1.68%     3.87MB / 2.69MB   0B / 6.94MB       51
6b2ec12fdf4a   adb2-dbserver-fe9c1e58-0-172.17.0.1-8540      1.46%     130.5MiB / 7.514GiB   1.70%     6.54MB / 7.62MB   0B / 6.95MB       51
3e9dc16111d6   adb3-dbserver-b489c618-0-172.17.0.1-8550      1.47%     125MiB / 7.514GiB     1.62%     2.15MB / 2.9MB    0B / 1.71MB       49
75e7d3616601   adb1-agent-37190687-0-172.17.0.1-8531         1.36%     127.7MiB / 7.514GiB   1.66%     9.55MB / 7.5MB    0B / 3.24MB       39
f6cdaf0ec7e6   adb2-agent-fe9c1e58-0-172.17.0.1-8541         2.02%     129.2MiB / 7.514GiB   1.68%     11.5MB / 12.7MB   0B / 3.24MB       39
58fe81536472   adb3-agent-b489c618-0-172.17.0.1-8551         1.37%     124.7MiB / 7.514GiB   1.62%     4.12MB / 2.7MB    0B / 3.25MB       39
c2fc39664a28   adb3                                          0.11%     8.328MiB / 7.514GiB   0.11%     1.11MB / 1.37MB   102kB / 0B        14
f4b91c364728   adb2                                          0.15%     7.586MiB / 7.514GiB   0.10%     687kB / 629kB     0B / 0B           14
392b57907d3a   adb1                                          0.13%     7.707MiB / 7.514GiB   0.10%     695kB / 646kB     4.1kB / 8.19kB    14
```

### Tijekom testa

```shell
toni@toni-WRT-WX9:~$ docker stats --no-stream
CONTAINER ID   NAME                                          CPU %     MEM USAGE / LIMIT     MEM %     NET I/O           BLOCK I/O         PIDS
6af4fe4b7a74   adb1-coordinator-37190687-0-172.17.0.1-8529   28.05%    66.21MiB / 7.514GiB   0.86%     379MB / 220MB     6.21MB / 12.3kB   52
94daa9ef34a2   adb2-coordinator-fe9c1e58-0-172.17.0.1-8539   0.49%     51.91MiB / 7.514GiB   0.67%     2.26MB / 3.59MB   131kB / 12.3kB    38
188f92154aee   adb3-coordinator-b489c618-0-172.17.0.1-8549   110.81%   56.24MiB / 7.514GiB   0.73%     410MB / 605MB     77.8kB / 12.3kB   38
a2445552ca27   adb1-dbserver-37190687-0-172.17.0.1-8530      95.40%    203MiB / 7.514GiB     2.64%     244MB / 222MB     655kB / 81.9MB    51
6b2ec12fdf4a   adb2-dbserver-fe9c1e58-0-172.17.0.1-8540      97.52%    203.6MiB / 7.514GiB   2.65%     248MB / 231MB     455kB / 81.9MB    52
3e9dc16111d6   adb3-dbserver-b489c618-0-172.17.0.1-8550      103.10%   196.8MiB / 7.514GiB   2.56%     258MB / 270MB     36.9kB / 72.1MB   51
75e7d3616601   adb1-agent-37190687-0-172.17.0.1-8531         0.49%     129.2MiB / 7.514GiB   1.68%     12.8MB / 9.63MB   0B / 4.93MB       39
f6cdaf0ec7e6   adb2-agent-fe9c1e58-0-172.17.0.1-8541         0.80%     132.2MiB / 7.514GiB   1.72%     20.5MB / 22.4MB   0B / 4.92MB       39
58fe81536472   adb3-agent-b489c618-0-172.17.0.1-8551         0.48%     126.1MiB / 7.514GiB   1.64%     7.35MB / 4.83MB   4.1kB / 4.93MB    39
c2fc39664a28   adb3                                          0.00%     8.586MiB / 7.514GiB   0.11%     1.98MB / 2.46MB   102kB / 0B        14
f4b91c364728   adb2                                          0.00%     8.117MiB / 7.514GiB   0.11%     1.21MB / 1.11MB   0B / 0B           14
392b57907d3a   adb1                                          0.00%     8.887MiB / 7.514GiB   0.12%     1.21MB / 1.12MB   1.26MB / 8.19kB   14
```

### Nakon testa

```shell
toni@toni-WRT-WX9:~$ docker stats --no-stream
CONTAINER ID   NAME                                          CPU %     MEM USAGE / LIMIT     MEM %     NET I/O           BLOCK I/O         PIDS
6af4fe4b7a74   adb1-coordinator-37190687-0-172.17.0.1-8529   1.34%     62.78MiB / 7.514GiB   0.82%     1.06GB / 598MB    6.21MB / 12.3kB   39
94daa9ef34a2   adb2-coordinator-fe9c1e58-0-172.17.0.1-8539   1.30%     52.21MiB / 7.514GiB   0.68%     2.37MB / 3.88MB   131kB / 12.3kB    38
188f92154aee   adb3-coordinator-b489c618-0-172.17.0.1-8549   1.30%     55.87MiB / 7.514GiB   0.73%     1.16GB / 1.71GB   77.8kB / 12.3kB   38
a2445552ca27   adb1-dbserver-37190687-0-172.17.0.1-8530      1.58%     270MiB / 7.514GiB     3.51%     678MB / 620MB     3.52MB / 226MB    51
6b2ec12fdf4a   adb2-dbserver-fe9c1e58-0-172.17.0.1-8540      1.62%     269.9MiB / 7.514GiB   3.51%     683MB / 629MB     3.56MB / 226MB    51
3e9dc16111d6   adb3-dbserver-b489c618-0-172.17.0.1-8550      1.48%     213.5MiB / 7.514GiB   2.77%     726MB / 757MB     57.3kB / 205MB    51
75e7d3616601   adb1-agent-37190687-0-172.17.0.1-8531         1.35%     129.3MiB / 7.514GiB   1.68%     13.5MB / 10.1MB   0B / 5.23MB       39
f6cdaf0ec7e6   adb2-agent-fe9c1e58-0-172.17.0.1-8541         2.01%     132.2MiB / 7.514GiB   1.72%     22.4MB / 24.4MB   0B / 5.23MB       39
58fe81536472   adb3-agent-b489c618-0-172.17.0.1-8551         1.34%     126.3MiB / 7.514GiB   1.64%     8.02MB / 5.28MB   4.1kB / 5.24MB    39
c2fc39664a28   adb3                                          0.06%     8.559MiB / 7.514GiB   0.11%     2.16MB / 2.69MB   102kB / 0B        14
f4b91c364728   adb2                                          0.00%     7.609MiB / 7.514GiB   0.10%     1.32MB / 1.21MB   0B / 0B           14
392b57907d3a   adb1                                          0.00%     8.766MiB / 7.514GiB   0.11%     1.32MB / 1.22MB   1.33MB / 8.19kB   14
```

## Analiza testiranja i mjerenja

Sva mjerenja odnose se na sustav prilikom opterećenja. Za višeprocesni način rada zbrojeni su podaci pojedinačnih procesa.

### Analiza jednoprocesnog rada

Prema naredbi `top`, prilikom opterećenja (testiranja) baze u jednoprocesnom načinu rada zauzeto je oko 6 procesnih niti, od njih ukupno 8, dakle 75% ukupne procesorske moći. Zauzeće memorije iznosi 4.1%. Isto tako vidimo da tijekom opterećenja load average u zadnjih 60 sekundi iznosi 1,99, dakle oko 2 procesa čeka na izvođenje u svakom trenutku.

Naredba `docker stats` pokazuje da je prilikom opterećenja zauzeto oko 6.4 procesnih niti od ukupnih 8. Prema tome i ostalim podacima, možemo zaključiti da pri:

- optrećenju procesora od 80%
- zauzeću memorije 3.63%
- opterećenju diska od 279.2 MiB
- optrećenju mreže od 28.4kB/7kB

sustav za pohranu podataka ArangoDB vrši 69.6 kB /120 MB čitanja / zapisivanja po sekundi.

### Analiza višeprocesnog rada

Prema naredbi `top`, prilikom opterećenja (testiranja) baze u višeprocesnom načinu rada zauzeto je oko 4.15 procesnih niti, od njih ukupno 8, dakle oko 52% ukupne procesorske moći. Zauzeće memorije iznosi 19.2%. Isto tako vidimo da tijekom opterečenja load average u zadnjih 60 sekundi iznosi 10.01, dakle oko 10 procesa čeka na izvođenje u svakom trenutku.

Naredba `docker stats` pokazuje da je prilikom opterećenja u višeprocesnom načinu rada zauzeto oko 4.38 procesnih niti od ukupnih 8. Prema tome i ostalim podacima, možemo zaključiti da pri:

- optrećenju procesora od 54.75%
- zauzeću memorije 15.49%
- opterećenju diska od 1190 MiB
- optrećenju mreže od 1586MB/1593MB

sustav za pohranu podataka ArangoDB vrši 15 MB /673 MB čitanja / zapisivanja po sekundi.

### Usporedba jednoprocesnog i višeprocesnog načina rada

Navedene podatke možemo usporediti u tabličnom obliku:

Naredba `top`:

| Metrika | Jednoprocesni rad  | Višeprocesni rad|
| ---------|:-------------------: | :----------------:|
| CPU%| 75% | 52% |
| MEM% | 4.1% | 19.2% |
| Load AVG | 1.99 | 10.01 |

Naredba `docker stats`:

| Metrika | Jednoprocesni rad  | Višeprocesni rad|
| ---------|:-------------------: | :----------------:|
| CPU% | 80% | 54.75%
| MEM%| 3.63% | 15.49% |
| MEM USAGE| 279.2 MiB | 1190 MiB |
| NET I/O | 28.4 kB / 7kB | 1586 MB / 1593 MB |
| BLOCK I/O | 69.6 kB / 120 MB  | 15 MB / 673 MB |

Iz navedenih podataka, možemo zaključiti da jednoprocesni način rada zahtijeva oko 25% više procesorske moći, a koristi oko 13.5% memorije manje u odnosu na višeprocesni prilikom opterećenja, što se odražava i u postotku opterećenosti diska. Isto je tako vidljivo kako u višeprocesnom načinu rada oko 8 procesa više čeka na izvođenje. U istom je promet kroz mrežu znatno veći zbog međusobne komunikacije procesa. Također je i broj zapisivanja i čitanja znatno veći u višeprocesnom načinu rada, kako smo uključili replikaciju i particioniranje podataka.

Nadalje, možemo usporediti i metrike arangobencha:

| Metrika | Jednoprocesni rad  | Višeprocesni rad|
| ---------|:-------------------: | :----------------:|
| Ukupno trajanje zahtjeva/odgovora svih niti | 335.82 s | 2566.14 s |
| Ukupno trajanje zahtjeva/odgovora po pojedinačnoj niti | 41.98 s | 320.76 s |
| Potrebno vrijeme po operaciji | 0.000028 s | 0.000214 s |
| Potrebno vrijeme po operaciji, po niti | 0.000227 s | 0.001715 s |
| Stopa operacija po sekundi | 35277.750092 | 4663.466314 |
| Minimalno vrijeme zahtjeva | 0.0601ms | 0.2856ms |
| Prosječno vrijeme zahtjeva | 0.2239ms | 1.7108ms |
| Maksimalno vrijeme zahtjeva | 20.0541ms | 83.3139ms |
| Trajanje testa | 42.519718 s | 321.649155 s |

Iz navedenih podataka možemo vidjeti kako je pri jednoprocesni način rada brži u svakoj metrici koju arangobench ispisuje, ali to je i očekivano s obzirom da smo u višeprocesnom načinu rada uključili replikaciju i particioniranje podataka.

## Literatura

- [1] <https://www.arangodb.com/docs/stable/>
- [2] <https://docs.docker.com/engine/reference/commandline/stats/>
- [3] <https://github.com/arangodb-helper/arangodb>
