```shell
toni@toni-WRT-WX9:~$ docker stats arangodb-instance --no-stream
CONTAINER ID   NAME                CPU %     MEM USAGE / LIMIT     MEM %     NET I/O        BLOCK I/O        PIDS
620ba3c1fc65   arangodb-instance   636.59%   313.8MiB / 7.514GiB   4.08%     28.2kB / 7kB   69.6kB / 120MB   63

```

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
