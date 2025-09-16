# Icecast Installation Guide

Icecast is a free and open-source Streaming Server. Streaming media server

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
  - CPU: 2 cores minimum (4+ cores recommended)
  - RAM: 2GB minimum (4GB+ recommended)
  - Storage: 1GB for installation
  - Network: 8000 ports
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 8000 (default icecast port)
- **Dependencies**:
  - libxml2, libxslt1.1
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

# Install icecast
sudo dnf install -y icecast libxml2, libxslt1.1

# Enable and start service
sudo systemctl enable --now icecast2

# Configure firewall
sudo firewall-cmd --permanent --add-service=icecast
sudo firewall-cmd --reload

# Verify installation
icecast --version || systemctl status icecast2
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install icecast
sudo apt install -y icecast libxml2, libxslt1.1

# Enable and start service
sudo systemctl enable --now icecast2

# Configure firewall
sudo ufw allow 8000

# Verify installation
icecast --version || systemctl status icecast2
```

### Arch Linux

```bash
# Install icecast
sudo pacman -S icecast

# Enable and start service
sudo systemctl enable --now icecast2

# Verify installation
icecast --version || systemctl status icecast2
```

### Alpine Linux

```bash
# Install icecast
apk add --no-cache icecast

# Enable and start service
rc-update add icecast2 default
rc-service icecast2 start

# Verify installation
icecast --version || rc-service icecast2 status
```

### openSUSE/SLES

```bash
# Install icecast
sudo zypper install -y icecast libxml2, libxslt1.1

# Enable and start service
sudo systemctl enable --now icecast2

# Configure firewall
sudo firewall-cmd --permanent --add-service=icecast
sudo firewall-cmd --reload

# Verify installation
icecast --version || systemctl status icecast2
```

### macOS

```bash
# Using Homebrew
brew install icecast

# Start service
brew services start icecast

# Verify installation
icecast --version
```

### FreeBSD

```bash
# Using pkg
pkg install icecast

# Enable in rc.conf
echo 'icecast2_enable="YES"' >> /etc/rc.conf

# Start service
service icecast2 start

# Verify installation
icecast --version || service icecast2 status
```

### Windows

```powershell
# Using Chocolatey
choco install icecast

# Or using Scoop
scoop install icecast

# Verify installation
icecast --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory if needed
sudo mkdir -p /etc/icecast2

# Set up basic configuration
sudo tee /etc/icecast2/icecast.conf << 'EOF'
# Icecast Configuration
<clients>1000</clients>
EOF

# Test configuration
sudo icecast -t || sudo icecast2 configtest

# Reload service
sudo systemctl reload icecast2
```

### Security Hardening

```bash
# Set appropriate permissions
sudo chown -R icecast:icecast /etc/icecast2
sudo chmod 750 /etc/icecast2

# Enable security features
# See security section for detailed hardening steps
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable icecast2

# Start service
sudo systemctl start icecast2

# Stop service
sudo systemctl stop icecast2

# Restart service
sudo systemctl restart icecast2

# Reload configuration
sudo systemctl reload icecast2

# Check status
sudo systemctl status icecast2

# View logs
sudo journalctl -u icecast2 -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add icecast2 default

# Start service
rc-service icecast2 start

# Stop service
rc-service icecast2 stop

# Restart service
rc-service icecast2 restart

# Check status
rc-service icecast2 status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'icecast2_enable="YES"' >> /etc/rc.conf

# Start service
service icecast2 start

# Stop service
service icecast2 stop

# Restart service
service icecast2 restart

# Check status
service icecast2 status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start icecast
brew services stop icecast
brew services restart icecast

# Check status
brew services list | grep icecast
```

### Windows Service Manager

```powershell
# Start service
net start icecast2

# Stop service
net stop icecast2

# Using PowerShell
Start-Service icecast2
Stop-Service icecast2
Restart-Service icecast2

# Check status
Get-Service icecast2
```

## Advanced Configuration

### Performance Optimization

```bash
# Configure performance settings
cat >> /etc/icecast2/icecast.conf << 'EOF'
<clients>1000</clients>
EOF

# Apply system tuning
sudo sysctl -w net.core.somaxconn=65535
sudo sysctl -w net.ipv4.tcp_max_syn_backlog=65535

# Restart service
sudo systemctl restart icecast2
```

### Clustering and High Availability

```bash
# Configure clustering (if supported)
# See official documentation for cluster setup

