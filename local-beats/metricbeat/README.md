## Example with modules
We can add modules in metricbeat.yml. The best way is the enable modules in `modules.d` instead
```yaml
name: elasticserch-kibana

metricbeat.config.modules:
  path: ${path.config}/modules.d/*.yml
  reload.enabled: true
  reload.period: 10s

setup.template.settings:
  index.number_of_shards: 1
  index.codec: best_compression

## Docker Module
metricbeat.modules:
- module: docker
  metricsets:
    - "container"
    - "cpu"
    - "diskio"
    - "event"
    - "healthcheck"
    - "info"
    - "memory"
    - "network"
  hosts: ["unix:///var/run/docker.sock"]
  period: 10s
  enabled: true

setup.kibana:
  host: "http://3.225.236.30:5601" 
setup.dashboards.enabled: true

output.elasticsearch:
  hosts: ["https://3.225.236.30:9200"]
  preset: balanced
  protocol: "https"
  username: "elastic"
  password: "onw06_8uvZjvZWpotiGk"
  ssl.verification_mode: "none"

processors:
  - add_docker_metadata:
      host: "unix:///var/run/docker.sock"

processors:
  - add_host_metadata: ~
  - add_cloud_metadata: ~
  - add_docker_metadata: ~
  - add_kubernetes_metadata: ~
```