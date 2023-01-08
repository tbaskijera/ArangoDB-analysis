```shell

toni@toni-WRT-WX9:~$ docker exec -it arangodb-instance arangosh
Please specify a password: 

                                       _     
  __ _ _ __ __ _ _ __   __ _  ___  ___| |__  
 / _` | '__/ _` | '_ \ / _` |/ _ \/ __| '_ \ 
| (_| | | | (_| | | | | (_| | (_) \__ \ | | |
 \__,_|_|  \__,_|_| |_|\__, |\___/|___/_| |_|
                       |___/                 

arangosh (ArangoDB 3.10.2 [linux] 64bit, using jemalloc, build tags/v3.10.2-0-g8a90b61d891, VPack 0.1.36, RocksDB 7.2.0, ICU 64.2, V8 7.9.317, OpenSSL 1.1.1s  1 Nov 2022)
Copyright (c) ArangoDB GmbH

Command-line history will be persisted when the shell is exited. You can use `--console.history false` to turn this off
Connected to ArangoDB 'http+tcp://127.0.0.1:8529, version: 3.10.2 [SINGLE, server], database: '_system', username: 'root'

Type 'tutorial' for a tutorial or 'help' to see common examples

CONTAINER ID   NAME                CPU %     MEM USAGE / LIMIT     MEM %     NET I/O        BLOCK I/O        PIDS
620ba3c1fc65   arangodb-instance   636.59%   313.8MiB / 7.514GiB   4.08%     28.2kB / 7kB   69.6kB / 120MB   63




```shell

toni@toni-WRT-WX9:~$ docker exec -it arangodb-instance /bin/sh
/ # arangobench --test-case crud --requests 1500000 --complexity 10
Please specify a password: 
2023-01-03T17:49:57Z [350] INFO [69091] {bench} Running test case 'crud': will perform a mix of insert, update, get and remove operations for documents. 20% of the operations will be single-document inserts, 20% of the operations will be single-document updates, 40% of the operations are single-document read requests, and 20% of the operations will be single-document removals. There will be a total of --requests operations. The --complexity parameter can be used to control the number of attributes for the inserted and updated documents.
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

Total number of operations: 1500000, runs: 1, keep alive: yes, async: no, batch size: 0, replication factor: 1, number of shards: 1, wait for sync: false, concurrency level (threads): 8
Test case: crud, complexity: 10, database: '_system', collection: 'ArangoBenchmark'
Total request/response duration (sum of all threads): 305.469264 s
Request/response duration (per thread): 38.183658 s
Time needed per operation: 0.000026 s
Time needed per operation per thread: 0.000206 s
Operations per second rate: 38803.800151
Elapsed time since start: 38.656008 s

Min request time: 0.0565ms
Avg request time: 0.2036ms
Max request time: 17.0405ms


```

<!-->
Pokrenemo kontejner:

```shell
docker pull arangodb:3.6

docker run  -e ARANGO_ROOT_PASSWORD=password  -p 8529:8529  -v /arangodb:/var/lib/arangodb3  -v /arangodb/logs/:/var/log/arangodb3  --restart always  --name arangodb  -d arangodb:3.6  --log.level warning \

```
<!-->

172.17.0.1

docker volume create arangodb2
docker run -it --name=adb2 --rm -p 8528:8528 \
    -v arangodb2:/data \
    -v /var/run/docker.sock:/var/run/docker.sock \
    arangodb/arangodb-starter \
    --starter.address=$IP \
    --starter.join 172.17.0.1:8528
