

* Exercise 1

Install Kong Community Edition with Postgres as per docs; <https://docs.konghq.com/install/docker/>

```docker network create kong-net```

```docker run -d --name kong-database --network=kong-net -p 5432:5432 -e "POSTGRES_USER=kong" -e "POSTGRES_DB=kong" postgres:9.6```


```docker run --rm --network=kong-net -e "KONG_DATABASE=postgres" -e "KONG_PG_HOST=kong-database" -e "KONG_CASSANDRA_CONTACT_POINTS=kong-database" kong:latest kong migrations up```

```docker run -d --name kong --network=kong-net -e "KONG_DATABASE=postgres" -e "KONG_PG_HOST=kong-database" -e "KONG_CASSANDRA_CONTACT_POINTS=kong-database" -e "KONG_PROXY_ACCESS_LOG=/dev/stdout" -e "KONG_ADMIN_ACCESS_LOG=/dev/stdout" -e "KONG_PROXY_ERROR_LOG=/dev/stderr" -e "KONG_ADMIN_ERROR_LOG=/dev/stderr" -e "KONG_ADMIN_LISTEN=0.0.0.0:8001, 0.0.0.0:8444 ssl" -p 8000:8000 -p 8443:8443 -p 8001:8001 -p 8444:8444 kong:latest```

Import `Kong.postman_collection.json` into Postman

* Exercise 2

1.	Using Docker-Compose, run a two node Cassandra cluster locally.

 ```
 docker-compose up -d
 ```
 
2.	By logging-in into the shell of one of those Docker container, prove the two nodes are properly clustered together by using the nodetool CLI tool.

```
$ docker exec cassandra0 nodetool status
Datacenter: datacenter1
=======================
Status=Up/Down
|/ State=Normal/Leaving/Joining/Moving
--  Address     Load       Tokens       Owns (effective)  Host ID                               Rack
UN  172.19.0.3  309.25 KiB  256          100.0%            a4c388ca-ea5d-4585-8677-fae330467d99  rack1
UN  172.19.0.2  240.04 KiB  256          100.0%            8c57aec9-708c-4f33-9c3c-dea62250d434  rack1
```
or
```
$ docker exec -it cassandra0 nodetool status
Datacenter: datacenter1
=======================
Status=Up/Down
|/ State=Normal/Leaving/Joining/Moving
--  Address     Load       Tokens       Owns (effective)  Host ID                               Rack
UN  172.18.0.2  108.59 KiB  256          100.0%            27b2b6f9-80f9-4236-a06c-15fa6ea9e575  rack1
UN  172.18.0.3  88.88 KiB  256          100.0%            efea05ac-f8e6-4909-9eff-979ac1720d36  rack1
```
or
```
docker exec -it cassandra0 bash
root@d2314cac05e9:/# nodetool status
Datacenter: datacenter1
=======================
Status=Up/Down
|/ State=Normal/Leaving/Joining/Moving
--  Address     Load       Tokens       Owns (effective)  Host ID                               Rack
UN  172.18.0.2  108.59 KiB  256          100.0%            27b2b6f9-80f9-4236-a06c-15fa6ea9e575  rack1
UN  172.18.0.3  88.88 KiB  256          100.0%            efea05ac-f8e6-4909-9eff-979ac1720d36  rack1
```

3.	Expose the Cassandra cluster port to your local host, and run Kong locally connecting to the Dockerized Cassandra cluster.
4.	Prove that adding an API, protecting it with key-auth, and provisioning all the right Kong entities to consume the API (consumer, credentials) works.
5.	Login into the Cassandra cluster again, and list the plugin configuration from the C* table.


