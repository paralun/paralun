# Download
```
wget -q --show-progress --https-only --timestamping \
  https://github.com/prometheus/node_exporter/releases/download/v1.8.2/node_exporter-1.8.2.linux-amd64.tar.gz
```

# Install AlmaLinux
```
tar -xvf node_exporter-1.8.2.linux-amd64.tar.gz
sudo mv node_exporter /usr/local/bin
sudo chmod +x /usr/local/bin/node_exporter
```

# Create Service
```
cat <<EOF | sudo tee /etc/systemd/system/node-exporter.service
[Unit]
Description=Node Exporter
Documentation=https://prometheus.io/docs/introduction/overview/

[Service]
User=prometheus
Group=prometheus
Restart=always
Type=simple

Restart=on-failure
ExecStart=/usr/bin/node_exporter
ExecReload=/bin/kill -HUP $MAINPID
TimeoutStopSec=20s
SendSIGKILL=no

[Install]
WantedBy=multi-user.target
EOF
```

# Start Service
```
sudo systemctl daemon-reload
sudo systemctl enable node-exporter
sudo systemctl start node-exporter
sudo systemctl status node-exporter

sudo lsof -n -P -i | grep node
http://192.168.100.2:9100/metrics
```

# Modify Configuration Prometheus
```
# sudo vi /etc/prometheus/prometheus.yml 
scrape_configs:
  - job_name: "prometheus"
    static_configs:
      - targets: ["localhost:9090"]
  - job_name: "node-exporter"
    static_configs:
      - targets: ["localhost:9100"]
```

# Restart Service Prometheus