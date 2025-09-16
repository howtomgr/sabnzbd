# sabnzbd Installation Guide

sabnzbd is a free and open-source Usenet downloader. SABnzbd makes Usenet downloading simple with automation and integration

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Supported Operating Systems](#supported-operating-systems)
3. [Installation](#installation)
4. [Configuration](#configuration)
5. [Service Management](#service-management)
6. [Troubleshooting](#troubleshooting)
7. [Security Considerations](#security-considerations)
8. [Performance Tuning](#performance-tuning)
9. [Backup and Restore](#backup-and-restore)
10. [System Requirements](#system-requirements)
11. [Support](#support)
12. [Contributing](#contributing)
13. [License](#license)
14. [Acknowledgments](#acknowledgments)
15. [Version History](#version-history)
16. [Appendices](#appendices)

## 1. Prerequisites

- **Hardware Requirements**:
  - CPU: 1 core minimum
  - RAM: 512MB minimum
  - Storage: 1GB for downloads
  - Network: NNTP access
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 8080 (default sabnzbd port)
  - HTTPS on 9090
- **Dependencies**:
  - See official documentation for specific requirements
- **System Access**: root or sudo privileges required


## 2. Supported Operating Systems

This guide supports installation on:
- RHEL 8/9 and derivatives (CentOS Stream, Rocky Linux, AlmaLinux)
- Debian 11/12
- Ubuntu 20.04/22.04/24.04 LTS
- Arch Linux (rolling release)
- Alpine Linux 3.18+
- openSUSE Leap 15.5+ / Tumbleweed
- SUSE Linux Enterprise Server (SLES) 15+
- macOS 12+ (Monterey and later) 
- FreeBSD 13+
- Windows 10/11/Server 2019+ (where applicable)

## 3. Installation

### RHEL/CentOS/Rocky Linux/AlmaLinux

```bash
# Install EPEL repository if needed
sudo dnf install -y epel-release

# Install sabnzbd
sudo dnf install -y sabnzbd

# Enable and start service
sudo systemctl enable --now sabnzbd

# Configure firewall
sudo firewall-cmd --permanent --add-port=8080/tcp
sudo firewall-cmd --reload

# Verify installation
sabnzbd --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install sabnzbd
sudo apt install -y sabnzbd

# Enable and start service
sudo systemctl enable --now sabnzbd

# Configure firewall
sudo ufw allow 8080

# Verify installation
sabnzbd --version
```

### Arch Linux

```bash
# Install sabnzbd
sudo pacman -S sabnzbd

# Enable and start service
sudo systemctl enable --now sabnzbd

# Verify installation
sabnzbd --version
```

### Alpine Linux

```bash
# Install sabnzbd
apk add --no-cache sabnzbd

# Enable and start service
rc-update add sabnzbd default
rc-service sabnzbd start

# Verify installation
sabnzbd --version
```

### openSUSE/SLES

```bash
# Install sabnzbd
sudo zypper install -y sabnzbd

# Enable and start service
sudo systemctl enable --now sabnzbd

# Configure firewall
sudo firewall-cmd --permanent --add-port=8080/tcp
sudo firewall-cmd --reload

# Verify installation
sabnzbd --version
```

### macOS

```bash
# Using Homebrew
brew install sabnzbd

# Start service
brew services start sabnzbd

# Verify installation
sabnzbd --version
```

### FreeBSD

```bash
# Using pkg
pkg install sabnzbd

# Enable in rc.conf
echo 'sabnzbd_enable="YES"' >> /etc/rc.conf

# Start service
service sabnzbd start

# Verify installation
sabnzbd --version
```

### Windows

```bash
# Using Chocolatey
choco install sabnzbd

# Or using Scoop
scoop install sabnzbd

# Verify installation
sabnzbd --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/sabnzbd

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
sabnzbd --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable sabnzbd

# Start service
sudo systemctl start sabnzbd

# Stop service
sudo systemctl stop sabnzbd

# Restart service
sudo systemctl restart sabnzbd

# Check status
sudo systemctl status sabnzbd

# View logs
sudo journalctl -u sabnzbd -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add sabnzbd default

# Start service
rc-service sabnzbd start

# Stop service
rc-service sabnzbd stop

# Restart service
rc-service sabnzbd restart

# Check status
rc-service sabnzbd status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'sabnzbd_enable="YES"' >> /etc/rc.conf

# Start service
service sabnzbd start

# Stop service
service sabnzbd stop

# Restart service
service sabnzbd restart

# Check status
service sabnzbd status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start sabnzbd
brew services stop sabnzbd
brew services restart sabnzbd

# Check status
brew services list | grep sabnzbd
```

### Windows Service Manager

```powershell
# Start service
net start sabnzbd

# Stop service
net stop sabnzbd

# Using PowerShell
Start-Service sabnzbd
Stop-Service sabnzbd
Restart-Service sabnzbd

# Check status
Get-Service sabnzbd
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream sabnzbd_backend {
    server 127.0.0.1:8080;
}

server {
    listen 80;
    server_name sabnzbd.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name sabnzbd.example.com;

    ssl_certificate /etc/ssl/certs/sabnzbd.example.com.crt;
    ssl_certificate_key /etc/ssl/private/sabnzbd.example.com.key;

    location / {
        proxy_pass http://sabnzbd_backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### Apache Configuration

```apache
<VirtualHost *:80>
    ServerName sabnzbd.example.com
    Redirect permanent / https://sabnzbd.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName sabnzbd.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/sabnzbd.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/sabnzbd.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:8080/
    ProxyPassReverse / http://127.0.0.1:8080/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend sabnzbd_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/sabnzbd.pem
    redirect scheme https if !{ ssl_fc }
    default_backend sabnzbd_backend

backend sabnzbd_backend
    balance roundrobin
    server sabnzbd1 127.0.0.1:8080 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R sabnzbd:sabnzbd /etc/sabnzbd
sudo chmod 750 /etc/sabnzbd

# Configure firewall
sudo firewall-cmd --permanent --add-port=8080/tcp
sudo firewall-cmd --reload

# Enable SELinux policies (if applicable)
sudo setsebool -P httpd_can_network_connect on
```

## Database Setup

See official documentation for database configuration requirements.

## Performance Optimization

### System Tuning

```bash
# Basic system tuning
echo 'net.core.somaxconn = 65535' | sudo tee -a /etc/sysctl.conf
echo 'net.ipv4.tcp_max_syn_backlog = 65535' | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

## Monitoring

### Basic Monitoring

```bash
# Check service status
sudo systemctl status sabnzbd

# View logs
sudo journalctl -u sabnzbd -f

# Monitor resource usage
top -p $(pgrep sabnzbd)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/sabnzbd"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/sabnzbd-backup-$DATE.tar.gz" /etc/sabnzbd /var/lib/sabnzbd

echo "Backup completed: $BACKUP_DIR/sabnzbd-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop sabnzbd

# Restore from backup
tar -xzf /backup/sabnzbd/sabnzbd-backup-*.tar.gz -C /

# Start service
sudo systemctl start sabnzbd
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u sabnzbd -n 100
sudo tail -f /var/log/sabnzbd/sabnzbd.log

# Check configuration
sabnzbd --version

# Check permissions
ls -la /etc/sabnzbd
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 8080

# Test connectivity
telnet localhost 8080

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep sabnzbd)

# Check disk I/O
iotop -p $(pgrep sabnzbd)

# Check connections
ss -an | grep 8080
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  sabnzbd:
    image: sabnzbd:latest
    ports:
      - "8080:8080"
    volumes:
      - ./config:/etc/sabnzbd
      - ./data:/var/lib/sabnzbd
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update sabnzbd

# Debian/Ubuntu
sudo apt update && sudo apt upgrade sabnzbd

# Arch Linux
sudo pacman -Syu sabnzbd

# Alpine Linux
apk update && apk upgrade sabnzbd

# openSUSE
sudo zypper update sabnzbd

# FreeBSD
pkg update && pkg upgrade sabnzbd

# Always backup before updates
tar -czf /backup/sabnzbd-pre-update-$(date +%Y%m%d).tar.gz /etc/sabnzbd

# Restart after updates
sudo systemctl restart sabnzbd
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/sabnzbd

# Clean old logs
find /var/log/sabnzbd -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/sabnzbd
```

## Additional Resources

- Official Documentation: https://docs.sabnzbd.org/
- GitHub Repository: https://github.com/sabnzbd/sabnzbd
- Community Forum: https://forum.sabnzbd.org/
- Best Practices Guide: https://docs.sabnzbd.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
