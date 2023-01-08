# Analiza sustava za pohranu podataka ArangoDB

**Autori:** Toni Baskijera i Martina Sirotić

**Datum:** 16. prosinca 2022.

## ArangoDB

Općenito o [arangodb](https://www.arangodb.com/) opisat.

## Korišteni hardver

Huawei Matebook 13 2019

- Procesor: Intel Core i5-8265u 1.6 GHz
- RAM: 8GB
- Pohrana: 256 GB SSD
- Grafika: Intel HD Graphics 620

## Jednoprocesni rad

Za početak, pomoću naredbe `docker run`, pokrenuti ćemo Docker kontejner sa željenom slikom. Uz parametar `-e` navodimo varijable okoline, od kojih u našem slučaju imamo `ARANGO_NO_AUTH=1`, kojom osiguravamo da sustav isključi autentifikaciju, koja nam za svrhe testiranja nije potrebna. Nadalje, navođenjem parametra `-d` kontejner će se izvesti u pozadini, dok parametar `--name` koristimo za dodjelu imena kontejneru. Na kraju dodamo ime slike koju želimo pokrenuti unutar kontejnera, dakle [arangodb](https://hub.docker.com/_/arangodb).

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

Za testiranje performansi sustava za pohranu podataka koristiti ćemo ugrađeni alat [`arangobench`](https://www.arangodb.com/docs/stable/programs-arangobench.html). Arangobench je klijentski alat koji uspostavlja mrežne veze s ArangoDB poslužiteljem na otprilike isti način kao što bi to radila klijentska aplikacija. Na taj način često daje dovoljno dobre procjene propusnosti i performansi. Pruža različite testne slučajeve koji se mogu izvršiti, a koji odražavaju širi skup slučajeva upotrebe. Korisno je odabrati i pokrenuti testne slučajeve koji su najsličniji tipičnim ili očekivanim radnim opterećenjima.

Koristiti ćemo naredbu `docker exec` uz parametre `-i` i `-t` kako bismo mogli pokretati naredbe unutar navedenog kontejnera, u našem slučaju `arangodb-instance`:

```shell

toni@toni-WRT-WX9:~$ docker exec -it arangodb-instance /bin/sh

```

Tada pokrećemo `arangobench` s sljedećim parametrima i vrijednostima:

- `--test-case crud` - izvodi kombinaciju operacija umetanja, ažuriranja, čitanja i uklanjanja dokumenata. 20% operacija biti će umetanje, 20% operacija biti će ažuriranje, 40% operacija biti će zahtjevi za čitanje, a preostalih 20% operacija otpada na uklanjanje dokumenta

- `requests 1500000` - ukupni broj informacija iznositi će 1500000

- `complexity 10` - broj atributa za stvaranje novih dokumenata i ažuriranje postojećih iznositi će 10

- `histogram.generate` - generiranje histograma

```shell
/ # arangobench --test-case crud --requests 1500000 --complexity 10 --histogra
m.generate
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

Opisati malo rezultate, ovi sta pisu

## Praćenje naredbom `top`

Opisat malo naredbu

### Prije

```shell
toni@toni-WRT-WX9:~$ top -p 13887

top - 18:46:02 up 1 day,  5:28,  1 user,  load average: 0,31, 0,24, 0,31
Tasks:   1 total,   0 running,   1 sleeping,   0 stopped,   0 zombie
%Cpu(s):  2,4 us,  1,2 sy,  0,0 ni, 96,2 id,  0,2 wa,  0,0 hi,  0,0 si,  0,0 st
MiB Mem :   7694,4 total,   1945,2 free,   2477,7 used,   3271,5 buff/cache
MiB Swap:   2048,0 total,   2019,4 free,     28,6 used.   3762,6 avail Mem 

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND 
  13887 root      20   0  943344 198452  47160 S   1,3   2,5   0:17.45 arangod

```

### Tijekom

```shell
toni@toni-WRT-WX9:~$ top -p 13887

top - 18:58:12 up 1 day,  5:32,  1 user,  load average: 1,99, 0,69, 0,45
Tasks:   1 total,   0 running,   1 sleeping,   0 stopped,   0 zombie
%Cpu(s):  56,5 us,  18,2 sy,  0,0 ni, 20,9 id,  0,1 wa,  0,0 hi,  4,3 si,  0,0 st
MiB Mem :   7694,4 total,   1767,1 free,   2545,8 used,   3381,4 buff/cache
MiB Swap:   2048,0 total,   2019,4 free,     28,6 used.   3657,8 avail Mem 

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND 
  13887 root      20   0 1122080 306980  47672 S 456,3   4,1   1:34.34 arangod 

```

### Nakon

```shell
toni@toni-WRT-WX9:~$ top -p 13887

top - 19:00:07 up 1 day,  5:42,  1 user,  load average: 0,73, 0,73, 0,59
Tasks:   1 total,   0 running,   1 sleeping,   0 stopped,   0 zombie
%Cpu(s):  1,9 us,  1,1 sy,  0,0 ni, 96,7 id,  0,2 wa,  0,0 hi,  0,0 si,  0,0 st
MiB Mem :   7694,4 total,   1467,0 free,   2872,3 used,   3355,2 buff/cache
MiB Swap:   2048,0 total,   2019,4 free,     28,6 used.   3337,7 avail Mem 

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND 
  13887 root      20   0 1235800 322384  47736 S   1,7   4,1   3:25.53 arangod 

```

## Praćenje naredbom `docker stats`

Opisat malo naredbu

### Prije

```shell

CONTAINER ID   NAME                CPU %     MEM USAGE / LIMIT     MEM %     NET I/O          BLOCK I/O         PIDS
620ba3c1fc65   arangodb-instance   1.19%     164.4MiB / 7.514GiB   2.14%     23.1kB / 5.3kB   12.3kB / 3.87MB   60

```

### Tijekom

```shell

CONTAINER ID   NAME                CPU %     MEM USAGE / LIMIT     MEM %     NET I/O        BLOCK I/O        PIDS
620ba3c1fc65   arangodb-instance   636.59%   313.8MiB / 7.514GiB   4.08%     28.2kB / 7kB   69.6kB / 120MB   63

```

### Poslije

```shell
CONTAINER ID   NAME                CPU %     MEM USAGE / LIMIT     MEM %     NET I/O        BLOCK I/O        PIDS
620ba3c1fc65   arangodb-instance   1.67%     279.2MiB / 7.514GiB   3.63%     28.4kB / 7kB   69.6kB / 186MB   47

```

## Višeprocesni rad

Opisati ArangoDB cluster arhitekturu.

Opisati ArangoDB Master/follower vezu u clusteru i Leader/follower vezu za replikaciju.

Opisati ArangoDB cluster starter.

Navesti više načina za postaviti cluster (manualno, starter, ...)

Opisati konfiguraciju koju ćemo koristiti

## Analiza podataka

## Literatura
