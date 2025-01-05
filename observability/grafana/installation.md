# Install on Redhat
```
sudo yum install -y https://dl.grafana.com/enterprise/release/grafana-enterprise-11.4.0-1.x86_64.rpm
```

# Start Service
```
sudo systemctl enable grafana-server
sudo systemctl start grafana-server
sudo systemctl status grafana-server

sudo lsof -n -P -i | grep grafana
http://192.168.100.2:3000
```