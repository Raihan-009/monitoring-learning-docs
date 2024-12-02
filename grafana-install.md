# Grafana Installation and Configuration Guide

## Prerequisites
- Ubuntu/Debian-based system
- Root/sudo access
- Internet connectivity

## Installation Steps

### 1. Install Required Packages
```bash
sudo apt-get install -y apt-transport-https software-properties-common wget
```

### 2. Add Grafana Repository
```bash
# Add GPG key
wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -

# Add repository
sudo add-apt-repository "deb https://packages.grafana.com/oss/deb stable main"
```

### 3. Install Grafana
```bash
sudo apt-get update
sudo apt-get install grafana=6.6.2
```

### 4. Start and Enable Grafana
```bash
sudo systemctl enable grafana-server
sudo systemctl start grafana-server
```

### 5. Verify Installation
```bash
sudo systemctl status grafana-server
```

Expected output should show: `active (running)`

## Accessing Grafana

1. Open web browser
2. Navigate to `http://<GRAFANA_SERVER_PUBLIC_IP>:3000`
3. Default credentials:
   - Username: `admin`
   - Password: `admin`

## Configuring Prometheus Data Source

1. Log in to Grafana
2. Reset default password when prompted
3. Navigate to data sources:
   - Click "Add data source"
   - Select "Prometheus"
4. Configure data source:
   - URL: `http://10.0.1.101:9090`
   - Click "Save & Test"
   - Verify "Data source is working" message appears

## Testing the Setup

1. Click Explore icon in left sidebar
2. Enter query: `up`
3. Execute query to verify data retrieval

## Troubleshooting

Common issues and solutions:
- If Grafana service fails to start, check logs: `sudo journalctl -u grafana-server`
- If web interface is inaccessible, verify:
  - Firewall allows port 3000
  - Grafana service is running
  - Correct IP address is being used

## Security Notes

- Change default admin password immediately
- Use HTTPS in production environments
- Restrict access to Grafana server
- Regularly update to latest stable version

## Version Information
This guide uses Grafana version 6.6.2. For other versions, consult official documentation for any changes.