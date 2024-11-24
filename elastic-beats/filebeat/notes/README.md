## Download Link
https://www.elastic.co/guide/en/beats/filebeat/current/setup-repositories.html

## Filebeat with Elasticsearch 8.x
https://www.youtube.com/watch?v=Bquc9I63DA0&list=PLPatHYWw1RVvx7Zk6QXTOxv-BQXFTksMB&index=7

## Configure inputs
https://www.elastic.co/guide/en/beats/filebeat/current/configuration-filebeat-options.html

## STEPS
```sh
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -

sudo apt-get install apt-transport-https

echo "deb https://artifacts.elastic.co/packages/8.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-8.x.list

sudo apt-get update && sudo apt-get install filebeat
sudo systemctl enable filebeat
sudo systemctl start filebeat
sudo systemctl status filebeat

## /etc/filebeat/filebeat.yml
## ls /etc/filebeat/modules.d/
filebeat.inputs:
- type: filestream
  # Unique ID among all inputs, an ID is required.
  id: sys-log-id
  # Change to true to enable this input configuration.
  enabled: true
  # Paths that should be crawled and fetched. Glob based paths.
  paths:
    - /var/log/*.log

filebeat.config.modules:
  path: ${path.config}/modules.d/*.yml
  reload.enabled: true
  # 10s reload for demonstration purposes
  reload.period: 10s
setup.dashboards.enabled: false
setup.kibana:
  host: "https://<kibana-domain>:<kibana-port>"
output.elasticsearch:
  hosts: ["<elasticsearch-domain>:<elasticsearch-port>"]
  ssl.verification_mode: "none"
  protocol: "https"
  username: "elastic"
  password: "<your elastic password>"


## Check configs
/usr/share/filebeat/bin/filebeat test config -c /etc/filebeat/filebeat.yml --path.data /var/lib/filebeat --path.home /usr/share/filebeat

/usr/share/filebeat/bin/filebeat test output -c /etc/filebeat/filebeat.yml --path.data /var/lib/filebeat --path.home /usr/share/filebeat

## Start filebeat
sudo systemctl start filebeat
sudo systemctl status filebeat

/usr/share/filebeat/bin/filebeat setup -c /etc/filebeat/filebeat.yml --path.data /var/lib/filebeat --path.home /usr/share/filebeat

## enable system.yml
cd /etc/filebeat/modules.d/
vim vim system.yml.disabled
- module: system
  # Syslog
  syslog:
    enabled: true

  # Authorization logs
  auth:
    enabled: true

## rename the file. this will reload automatically as `reload.enabled: true` in filebeat config
mv system.yml.disabled system.yml
```