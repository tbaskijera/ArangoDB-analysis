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
