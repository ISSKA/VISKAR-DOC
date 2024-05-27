# Prometheus installation

```bash
cd ~
mkdir prometheus-files
cd prometheus-files
```

## Prometheus

This component centralizes metrics.

Tutorial followed: https://prometheus.io/docs/guides/node-exporter  
Version installed: 2.32.1 / 2021-12-17 https://github.com/prometheus/prometheus/releases

```bash
wget https://github.com/prometheus/prometheus/releases/download/v2.32.1/prometheus-2.32.1.linux-amd64.tar.gz
tar xvfz prometheus-2.32.1.linux-amd64.tar.gz
mv prometheus-2.32.1.linux-amd64 prometheus
rm prometheus-2.32.1.linux-amd64.tar.gz
nano prometheus.yml
```

Config contents

```
global:
  scrape_interval: 15s

scrape_configs:
- job_name: node
  static_configs:
  - targets: ['localhost:9100']
```

### Service

Tutorial partially followed: https://www.virtono.com/community/tutorial-how-to/how-to-install-and-configure-prometheus-on-a-linux-server/

```bash
nano /etc/systemd/system/prometheus.service
```

```
[Unit]

Description=Prometheus
Wants=network-online.target
Wants=prometheus-node-exporter.service
After=network-online.target

[Service]

Type=simple
ExecStart=/root/prometheus-files/prometheus/prometheus --config.file /root/prometheus-files/prometheus.yml

[Install]

WantedBy=multi-user.target
```

## Node exporter

This component is used to export machine metrics.

Tutorial followed: https://prometheus.io/docs/guides/node-exporter  
Version installed: 1.3.1 / 2021-12-01 https://github.com/prometheus/node_exporter/releases

```bash
wget https://github.com/prometheus/node_exporter/releases/download/v1.3.1/node_exporter-1.3.1.linux-amd64.tar.gz
tar xvfz node_exporter-1.3.1.linux-amd64.tar.gz
mv node_exporter-1.3.1.linux-amd64 node_exporter
rm node_exporter-1.3.1.linux-amd64.tar.gz
```

### Service

```bash
nano /etc/systemd/system/prometheus-node-exporter.service
```

```
[Unit]

Description=Prometheus Node Exporter

[Service]

Type=simple
ExecStart=/root/prometheus-files/node_exporter/node_exporter

[Install]

WantedBy=prometheus.service
```

## Start

```bash
systemctl daemon-reload
systemctl enable prometheus
systemctl enable prometheus-node-exporter
systemctl start prometheus
```

# Grafana installation

Tutorial followed: https://grafana.com/docs/grafana/latest/getting-started/getting-started-prometheus/  
Version installed: Grafana OSS from repository `https://packages.grafana.com/oss/deb stable main`

```bash
wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
echo "deb https://packages.grafana.com/oss/deb stable main" | tee -a /etc/apt/sources.list.d/grafana.list
apt update
apt install grafana
systemctl daemon-reload
systemctl enable grafana-server
systemctl start grafana-server
```

## Configuration

```bash
nano /etc/grafana/grafana.ini
```

Changed values:

```ini
#################################### Server ####################################
[server]
root_url = https://grafana.visualkarsys.com
#################################### Analytics ####################################
[analytics]
reporting_enabled = false
#################################### Security ####################################
[security]
cookie_secure = true
cookie_samesite = strict
#################################### SMTP / Emailing ##########################
[smtp]
enabled = true
host = mail.infomaniak.com:587
user = visualkarsys@visualkarsys.com
password = ************
from_address = visualkarsys@visualkarsys.com
from_name = VISKAR Grafana
ehlo_identity = grafana.visualkarsys.com
startTLS_policy = MandatoryStartTLS
```

```bash
systemctl restart grafana-server
```

## Login & Dashboard

Tutorial followed: https://grafana.com/docs/grafana/latest/datasources/prometheus/

Navigate to http://159.69.66.141:3000/login

default credentials: `admin` / `admin`  
New credentials: `admin` / `42)r!rBZ4MMCJwT2`

Add a data source. Choose Prometheus.  
HTTP URL: http://localhost:9090

Import dashboard: https://grafana.com/grafana/dashboards/13978?pg=dashboards&plcmt=featured-dashboard-2  
Dashboard was adapted a little to our use case

Link to the final dashboard: https://grafana.visualkarsys.com/d/fkXdZAb7k/viskar-health-checker?orgId=1&refresh=15s&from=now-3h&to=now

## Configure alerts

Alerts taken from: https://awesome-prometheus-alerts.grep.to/rules.html  
These alerts are meant for Prometheus, where we originally configured them.  
After encountering issues with Prometheus alerts, we decided to put them directly in Grafana, which is also able to handle alerts.  
For this reason, the alerts had to be adapted a little, but the base query is the same.  
Alerts chosen (see website for details):

- 1.2.1. Host out of memory
- 1.2.13. Host high CPU load
- 1.2.28. Host Network Interface Saturated

# Futur improvements

Adding exporters for PostgreSQL, Nginx and JVM could be useful to setup alerts in the future, for example:

- 2.2.7. Postgresql too many connections 
- 2.2.10. Postgresql slow queries
- 3.1.1. Nginx high HTTP 4xx error rate
- 3.1.2. Nginx high HTTP 5xx error rate
- 4.2.1. JVM memory filling up

- The dahsboard metrics can be adjusted to show more relevent info after we test out the current settings
- A plugin can enable Github integration, to show commits / pull requests etc on the charts. Might be interresting.
- If we install the Jenkins exporter on Prometheus, we could also show Jenkins deployments on the charts, which could provide context to up/down spikes
