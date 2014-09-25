# Spring XD on Docker

## Singlenode Image
TODO

## Cluster Deployment
You can also create a cluster configuration where each piece of Spring XD is running in
a separate container. Processes interact which each other using Docker links.

Let's start by running a ZooKeeper instance:
```
docker run -d --name zookeeper springxd/zookeeper
```

An HSQLDB server is also needed for managing Spring Batch jobs:
```
docker run -d --name hsqldb springxd/hsqldb
```

Let's use redis as our message bus and analytics store:
```
docker run -d --name redis redis
```

Now, it's time to spin up a Spring XD Admin node. We'll expose its 9393 port for now:
```
docker run --name admin \
	--link zookeeper:zookeeper \
	--link hsqldb:hsqldb \
	--link redis:redis \
	-d \
	-P \
	springxd/admin
```
Let's see which port got allocated using `docker ps`:
```
CONTAINER ID        IMAGE                       COMMAND                CREATED             STATUS              PORTS                     NAMES
570d230dedd1        springxd/admin:latest       "/opt/spring-xd/star   2 seconds ago       Up 2 seconds        0.0.0.0:49155->9393/tcp   admin
911fd1124af4        redis:latest                "redis-server"         15 seconds ago      Up 14 seconds       6379/tcp                  admin/redis,redis
9f1fc7e8c1f2        springxd/hsqldb:latest      "hsqldb/bin/hsqldb-s   19 seconds ago      Up 18 seconds       9101/tcp                  admin/hsqldb,hsqldb
0567eb7db601        springxd/zookeeper:latest   "/bin/sh -c './zkSer   23 seconds ago      Up 23 seconds       2181/tcp                  admin/zookeeper,zookeeper
```


Add a Spring XD container to the mix:
```
docker run --name container1 \
	--link zookeeper:zookeeper \
	--link hsqldb:hsqldb \
	--link redis:redis \
	springxd/container
```

Lastly, we'll fire up the Spring XD shell, in another terminal:
```
docker run --name shell -it springxd/shell
```

Inside the shell, let's connect to the admin server we created earlier:
```
server-unknown:>admin config server http://localhost:49155
```

If you're using `boot2docker`, don't forget to replace `localhost` with the result of `boot2docker ip`.

Now, let's create and deploy a simple stream:
```
xd:>stream create foo --definition "time | log"

xd:>stream deploy foo --properties "module.log.count=0"
```

The stream gets deployed on the (only) container.

Now, let's spin up another container. Given that we used `module.log.count=0`, the `log` module will automatically be deployed there, and messages will start to hit each container in turn:
```
docker run --name container2 \
	--link zookeeper:zookeeper \
	--link hsqldb:hsqldb \
	--link redis:redis \
	springxd/container
```

