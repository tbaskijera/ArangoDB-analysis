
```
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

<https://www.arangodb.com/community-server/cluster/>
