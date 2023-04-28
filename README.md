# conf42-sre-observability

## Pre requirements 
Docker installed 


## Deploy a stack for observability

### Deploy cadvisor
```bash
# Install cadvisor
docker run \
  --volume=/:/rootfs:ro \
  --volume=/var/run:/var/run:ro \
  --volume=/sys:/sys:ro \
  --volume=/var/lib/docker/:/var/lib/docker:ro \
  --volume=/dev/disk/:/dev/disk:ro \
  --publish=8080:8080 \
  --detach=true \
  --name=cadvisor \
  --privileged \
  --device=/dev/kmsg \
  gcr.io/cadvisor/cadvisor:latest

## Access to cadvisor
http://localhost:8080

## Access to cadvisor metrics
http://localhost:8080/metrics

```


### Deploy prometheus 
With this we start collect the metrics and storage using prometheus
```bash
# Copy the conf file
cp prometheus-conf.yaml /tmp

# Install and configure prometheus by default for docker
docker run --name prometheus -d -p 9090:9090 -v /tmp/prometheus-conf.yaml:/etc/prometheus/prometheus.yml prom/prometheus 

## Access to prometheus
http://localhost:9090
## Validate targets
http://localhost:9090/targets

```


### Deploy grafana
With this we can use a dashboards for Docker stack
```bash
# Install a default grafana
docker run -d --name=grafana -p 3000:3000 grafana/grafana
## Access to grafana
http://localhost:3000
```
#### Configure datasource on grafana
On this steps we configure a prometheus datasource on grafana

> :warning: **First you need check your IP address local**: like a 192.168.xx.xx

1. Go to Administration > Data Sources
1.1 Add new data source
1.2 Select prometheus
1.3 In URL drop `http://<yourip>:9090
1.4 Navigate to bottom 
1.5 Save & test
1.6 Done

> :warning: **Appear a green box if every going well** If not, please refer to documentation


### (optional) Add workloads for your Docker environment
```bash
# Clone the content of this, if you don't have any app
git https://github.com/dockersamples/example-voting-app.git

## Move into the dir
cd example-voiting-app

### Execute the app
docker compose up

#### Access to vote
http://localhost:5000/
#### Access to results
http://localhost:5001/
```


## References
- https://docs.docker.com/config/daemon/prometheus/
- https://prometheus.io/docs/prometheus/latest/installation/
- https://github.com/dockersamples/example-voting-app
- https://grafana.com/docs/grafana/latest/setup-grafana/installation/docker/
- https://hub.docker.com/r/grafana/grafana-oss
- https://medium.com/@nagavenkateshgowru/monitoring-docker-containers-d26bd0a791b4
- https://github.com/google/cadvisor


- https://grafana.com/grafana/dashboards/179-docker-prometheus-monitoring/
- https://grafana.com/grafana/dashboards/16310-docker-and-system-monitoring/


- https://grafana.com/grafana/dashboards/3662-prometheus-2-0-overview/
- https://grafana.com/grafana/dashboards/2-prometheus-stats/
- https://grafana.com/grafana/dashboards/15489-prometheus-2-0-stats/

- https://grafana.com/grafana/dashboards/3590-grafana-internals/