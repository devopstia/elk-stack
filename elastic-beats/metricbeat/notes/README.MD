## Metricbeat Installation

- https://www.elastic.co/guide/en/beats/metricbeat/current/metricbeat-installation-configuration.html

- https://elasticsearch.evermight.com/metricbeat-install-part-1/

- https://www.youtube.com/watch?v=aEaBmz5lJKw&list=PLPatHYWw1RVvx7Zk6QXTOxv-BQXFTksMB&index=1

## Set the host name while launching the server
```sh
#!/bin/bash
hostnamectl set-hostname bashtion-host
``

## Make sure the version matches the Elasticsearch version
```sh
# https://www.elastic.co/guide/en/beats/metricbeat/current/metricbeat-installation-configuration.html

curl -L -O https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-8.16.0-amd64.deb
apt update
sudo dpkg -i metricbeat-8.16.0-amd64.deb
sudo systemctl enable metricbeat
sudo systemctl start metricbeat
sudo systemctl status metricbeat
```

## configure metricbeat
make sure you add this `ssl.verification_mode: "none" `output.elasticsearch` 
```sh
sudo vim /etc/metricbeat/metricbeat.yml

## Verify the config
sudo /usr/share/metricbeat/bin/metricbeat test config -c /etc/metricbeat/metricbeat.yml --path.data /var/lib/metricbeat --path.home /usr/share/metricbeat

## Setup metricbeat for the first time to push dashboards
sudo /usr/share/metricbeat/bin/metricbeat setup -c /etc/metricbeat/metricbeat.yml --path.data /var/lib/metricbeat --path.home /usr/share/metricbeat
```

## Check metricbeat logs
```
journalctl -u metricbeat -n 30
```

## Monitor containers.
All metricbeat modules are disable by default
```sh
ls /etc/metricbeat/modules.d
sudo metricbeat modules list

sudo metricbeat modules enable docker
sudo metricbeat modules enable kafka

ls /etc/metricbeat/modules.d |grep docker
ls /etc/metricbeat/modules.d |grep kafka

# sudo vim /etc/metricbeat/modules.d/docker.yml
- module: docker
  metricsets:
    - container
    - cpu
    - diskio
    - healthcheck
    - info
    - memory
    - network
  hosts: ["unix:///var/run/docker.sock"]
  period: 10s
  enabled: true

## sudo vim /etc/metricbeat/modules.d/kafka.yml
- module: kafka
  metricsets:
    - partition
    - broker
  hosts: ["kafka:9092"]  # Replace "kafka" with your Kafka container's hostname
  period: 10s
  enabled: true

sudo useradd -r -s /bin/false metricbeat
sudo chmod 666 /var/run/docker.sock
sudo usermod -aG docker metricbeat
sudo systemctl restart metricbeat
sudo systemctl enable metricbeat
```