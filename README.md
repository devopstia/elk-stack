## Deploy
```sh
docker-compose -f kafka-zoo/docker-compose.yml up -d
docker-compose -f kafka-zoo/docker-compose.yml down -v
docker-compose -f kafka-zoo/docker-compose.yml ps
```

```sh
docker-compose -f elk/docker-compose.yml up -d
docker-compose -f elk/docker-compose.yml down -v
docker-compose -f elk/docker-compose.yml ps
```

## Access ElasticSearch
Please use https
```
https://localhost:9200
Username: elastic
Password: lm72k9a84b8j7xkk
```

## Kibana access
```
http://localhost:5601/
Username: elastic
Password: lm72k9a84b8j7xkk
```

## Notes for Memlimit issue
The memlimit issue can be solved using the following command:
```sh
Linux
sysctl -w vm.max_map_count=262144

To fix it permanently, you need to edit the /etc/sysctl.conf

Then add or edit the line to: vm.max_map_count=262144

Windows (WSL):
open powershell run wsl -d docker-desktop

then sysctl -w vm.max_map_count=262144

You might need to redo this step when you have restarted your computer.
```

## Article at Stack Overflow:
https://stackoverflow.com/questions/51445846/elasticsearch-max-virtual-memory-areas-vm-max-map-count-65530-is-too-low-inc