# Basic load balancing setup example
# Configure multiple instances on different ports
```

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream icecast_backend {
    server 127.0.0.1:8000;
    server 127.0.0.1:{default_port}1 backup;
}

server {
    listen 80;
    server_name icecast.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name icecast.example.com;

    ssl_certificate /etc/ssl/certs/icecast.example.com.crt;
    ssl_certificate_key /etc/ssl/private/icecast.example.com.key;

    location / {
        proxy_pass http://icecast_backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        
        # WebSocket support (if needed)
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
}
```

### Apache Configuration

```apache
<VirtualHost *:80>
    ServerName icecast.example.com
    Redirect permanent / https://icecast.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName icecast.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/icecast.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/icecast.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:8000/
    ProxyPassReverse / http://127.0.0.1:8000/
    
    # WebSocket support (if needed)
    RewriteEngine on
    RewriteCond %{HTTP:Upgrade} websocket [NC]
    RewriteCond %{HTTP:Connection} upgrade [NC]
    RewriteRule ^/?(.*) "ws://127.0.0.1:8000/$1" [P,L]
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend icecast_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/icecast.pem
    redirect scheme https if !{ ssl_fc }
    default_backend icecast_backend

backend icecast_backend
    balance roundrobin
    option httpchk GET /health
    server icecast1 127.0.0.1:8000 check
    server icecast2 127.0.0.1:{default_port}1 check backup
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R icecast:icecast /etc/icecast2
sudo chmod 750 /etc/icecast2

# Configure firewall
sudo firewall-cmd --permanent --add-service=icecast
sudo firewall-cmd --reload

# Enable SELinux policies (if applicable)
sudo setsebool -P httpd_can_network_connect on

# Configure fail2ban
sudo tee /etc/fail2ban/jail.d/icecast.conf << 'EOF'
[icecast]
enabled = true
port = 8000
filter = icecast
logpath = /var/log/icecast2/*.log
maxretry = 5
bantime = 3600
EOF
```

### SSL/TLS Configuration

```bash
# Generate SSL certificates
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -keyout /etc/ssl/private/icecast.key \
    -out /etc/ssl/certs/icecast.crt

# Configure SSL in icecast
# See official documentation for SSL configuration
```

## Database Setup

### PostgreSQL Backend (if applicable)

```bash
# Create database and user
sudo -u postgres psql << EOF
CREATE DATABASE icecast_db;
CREATE USER icecast_user WITH ENCRYPTED PASSWORD 'secure_password';
GRANT ALL PRIVILEGES ON DATABASE icecast_db TO icecast_user;
EOF

# Configure icecast to use PostgreSQL
# See official documentation for database configuration
```

### MySQL/MariaDB Backend (if applicable)

```bash
# Create database and user
sudo mysql << EOF
CREATE DATABASE icecast_db;
CREATE USER 'icecast_user'@'localhost' IDENTIFIED BY 'secure_password';
GRANT ALL PRIVILEGES ON icecast_db.* TO 'icecast_user'@'localhost';
FLUSH PRIVILEGES;
EOF
```

## Performance Optimization

### System Tuning

```bash
# Kernel parameters
sudo tee -a /etc/sysctl.conf << EOF
net.core.somaxconn = 65535
net.ipv4.tcp_max_syn_backlog = 65535
net.ipv4.ip_local_port_range = 1024 65535
net.core.netdev_max_backlog = 5000
vm.swappiness = 10
EOF

sudo sysctl -p

# Icecast specific tuning
<clients>1000</clients>
```

### Resource Limits

```bash
# Configure system limits
sudo tee -a /etc/security/limits.conf << EOF
icecast soft nofile 65535
icecast hard nofile 65535
icecast soft nproc 32768
icecast hard nproc 32768
EOF
```

## Monitoring

### Prometheus Integration

```yaml
# prometheus.yml configuration
scrape_configs:
  - job_name: 'icecast'
    static_configs:
      - targets: ['localhost:8000']
    metrics_path: '/metrics'
```

### Health Checks

```bash
# Basic health check script
#!/bin/bash
if systemctl is-active --quiet icecast2; then
    echo "Icecast is running"
    exit 0
else
    echo "Icecast is not running"
    exit 1
fi
```

### Log Monitoring

