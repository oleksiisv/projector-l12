# Balancing

Run ```docker-compose up -d``` to launch the environment. 

## Geo balancing

Balancer configuration is located in the ```config/balancer/geoip.conf``` file.

Containers ```app-uk, app-us, app-all``` are used to simulate the IP addresses from US, UK and other countries. 

### UK

```docker exec -it l12-app-uk-1 curl 172.18.236.100```\
172.18.236.100 (IP of the load balancer)

Result: "Node 1", which is the content of the Node 1 server index.html 

### US

US upstream contains 2 servers\
```docker exec -it l12-app-us-1 bin/bash```\
Execute multiple curl requests\
```for i in `seq 1 10`; do curl 172.18.236.100; echo; done```\
Result:\
Node 2\
Node 3\
Node 2\
Node 3\
Node 2\
Node 3\
Node 2\
Node 3\
Node 2\
Node 3

### Other

```docker exec -it l12-app-all-1 curl 172.18.236.100 ```\
Result: "Node 4"

## Healthchecks and failover

Health checks are added to the balancer config. Along with the backup upstream. Stop any of the upstream contains (node 1-3) to make backup server work

```shell
upstream nodes_uk {
    server node-1:8000 fail_timeout=5s;
    server node-4:8000 backup;
}

upstream nodes_us {
    server node-2:8000 fail_timeout=5s;
    server node-3:8000 fail_timeout=5s;
    server node-4:8000 backup;
}

```



