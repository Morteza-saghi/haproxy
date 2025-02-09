# HAProxy Documentation: Overview and Best Practices

## Introduction
HAProxy (High Availability Proxy) is an open-source software widely used as a load balancer and proxy server for TCP and HTTP-based applications. Known for its high performance, reliability, and flexibility, HAProxy is commonly deployed in environments that require high availability, scalability, and fault tolerance.

This document provides an overview of HAProxy, its architecture, key features, configuration basics, and best practices for deployment.

## Key Features

### Load Balancing:
- Supports multiple load-balancing algorithms (round-robin, least connections, source, etc.).
- Backend health checks ensure traffic is routed only to healthy servers.

### High Availability:
- Offers failover capabilities to reduce downtime.
- Works well with VRRP (e.g., via `keepalived`) for seamless failover between HAProxy instances.

### Protocol Support:
- Supports TCP (Layer 4) and HTTP/HTTPS (Layer 7).
- SSL termination and passthrough for encrypted traffic.

### Performance:
- Optimized for high concurrency with a low resource footprint.
- Can handle tens of thousands of simultaneous connections efficiently.

### Advanced Routing:
- Supports URL-based routing, content switching, and ACLs (Access Control Lists).
- Path and header manipulation for custom routing requirements.

### Monitoring and Logging:
- Provides detailed logs and metrics for performance and debugging.
- Integration with monitoring systems like Prometheus and Grafana.

### Extensibility:
- Configurable via its powerful configuration file (`haproxy.cfg`).
- Supports LUA scripting for custom functionalities.

## Architecture
HAProxy operates in a frontend-backend model:
- **Frontend**: Defines how client requests are received (e.g., ports, protocols, ACLs).
- **Backend**: Specifies the servers where traffic is routed.
- **Listeners**: Bind specific ports and direct traffic based on rules defined in the configuration.

## Installation

### Prerequisites
- A Linux-based system (Ubuntu, CentOS, etc.).
- Root or sudo privileges.

### Installation Steps

#### On Debian/Ubuntu:
```bash
sudo apt update
sudo apt install haproxy
```

On RHEL/CentOS:
```
sudo yum install epel-release
sudo yum install haproxy
```

From Source:
Download the latest release from the HAProxy official website.
Compile the source:
```
make TARGET=linux-glibc
sudo make install
```
Verify Installation:
```
haproxy -v
```

### Configuration Basics
The configuration file is located at /etc/haproxy/haproxy.cfg.

Example Configuration:
```
global
    log /dev/log local0
    log /dev/log local1 notice
    maxconn 2000
    daemon

defaults
    log     global
    option  httplog
    option  dontlognull
    timeout connect 5000ms
    timeout client  50000ms
    timeout server  50000ms

frontend http_front
    bind *:80
    default_backend servers

backend servers
    balance roundrobin
    server server1 192.168.1.101:80 check
    server server2 192.168.1.102:80 check

```

### Key Sections:
Global Section:
Defines global settings (logging, process limits, etc.).
Defaults Section:
Sets default parameters for frontends and backends.

---

Frontend Section:
Specifies how incoming traffic is handled.

---

Backend Section:
Defines the pool of servers for traffic distribution.


---

## HAProxy Best Practices

### General Recommendations

#### Backup Configurations
- Regularly back up the `haproxy.cfg` file before making changes.

#### Health Checks
- Enable health checks to monitor the health of backend servers:
```haproxy
server web1 192.168.1.101:80 check
```

### Logging and Monitoring
Use detailed logging for debugging:
```
option httplog
```
Integrate metrics with monitoring tools like Prometheus.


### Security
Use SSL/TLS for encrypted communication:
```
bind *:443 ssl crt /path/to/cert.pem
```
Restrict access using ACLs.

---

### Scaling
- Use a dynamic DNS or service discovery for dynamic backend scaling.
- Deploy multiple HAProxy instances with a failover mechanism (e.g., keepalived).

### Performance Tuning
- Adjust maxconn and timeout values based on traffic patterns.
- Optimize resource usage by tuning kernel parameters (e.g., sysctl).


### Troubleshooting
Check Configuration Syntax:
```
haproxy -c -f /etc/haproxy/haproxy.cfg
```

---

### View Logs:
Logs are usually stored in `/var/log/syslog` or `/var/log/messages.`

---

### Reload Configuration:

Apply configuration changes without downtime:
```
sudo systemctl reload haproxy
```


### inspect HAProxy Stats:

Enable the statistics page in the config:
```
frontend stats
    bind *:8080
    stats enable
    stats uri /haproxy?stats
```
