## Deploy
```sh
docker-compose -f kafka-zoo/docker-compose.yml up -d
docker-compose -f kafka-zoo/docker-compose.yml down -v
docker-compose -f kafka-zoo/docker-compose.yml ps
```
## Added permission
```
sudo chmod -R 755 ./data
```

## Create and list topics
```sh
docker exec -it kafka1 bash

kafka-topics --create \
  --bootstrap-server kafka1:19092 \
  --replication-factor 3 \
  --partitions 3 \
  --topic ECS

kafka-topics --create \
  --bootstrap-server kafka1:19092 \
  --replication-factor 5 \
  --partitions 8 \
  --topic DATA-SERVICE

kafka-topics --list \
  --bootstrap-server kafka1:19092

kafka docker exec -it kafka2 bash
kafka-topics --list   --bootstrap-server kafka2:19093
```