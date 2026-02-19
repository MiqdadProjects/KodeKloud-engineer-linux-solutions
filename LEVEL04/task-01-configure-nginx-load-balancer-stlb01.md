# 🔐 Task 01 - Configure Nginx Load Balancer on stlb01

**📌 Task Description**

The **Nautilus** production support team has observed a degradation in website performance due to increasing traffic. To address this, they have decided to deploy the application on a high availability stack in the **Stratos DC**. The migration is nearly complete, with only the **LBR (Load Balancer) server** configuration pending. This task involves installing **Nginx** on the `stlb01` server and configuring it as a high-performance **Load Balancer** using an HTTP upstream block to distribute traffic across App Servers 1, 2, and 3.

**👉 Task Requirements**:
- **Target Server**: LBR Server (`stlb01`)
- **Web Server**: Nginx
- **App Servers**: 
  - `stapp01`
  - `stapp02`
  - `stapp03`
- **Config Path**: `/etc/nginx/nginx.conf` (Main configuration file)
- **Operation Objectives**:
  - Install Nginx on the load balancer server.
  - Define an `upstream` group in the `http` context.
  - Configure a `server` block to proxy traffic to the backend pool.
  - Maintain existing Apache port configurations on app servers.
  - Ensure services are enabled and persistent.
- **Verification**: Use the **StaticApp** button to confirm high-availability access.

**💡 Note**: Ensure Apache service is active on all app servers before configuring the load balancer. The specific listener port for Apache must be identified dynamically.

---

## 🔹 Step 1: Connect to the Load Balancer Server

```bash
ssh loki@stlb01
sudo su -
```

**Purpose**: Establish an administrative session to provision the Nginx load balancer.

---

## 🔹 Step 2: Install and Provision Nginx

```bash
# Verify initial state
systemctl status nginx

# Install Nginx from the default repository
yum install nginx -y

# Start and enable for persistence
systemctl start nginx
systemctl enable nginx
```

**Success Indicators**:
- ✅ Nginx package installed successfully.
- ✅ Service status shows `active (running)`.

---

## 🔹 Step 3: Identify Backend Application Ports

Perform port discovery on all application servers to identify the active Apache listener.

```bash
ssh tony@stapp01 "sudo ss -luntp | grep httpd"
ssh steve@stapp02 "sudo ss -luntp | grep httpd"
ssh banner@stapp03 "sudo ss -luntp | grep httpd"
```

**Expected Result Analysis**:
Look for the port following the colon (e.g., `0.0.0.0:6400`).
*Example: Port **6400** detected across all nodes.*

---

## 🔹 Step 4: Configure Load Balancing (nginx.conf)

```bash
vi /etc/nginx/nginx.conf
```

**Purpose**: Implement the load balancing logic within the main Nginx configuration tree.

**Key Configuration (In http context)**:
```nginx
user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log;
pid /run/nginx.pid;

events {
    worker_connections 1024;
}

http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;
    sendfile        on;

    # Upstream Application Pool
    upstream app_servers {
        server stapp01:6400; # Use actual port found in Step 3
        server stapp02:6400;
        server stapp03:6400;
    }

    # Proxy Server Configuration
    server {
        listen       80 default_server;
        server_name  _;

        location / {
            proxy_pass http://app_servers;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }

        # Health monitoring stub
        location /nginx_status {
            stub_status on;
            access_log off;
            allow 127.0.0.1;
            deny all;
        }
    }
}
```

---

## 🔹 Step 5: Validate and Activate Traffic Routing

```bash
# Validate syntax integrity
nginx -t

# Apply high-availability configuration
systemctl restart nginx
```

**Expected Output for `nginx -t`**:
```text
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

---

## 🔹 Step 6: Final High-Availability Verification

```bash
# Test LB endpoint from Jump Host
curl -I http://stlb01.stratos.xfusioncorp.com
```

**Success Indicators**:
- ✅ `HTTP/1.1 200 OK` received from the Load Balancer.
- ✅ StaticApp button renders "Welcome to xFusionCorp Industries!".
- ✅ Traffic successfully traverses the proxy to the Apache backend.

---

## 📋 Quick Command Reference

Setup for **stlb01**:

```bash
# Installation
sudo yum install -y nginx

# Configuration
# Edit /etc/nginx/nginx.conf with upstream and proxy_pass settings

# Orchestration
sudo nginx -t && sudo systemctl enable --now nginx
```

---

## 💡 Common Load Balancer Issues & Fixes

### **Issue 1: 502 Bad Gateway**
**Problem**: Nginx cannot communicate with the backend App Servers.
**Fix**: Ensure the ports in `upstream` block match the ports found via `ss -luntp` on app servers. Verify Apache is running on all nodes.

### **Issue 2: Session Stickiness**
**Problem**: Users are losing session data as requests jump between servers.
**Fix**: Add `ip_hash;` inside the `upstream` block for persistent sessions.

---

## 🔧 Troubleshooting Failures

### **Error: nginx: [emerg] bind() to 0.0.0.0:80 failed (Address already in use)**
**Symptoms**: Nginx fails to start.
**Solution**: Check for other processes on port 80: `ss -tulpn | grep :80`. Kill conflicting processes or reconfigure them.

### **Error: App servers showing context failures in logs**
**Symptoms**: Intermittent 503 errors.
**Solution**: Check firewall rules on app servers. Ensure they allow traffic from the Load Balancer IP.

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The backend Apache servers were pre-configured to listen on a non-standard port (e.g., 6400). Hardcoding standard port 80 or 8080 in the proxy config without verification would lead to a total service outage.

**💡 Solution Approach**:
1. **Dynamic Discovery**: Utilized `ss` with remote SSH execution to definitively identify backend ports across the cluster before writing the LB config.
2. **Context Integrity**: Modified the `nginx.conf` directly as requested, rather than using separate modular files, ensuring compliance with the task's strict requirements.
3. **Robust Proxying**: Implemented `X-Forwarded-*` headers to preserve client metadata, which is critical for logging and security auditing in production.

---

## ⚠️ Important Production Notes

🔧 **Load Balancing Algorithms**: Round-robin is the default. Use `least_conn;` for workloads where request processing time varies significantly.
🔐 **Security**: Lock down backend servers to only accept traffic from the Load Balancer's IP address.
📊 **Monitoring**: Regularly check the `/nginx_status` endpoint to monitor active connections and server health.
🛡️ **Performance**: Use `worker_processes auto;` to allow Nginx to utilize all available CPU cores efficiently.

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Installed and activated **Nginx** on the `stlb01` Load Balancer.
- Configured a high-availability **Application Pool** targeting stapp01, stapp02, and stapp03.
- Successfully implemented **Reverse Proxy** logic to forward traffic to the Apache backend.
- Verified end-to-end connectivity and content delivery.

**🔍 Verification Completed**:
- **stlb01**: Config validated | Service persisted | Traffic routed.
- **Entry Point**: High-availability endpoint confirmed active.

**🔒 Configuration Summary**:
- **Protocol**: HTTP
- **Entry Port**: 80
- **Upstream Type**: Round Robin
- **Status**: Operational
