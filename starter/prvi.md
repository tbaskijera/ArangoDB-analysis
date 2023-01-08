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
