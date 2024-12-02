# Prometheus Installation and Setup Guide

This guide walks through the process of installing and configuring Prometheus on a Linux system.

## Prerequisites
- A Linux system with sudo privileges
- wget installed
- systemd as the init system

## Installation Steps

### 1. Set Up User and Directories

First, create a dedicated system user for Prometheus and set up the required directories:

```bash
# Create prometheus user
sudo useradd -M -r -s /bin/false prometheus

# Create necessary directories
sudo mkdir /etc/prometheus /var/lib/prometheus
```

### 2. Download and Extract Prometheus

Download and extract the Prometheus binaries:

```bash
# Download Prometheus
wget https://github.com/prometheus/prometheus/releases/download/v2.16.0/prometheus-2.16.0.linux-amd64.tar.gz

# Extract the archive
tar xzf prometheus-2.16.0.linux-amd64.tar.gz prometheus-2.16.0.linux-amd64/
```

### 3. Install Prometheus Files

Copy the Prometheus binaries and configuration files to their appropriate locations:

```bash
# Copy binaries
sudo cp prometheus-2.16.0.linux-amd64/{prometheus,promtool} /usr/local/bin/
sudo chown prometheus:prometheus /usr/local/bin/{prometheus,promtool}

# Copy configuration files
sudo cp -r prometheus-2.16.0.linux-amd64/{consoles,console_libraries} /etc/prometheus/
sudo cp prometheus-2.16.0.linux-amd64/prometheus.yml /etc/prometheus/prometheus.yml

# Set correct ownership
sudo chown -R prometheus:prometheus /etc/prometheus
sudo chown prometheus:prometheus /var/lib/prometheus
```

### 4. Verify Installation

Test the installation by running Prometheus in the foreground:

```bash
prometheus --config.file=/etc/prometheus/prometheus.yml
```

## Setting Up Prometheus as a System Service

### 1. Create Systemd Service File

Create a new systemd service file for Prometheus:

```bash
sudo vi /etc/systemd/system/prometheus.service
```

Add the following content to the service file:

```ini
[Unit]
Description=Prometheus Time Series Collection and Processing Server
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
Group=prometheus
Type=simple
ExecStart=/usr/local/bin/prometheus \
    --config.file /etc/prometheus/prometheus.yml \
    --storage.tsdb.path /var/lib/prometheus/ \
    --web.console.templates=/etc/prometheus/consoles \
    --web.console.libraries=/etc/prometheus/console_libraries

[Install]
WantedBy=multi-user.target
```

### 2. Enable and Start Prometheus Service

```bash
# Reload systemd configuration
sudo systemctl daemon-reload

# Start Prometheus service
sudo systemctl start prometheus

# Enable Prometheus to start on boot
sudo systemctl enable prometheus
```

### 3. Verify Service Status

Check if Prometheus is running correctly:

```bash
# Check service status
sudo systemctl status prometheus

# Test HTTP endpoint
curl localhost:9090
```

## Accessing Prometheus

You can access the Prometheus web interface in two ways:
1. Locally: `http://localhost:9090`
2. Remotely: `http://<PROMETHEUS_SERVER_PUBLIC_IP>:9090`

## Troubleshooting

If you encounter issues:
1. Check the service status: `sudo systemctl status prometheus`
2. View logs: `sudo journalctl -u prometheus`
3. Verify file permissions in `/etc/prometheus` and `/var/lib/prometheus`
4. Ensure ports are not blocked by firewall rules

## Security Considerations

1. Consider setting up authentication for the web interface
2. Review firewall rules to restrict access to port 9090
3. Regularly update Prometheus to the latest version
4. Use HTTPS if exposing the web interface publicly

## Next Steps

After installation, consider:
1. Configuring additional scrape targets
2. Setting up alerting rules
3. Integrating with Grafana for visualization
4. Implementing monitoring for your specific services