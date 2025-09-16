# cockpit-cms Installation Guide

cockpit-cms is a free and open-source API-first CMS. Cockpit provides simple content platform for developers

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
  - Storage: 1GB for data
  - Network: HTTP/API access
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 80 (default cockpit-cms port)
  - None
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

# Install cockpit-cms
sudo dnf install -y cockpit-cms

# Enable and start service
sudo systemctl enable --now cockpit-cms

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload

# Verify installation
cockpit-cms --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install cockpit-cms
sudo apt install -y cockpit-cms

# Enable and start service
sudo systemctl enable --now cockpit-cms

# Configure firewall
sudo ufw allow 80

# Verify installation
cockpit-cms --version
```

### Arch Linux

```bash
# Install cockpit-cms
sudo pacman -S cockpit-cms

# Enable and start service
sudo systemctl enable --now cockpit-cms

# Verify installation
cockpit-cms --version
```

### Alpine Linux

```bash
# Install cockpit-cms
apk add --no-cache cockpit-cms

# Enable and start service
rc-update add cockpit-cms default
rc-service cockpit-cms start

# Verify installation
cockpit-cms --version
```

### openSUSE/SLES

```bash
# Install cockpit-cms
sudo zypper install -y cockpit-cms

# Enable and start service
sudo systemctl enable --now cockpit-cms

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload

# Verify installation
cockpit-cms --version
```

### macOS

```bash
# Using Homebrew
brew install cockpit-cms

# Start service
brew services start cockpit-cms

# Verify installation
cockpit-cms --version
```

### FreeBSD

```bash
# Using pkg
pkg install cockpit-cms

# Enable in rc.conf
echo 'cockpit-cms_enable="YES"' >> /etc/rc.conf

# Start service
service cockpit-cms start

# Verify installation
cockpit-cms --version
```

### Windows

```bash
# Using Chocolatey
choco install cockpit-cms

# Or using Scoop
scoop install cockpit-cms

# Verify installation
cockpit-cms --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/cockpit-cms

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
cockpit-cms --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable cockpit-cms

# Start service
sudo systemctl start cockpit-cms

# Stop service
sudo systemctl stop cockpit-cms

# Restart service
sudo systemctl restart cockpit-cms

# Check status
sudo systemctl status cockpit-cms

# View logs
sudo journalctl -u cockpit-cms -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add cockpit-cms default

# Start service
rc-service cockpit-cms start

# Stop service
rc-service cockpit-cms stop

# Restart service
rc-service cockpit-cms restart

# Check status
rc-service cockpit-cms status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'cockpit-cms_enable="YES"' >> /etc/rc.conf

# Start service
service cockpit-cms start

# Stop service
service cockpit-cms stop

# Restart service
service cockpit-cms restart

# Check status
service cockpit-cms status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start cockpit-cms
brew services stop cockpit-cms
brew services restart cockpit-cms

# Check status
brew services list | grep cockpit-cms
```

### Windows Service Manager

```powershell
# Start service
net start cockpit-cms

# Stop service
net stop cockpit-cms

# Using PowerShell
Start-Service cockpit-cms
Stop-Service cockpit-cms
Restart-Service cockpit-cms

# Check status
Get-Service cockpit-cms
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream cockpit-cms_backend {
    server 127.0.0.1:80;
}

server {
    listen 80;
    server_name cockpit-cms.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name cockpit-cms.example.com;

    ssl_certificate /etc/ssl/certs/cockpit-cms.example.com.crt;
    ssl_certificate_key /etc/ssl/private/cockpit-cms.example.com.key;

    location / {
        proxy_pass http://cockpit-cms_backend;
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
    ServerName cockpit-cms.example.com
    Redirect permanent / https://cockpit-cms.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName cockpit-cms.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/cockpit-cms.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/cockpit-cms.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:80/
    ProxyPassReverse / http://127.0.0.1:80/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend cockpit-cms_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/cockpit-cms.pem
    redirect scheme https if !{ ssl_fc }
    default_backend cockpit-cms_backend

backend cockpit-cms_backend
    balance roundrobin
    server cockpit-cms1 127.0.0.1:80 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R cockpit-cms:cockpit-cms /etc/cockpit-cms
sudo chmod 750 /etc/cockpit-cms

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
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
sudo systemctl status cockpit-cms

# View logs
sudo journalctl -u cockpit-cms -f

# Monitor resource usage
top -p $(pgrep cockpit-cms)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/cockpit-cms"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/cockpit-cms-backup-$DATE.tar.gz" /etc/cockpit-cms /var/lib/cockpit-cms

echo "Backup completed: $BACKUP_DIR/cockpit-cms-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop cockpit-cms

# Restore from backup
tar -xzf /backup/cockpit-cms/cockpit-cms-backup-*.tar.gz -C /

# Start service
sudo systemctl start cockpit-cms
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u cockpit-cms -n 100
sudo tail -f /var/log/cockpit-cms/cockpit-cms.log

# Check configuration
cockpit-cms --version

# Check permissions
ls -la /etc/cockpit-cms
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 80

# Test connectivity
telnet localhost 80

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep cockpit-cms)

# Check disk I/O
iotop -p $(pgrep cockpit-cms)

# Check connections
ss -an | grep 80
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  cockpit-cms:
    image: cockpit-cms:latest
    ports:
      - "80:80"
    volumes:
      - ./config:/etc/cockpit-cms
      - ./data:/var/lib/cockpit-cms
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update cockpit-cms

# Debian/Ubuntu
sudo apt update && sudo apt upgrade cockpit-cms

# Arch Linux
sudo pacman -Syu cockpit-cms

# Alpine Linux
apk update && apk upgrade cockpit-cms

# openSUSE
sudo zypper update cockpit-cms

# FreeBSD
pkg update && pkg upgrade cockpit-cms

# Always backup before updates
tar -czf /backup/cockpit-cms-pre-update-$(date +%Y%m%d).tar.gz /etc/cockpit-cms

# Restart after updates
sudo systemctl restart cockpit-cms
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/cockpit-cms

# Clean old logs
find /var/log/cockpit-cms -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/cockpit-cms
```

## Additional Resources

- Official Documentation: https://docs.cockpit-cms.org/
- GitHub Repository: https://github.com/cockpit-cms/cockpit-cms
- Community Forum: https://forum.cockpit-cms.org/
- Best Practices Guide: https://docs.cockpit-cms.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
