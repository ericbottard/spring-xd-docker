= Spring XD on Docker

== Singlenode Image
TODO

== Cluster Deployment


```
docker run -d --name zookeeper springxd/zookeeper
```

```
docker run -d --name hsqldb springxd/hsqldb
```

```
docker run -d --name redis redis
```

```
docker run --name admin \
	--link zookeeper:zookeeper \
	--link hsqldb:hsqldb \
	--link redis:redis \
	-d \
	-P \
	springxd/admin
```

```
docker run --name container1 \
	--link zookeeper:zookeeper \
	--link hsqldb:hsqldb \
	--link redis:redis \
	springxd/container
```