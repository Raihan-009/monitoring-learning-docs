# Installing and Configuring Node Exporter on Target Web Server

This guide provides step-by-step instructions for installing Node Exporter on a target web server and configuring Prometheus to collect metrics from it.

## Part 1: Node Exporter Installation

### 1. User and Group Creation
First, create a dedicated system user and group for Node Exporter:
```bash
sudo useradd -M -r -s /bin/false node_exporter
```

### 2. Download and Extract Node Exporter
Download and extract the Node Exporter binary:
```bash
wget https://github.com/prometheus/node_exporter/releases/download/v0.18.1/node_exporter-0.18.1.linux-amd64.tar.gz
tar xvfz node_exporter-0.18.1.linux-amd64.tar.gz
```

### 3. Install Binary and Set Permissions
Copy the binary to the appropriate location and set ownership:
```bash
sudo cp node_exporter-0.18.1.linux-amd64/node_exporter /usr/local/bin/
sudo chown node_exporter:node_exporter /usr/local/bin/node_exporter
```

### 4. Create Systemd Service
Create a systemd unit file for Node Exporter:
```bash
sudo vi /etc/systemd/system/node_exporter.service
```

Add the following content to the file:
```ini
[Unit]
Description=Prometheus Node Exporter
Wants=network-online.target
After=network-online.target

[Service]
User=node_exporter
Group=node_exporter
Type=simple
ExecStart=/usr/local/bin/node_exporter

[Install]
WantedBy=multi-user.target
```

### 5. Enable and Start the Service
Reload systemd, start Node Exporter, and enable it to start on boot:
```bash
sudo systemctl daemon-reload
sudo systemctl start node_exporter
sudo systemctl enable node_exporter
```

### 6. Verify Installation
Test that Node Exporter is working properly:
```bash
curl localhost:9100/metrics
```
You should see metric data in the response.

## Part 2: Prometheus Configuration

### 1. Configure Prometheus
On the Prometheus server, edit the configuration file:
```bash
sudo vi /etc/prometheus/prometheus.yml
```

Add the following job under the `scrape_configs` section:
```yaml
  - job_name: 'Target Web Server'
    static_configs:
      - targets: ['10.10.10.10:9100']
```
Note: Replace `10.0.1.102` with your target server's IP address.

### 2. Apply Changes
Restart Prometheus to apply the configuration:
```bash
sudo systemctl restart prometheus
```

### 3. Verify Configuration
1. Access the Prometheus web interface at `http://<PROMETHEUS_SERVER_PUBLIC_IP>:9090`
2. Test the configuration by running this query:
```
node_filesystem_avail_bytes{job="Target Web Server"}
```

## Troubleshooting

If you encounter issues:
1. Check Node Exporter status: `sudo systemctl status node_exporter`
2. Verify Node Exporter is listening: `netstat -tulpn | grep 9100`
3. Check Prometheus configuration: `promtool check config /etc/prometheus/prometheus.yml`
4. Review Prometheus logs: `sudo journalctl -u prometheus`

## Security Considerations

1. Consider implementing firewall rules to restrict access to Node Exporter (port 9100)
2. Use TLS encryption for production environments
3. Implement authentication if required
4. Regularly update Node Exporter to the latest stable version

## Additional Notes

- Default Node Exporter port: 9100
- Metrics endpoint: `/metrics`
- Node Exporter collects system metrics including CPU, memory, disk, and network statistics