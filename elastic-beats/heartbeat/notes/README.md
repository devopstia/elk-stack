## Repositories for APT and YUM
https://www.elastic.co/guide/en/beats/heartbeat/current/setup-repositories.html

## Heartbeat with Elasticsearch 8.x - Part 1: Install & Secure
```sh
https://elasticsearch.evermight.com/heartbeat-install-part-1/

wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -

sudo apt-get install apt-transport-https

echo "deb https://artifacts.elastic.co/packages/8.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-8.x.list

sudo apt-get update && sudo apt-get install heartbeat-elastic

sudo systemctl enable heartbeat-elastic
sudo systemctl status heartbeat-elastic
```

## Configure Heartbeat
```sh
## /etc/heartbeat/heartbeat.yml
heartbeat.monitors:
- type: http
  # ID used to uniquely identify this monitor in elasticsearch even if the config changes
  id: apache-website
  # Human readable display name for this service in Uptime UI and elsewhere
  name: Apache Website
  # List or urls to query
  urls: ["<url>"]  #["http://192.168.0.60"]
  # Configure task schedule
  schedule: '@every 5s'
  # Total test connection and data exchange timeout
  #timeout: 16s
  # Name of corresponding APM service, if Elastic APM is in use for the monitored service.
  #service.name: my-apm-service-name
- type: http
  # ID used to uniquely identify this monitor in elasticsearch even if the config changes
  id: elastic-rest
  # Human readable display name for this service in Uptime UI and elsewhere
  name: Elastic Rest
  # List or urls to query
  urls: ["<elasticsearch-domain>:<elasticsearch-port>"]
  username: 'elastic'
  password: "<your elastic password>"
  # Configure task schedule
  schedule: '@every 5s'
setup.kibana:
  host: "https://<kibana-domain>:<kibana-port>"
output.elasticsearch:
  hosts: ["<elasticsearch-domain>:<elasticsearch-port>"]
  ssl.verification_mode: "none"
  protocol: "https"
  username: "elastic"
  password: "<your elastic password>"

## /etc/heartbeat/heartbeat.yml
## we must allow ping on the sg to use ping
heartbeat.monitors:
- type: http
  enabled: true
  id: kibana-monitor
  name: Kibana Monitor
  urls: ["http://3.225.236.30:5601"]
  schedule: '@every 10s'

- type: http
  enabled: true
  id: apache-monitor
  name: Apache Monitor
  urls: ["http://54.146.45.68:80"]
  schedule: '@every 10s'

- type: http
  enabled: true
  id: elasticsearch-monitor
  name: Elasticsearch Monitor
  urls: ["https://3.225.236.30:9200"]
  schedule: '@every 10s'

- type: http
  enabled: true
  id: del-monitor
  name: DEL Monitor
  urls: ["jenkins.devopseasylearning.uk"]
  schedule: '@every 10s'

- type: icmp
  enabled: true
  id: ping-apache-server
  name: Monitor Apacher Server
  hosts: ["54.146.45.68"]
  schedule: '@every 10s'

- type: icmp
  enabled: true
  id: ping-elasticsearch-server
  name: Monitor Elasticsearch Server
  hosts: ["3.225.236.30"]
  schedule: '@every 10s'

## check config
sudo /usr/share/heartbeat/bin/heartbeat test config -c /etc/heartbeat/heartbeat.yml --path.data /var/lib/heartbeat --path.home /usr/share/heartbeat

## start
sudo systemctl enable heartbeat-elastic
sudo systemctl start heartbeat-elastic
sudo systemctl status heartbeat-elastic

## setup
sudo /usr/share/heartbeat/bin/heartbeat setup -c /etc/heartbeat/heartbeat.yml --path.data /var/lib/heartbeat --path.home /usr/share/heartbeat
```

## Access in kibana
Got to overview - monitors and click on see all monitors
![alt text](image.png)

## using /etc/heartbeat/monitors.d
- When we add the heartbeat minitor in /etc/heartbeat/heartbeat.yml, we have to run `sudo systemctl restart heartbeat-elastic` every single time that we add a new monitor
- We can add those those in `sample.http.yml.disabled  sample.icmp.yml.disabled  sample.tcp.yml.disabled` and heartbeat will reload the configuration automatically.
- All these are disable by default. We just need to add those and remove disabled.
- Each protocal should be in the specific file
```sh
ls /etc/heartbeat/monitors.d/
sample.http.yml.disabled  sample.icmp.yml.disabled  sample.tcp.yml.disabled
