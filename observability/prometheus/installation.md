# Download
```
wget -q --show-progress --https-only --timestamping \
  https://github.com/prometheus/prometheus/releases/download/v2.53.3/prometheus-2.53.3.linux-amd64.tar.gz
```

# Install on AlmaLinux
```
# Add User
sudo groupadd --system prometheus
sudo useradd --system -s /sbin/nologin -g prometheus prometheus

tar -xvf prometheus-2.53.3.linux-amd64.tar.gz
sudo mv prometheus promtool /usr/local/bin

sudo mkdir /etc/prometheus
sudo mv consoles/ console_libraries/ prometheus.yml /etc/prometheus

sudo mkdir /var/lib/prometheus
sudo chown -R prometheus:prometheus /var/lib/prometheus

# Disabled Selinux
sudo setenforce 0
```

# Configuration
```
cat <<EOF | sudo tee /etc/prometheus/prometheus.yml
global:
  scrape_interval: 15s
  evaluation_interval: 15s

scrape_configs:
  - job_name: "prometheus"
    static_configs:
      - targets: ["localhost:9090"]
EOF
```

# Create Service
```
cat <<EOF | sudo tee /etc/systemd/system/prometheus.service
[Unit]
Description=Prometheus
Documentation=https://prometheus.io/docs/introduction/overview/

[Service]
User=prometheus
Group=prometheus
Restart=always
Type=simple

ExecStart=/usr/local/bin/prometheus \\
  --config.file=/etc/prometheus/prometheus.yml \\
  --storage.tsdb.path=/var/lib/prometheus \\
  --storage.tsdb.retention.time=30d \\
  --web.console.templates=/etc/prometheus/consoles \\
  --web.console.libraries=/etc/prometheus/console_libraries \\
  --web.listen-address=0.0.0.0:9090 \\
  -- web.enable-lifecycle

[Install]
WantedBy=multi-user.target
EOF
```

# Start Service
```
sudo systemctl daemon-reload
sudo systemctl enable prometheus
sudo systemctl start prometheus
sudo systemctl status prometheus

http://192.168.100.2:9090
http://192.168.100.2:9090/metrics
```