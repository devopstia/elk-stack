## Auditbeat with Elasticsearch
https://www.youtube.com/watch?v=mfwXr07ydmE&list=PLPatHYWw1RVvx7Zk6QXTOxv-BQXFTksMB&index=3

https://elasticsearch.evermight.com/auditbeat-install-part-1/

## Repositories for APT and YUM
```sh
## https://www.elastic.co/guide/en/beats/auditbeat/current/setup-repositories.html

apt update
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
sudo apt-get install apt-transport-https
echo "deb https://artifacts.elastic.co/packages/8.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-8.x.list
sudo apt-get update && sudo apt-get install auditbeat

## vim /etc/auditbeat/auditbeat.yml
- module: system
  state.period: 2m
setup.dashboards.enabled: true
setup.kibana:
  host: "https://<kibana-domain>:<kibana-port>"
output.elasticsearch:
  hosts: ["<elasticsearch-domain>:<elasticsearch-port>"]
  ssl.verification_mode: "none"
  protocol: "https"
  username: "elastic"
  password: "<your elastic password>"

sudo systemctl enable auditbeat
sudo systemctl start auditbeat
sudo systemctl status auditbeat

## check config
/usr/share/auditbeat/bin/auditbeat test config -c /etc/auditbeat/auditbeat.yml --path.data /var/lib/auditbeat --path.home /usr/share/auditbeat

## Setup Auditbeat
/usr/share/auditbeat/bin/auditbeat setup -c /etc/auditbeat/auditbeat.yml --path.data /var/lib/auditbeat --path.home /usr/share/auditbeat
```





