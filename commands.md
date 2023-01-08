ip a
docker 0
172.17.0.1

arangobench \
--server.endpoint tcp://172.17.0.1:8549 \
--number-of-shards 10 \
--replication-factor 3 \
--test-case crud \
--requests 1500000 \
--complexity 10 \
--histogram.generate

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
