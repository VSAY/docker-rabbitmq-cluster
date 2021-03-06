Docker images to run RabbitMQ cluster. It extends the official image with a rabbitmq-cluster script that does the magic.

# Building

Once you clone the project locally build the image:

```
docker build -t rabbitmq-cluster:latest .
```

# Running with docker-compose

# To start standalone 

If you want to run rabbitmq on one machine use [docker-compose](https://github.com/docker/compose/)

```
docker-compose up -d 
```

Standalone node is started up this way:

```
rabbit1:
  image: rabbitmq-cluster
  environment:
    - ERLANG_COOKIE=abcdefg
  ports:
    - "5672:5672"
    - "15672:15672"
```  

# To start a Cluster 

If you want to run the cluster on one machine use [docker-compose](https://github.com/docker/compose/)

```
docker-compose up -d --file rabbitmq-cluster-docker-compose.yml
```

Default 3 nodes are started up this way:

```
rabbit1:
  image: rabbitmq-cluster
  hostname: rabbit1
  environment:
    - ERLANG_COOKIE=abcdefg
  ports:
    - "5672:5672"
    - "15672:15672"
rabbit2:
  image: rabbitmq-cluster
  hostname: rabbit2
  links:
    - rabbit1
  environment:
    - ERLANG_COOKIE=abcdefg
    - CLUSTER_WITH=rabbit1
    - ENABLE_RAM=true
    - RAM_NODE=true
  ports:
    - "5673:5672"
    - "15673:15672"
rabbit3:
  image: rabbitmq-cluster
  hostname: rabbit3
  links:
    - rabbit1
    - rabbit2
  environment:
    - ERLANG_COOKIE=abcdefg
    - CLUSTER_WITH=rabbit1
  ports:
    - "5674:5672"
```

If needed, additional nodes can be added to this file.

Once cluster is up:
* The management console can be accessed at `http://hostip:15672`
* The connection host should look like this: `hostip:5672,hostip:5673,hostip:5674`

# Credits

* Inspired by https://github.com/harbur/docker-rabbitmq-cluster