```bash
# Configure log rotation
sudo tee /etc/logrotate.d/icecast << 'EOF'
/var/log/icecast2/*.log {
    daily
    rotate 14
    compress
    delaycompress
    missingok
    notifempty
    create 0640 icecast icecast
    postrotate
        systemctl reload icecast2 > /dev/null 2>&1 || true
    endscript
}
EOF
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Icecast backup script
BACKUP_DIR="/backup/icecast"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"

# Stop service (if required)
systemctl stop icecast2

# Backup configuration
tar -czf "$BACKUP_DIR/icecast-config-$DATE.tar.gz" /etc/icecast2

# Backup data (adjust paths as needed)
tar -czf "$BACKUP_DIR/icecast-data-$DATE.tar.gz" /var/lib/icecast

# Start service
systemctl start icecast2

# Clean old backups (keep 30 days)
find "$BACKUP_DIR" -name "*.tar.gz" -mtime +30 -delete

echo "Backup completed: $BACKUP_DIR"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop icecast2

# Restore configuration
sudo tar -xzf /backup/icecast/icecast-config-*.tar.gz -C /

# Restore data
sudo tar -xzf /backup/icecast/icecast-data-*.tar.gz -C /

# Set permissions
sudo chown -R icecast:icecast /etc/icecast2
sudo chown -R icecast:icecast /var/lib/icecast

# Start service
sudo systemctl start icecast2
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u icecast2 -n 100
sudo tail -f /var/log/icecast2/*.log

# Check configuration
sudo icecast -t || sudo icecast2 configtest

# Check permissions
ls -la /etc/icecast2
ls -la /var/lib/icecast
```

2. **Connection refused**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 8000
sudo netstat -tlnp | grep 8000

# Check firewall
sudo firewall-cmd --list-all
sudo iptables -L -n

# Test connection
telnet localhost 8000
nc -zv localhost 8000
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep icecast)
htop -p $(pgrep icecast)

# Check connections
ss -ant | grep :8000 | wc -l

# Monitor I/O
iotop -p $(pgrep icecast)
```

### Debug Mode

```bash
# Run in debug mode
sudo icecast -d
# or
sudo icecast2 debug

# Increase log verbosity
# Edit configuration to enable debug logging
```

## Integration Examples

### Docker Compose

```yaml
version: '3.8'
services:
  icecast:
    image: icecast:latest
    container_name: icecast
    ports:
      - "8000:8000"
    volumes:
      - ./config:/etc/icecast2
      - ./data:/var/lib/icecast
    environment:
      - icecast_CONFIG=/etc/icecast2/icecast.conf
    restart: unless-stopped
    networks:
      - icecast_net

networks:
  icecast_net:
    driver: bridge
```

### Kubernetes Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: icecast
spec:
  replicas: 3
  selector:
    matchLabels:
      app: icecast
  template:
    metadata:
      labels:
        app: icecast
    spec:
      containers:
      - name: icecast
        image: icecast:latest
        ports:
        - containerPort: 8000
        volumeMounts:
        - name: config
          mountPath: /etc/icecast2
      volumes:
      - name: config
        configMap:
          name: icecast-config
---
apiVersion: v1
kind: Service
metadata:
  name: icecast
spec:
  selector:
    app: icecast
  ports:
  - port: 8000
    targetPort: 8000
  type: LoadBalancer
```

### Ansible Playbook

```yaml
---
- name: Install and configure Icecast
  hosts: all
  become: yes
  tasks:
    - name: Install icecast
      package:
        name: icecast
        state: present
    
    - name: Configure icecast
      template:
        src: icecast.conf.j2
        dest: /etc/icecast2/icecast.conf
        owner: icecast
        group: icecast
        mode: '0640'
      notify: restart icecast
    
    - name: Start and enable icecast
      systemd:
        name: icecast2
        state: started
        enabled: yes
  
  handlers:
    - name: restart icecast
      systemd:
        name: icecast2
        state: restarted
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update icecast

# Debian/Ubuntu
sudo apt update && sudo apt upgrade icecast

# Arch Linux
sudo pacman -Syu icecast

# Alpine Linux
apk update && apk upgrade icecast

# openSUSE
sudo zypper update icecast

# FreeBSD
pkg update && pkg upgrade icecast

# Always backup before updates
tar -czf /backup/icecast-pre-update-$(date +%Y%m%d).tar.gz /etc/icecast2

# Restart after updates
sudo systemctl restart icecast2
```

### Regular Maintenance Tasks

```bash
# Clean logs
find /var/log/icecast2 -name "*.log" -mtime +30 -delete

# Verify integrity
sudo icecast --verify || sudo icecast2 check

# Update databases (if applicable)
sudo icecast-update-db

# Optimize performance
sudo icecast-optimize

# Check for security updates
sudo icecast --security-check
```

## Additional Resources

- Official Documentation: https://docs.icecast.org/
- GitHub Repository: https://github.com/icecast/icecast
- Community Forum: https://forum.icecast.org/
- Wiki: https://wiki.icecast.org/
- Comparison vs SHOUTcast, Liquidsoap, Azuracast: https://docs.icecast.org/comparison

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
