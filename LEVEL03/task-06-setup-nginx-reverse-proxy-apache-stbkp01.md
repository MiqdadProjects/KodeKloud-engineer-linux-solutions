# 🔐 Task 06 - Configure Nginx Reverse Proxy for Apache on Backup Server

**📌 Task Description**

The **Nautilus DevOps** team is implementing a frontend interface for their backup utility on the **Nautilus Backup Server (stbkp01)**. To optimize performance and provide a secure access layer, they have decided on a dual-webserver architecture. **Apache** will serve as the backend application server listening on port **5001**, while **Nginx** will act as a high-performance **Reverse Proxy** listening on port **8097**. This configuration allows Nginx to handle incoming client requests and efficiently forward them to the Apache backend.

**👉 Task Requirements**:
- **Target Server**: Nautilus Backup Server (`stbkp01`)
- **Apache Configuration**:
  - Listening Port: `5001` (Bind to all interfaces)
  - Document Root: `/var/www/html/`
- **Nginx Configuration**:
  - Listening Port: `8097`
  - Proxy Target: `http://stbkp01:5001`
- **Content**: Deploy `index.html` from Jump Host to Apache's document root.
- **Operation Objectives**:
  - Install `httpd` and `nginx` packages.
  - Reconfigure Apache to use port 5001.
  - Configure Nginx as a reverse proxy for the Apache backend.
  - Transfer and deploy the web content.
  - Enable and start both services.
- Validate the reverse proxy chain via `curl`.

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Transfer Web Content from Jump Host

Perform this step from the **Jump Host**:

```bash
# Transfer the sample index file to the backup server
scp /home/thor/index.html clint@stbkp01:/tmp/
```

**Purpose**: Move the application frontend content to the target server for deployment.

---

## 🔹 Step 2: Connect to Backup Server and Install Packages

```bash
ssh clint@stbkp01
sudo su -

# Install both web servers
yum install -y httpd nginx
```

**Success Indicators**:
- ✅ `httpd` (Apache) and `nginx` packages installed successfully.

---

## 🔹 Step 3: Configure Apache Backend (Port 5001)

```bash
vi /etc/httpd/conf/httpd.conf
```

**Changes Required**:
Find the `Listen` directive and update it:
```apache
# Change from:
Listen 80
# Change to:
Listen 5001
```

**Deploy Index File**:
```bash
mv /tmp/index.html /var/www/html/
```

**Success Indicators**:
- ✅ Apache reconfigured to listen on port 5001.
- ✅ Content deployed to `/var/www/html/`.

---

## 🔹 Step 4: Configure Nginx as Reverse Proxy (Port 8097)

```bash
vi /etc/nginx/nginx.conf
```

**Purpose**: Define the proxy logic to forward traffic from Nginx (8097) to Apache (5001).

**Configuration Update**:
In the `server` block, modify the `listen` port and add the `location /` block:

```nginx
server {
    listen       8097;
    server_name  _;
    root         /usr/share/nginx/html;

    # Reverse Proxy Logic
    location / {
        proxy_pass http://stbkp01:5001;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

**Success Indicators**:
- ✅ Nginx set to listen on port 8097.
- ✅ `proxy_pass` directive correctly targets the Apache backend.

---

## 🔹 Step 5: Activate and Enable Services

```bash
# Enable and start Apache
systemctl enable --now httpd

# Enable and start Nginx
systemctl enable --now nginx
```

**Success Indicators**:
- ✅ Both `httpd` and `nginx` show `active (running)`.
- ✅ Services configured to persist across reboots.

---

## 🔹 Step 6: Final Verification

```bash
# Check status
systemctl status httpd nginx

# Test connectivity to the Nginx frontend
curl http://stbkp01:8097
```

**Expected Output**:
```html
Welcome to xFusionCorp Industries!
```

**Success Indicators**:
- ✅ Nginx successfully proxies the request to Apache.
- ✅ Branding content from `index.html` is returned via port 8097.

---

## 📋 Quick Command Reference

Setup for **stbkp01**:

```bash
# Prep
sudo yum install -y httpd nginx
sudo mv /tmp/index.html /var/www/html/

# Config Apache
sudo sed -i 's/Listen 80/Listen 5001/g' /etc/httpd/conf/httpd.conf

# Config Nginx (Proxy)
# Add manual edit to /etc/nginx/nginx.conf as per Step 4

# Rollout
sudo systemctl enable --now httpd nginx
```

---

## 💡 Common Proxy Issues & Fixes

### **Issue 1: 502 Bad Gateway**
**Problem**: Nginx is running, but it cannot reach the Apache backend.
**Fix**: Ensure Apache is running on port 5001 and that the `proxy_pass` URL is exactly correct (matching Apache's listener).

### **Issue 2: Port Binding Conflict**
**Problem**: Apache or Nginx fails to start because a port is already taken.
**Fix**: Use `ss -tulpn | grep -E '5001|8097'` to identify conflicting processes.

### **Issue 3: Permission Denied (SELinux)**
**Problem**: Nginx is prohibited from making network connections to the backend.
**Fix**: If SELinux is active, allow Nginx to proxy connections:
```bash
sudo setsebool -P httpd_can_network_connect 1
```

---

## 🔧 Troubleshooting Failures

### **Error: nginx: [emerg] bind() to 0.0.0.0:8097 failed**
**Symptoms**: Nginx fails to start.
**Solution**: Check for other Nginx instances or applications bound to 8097.

### **Error: Curl returns Apache's default page instead of index.html**
**Symptoms**: Incorrect content served.
**Solution**: Ensure `index.html` was correctly moved to `/var/www/html/` and has proper read permissions (`644`).

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The multi-hop nature of reverse proxying requires distinct ports for the frontend and backend to avoid conflicts on the same host. Additionally, Apache must be configured to allow the proxy's IP/hostname.

**💡 Solution Approach**:
1. **Duality of Service**: Installed both packages concurrently to minimize deployment downtime.
2. **Explicit Internal Mapping**: Used the backup server's hostname (`stbkp01`) in the `proxy_pass` directive to ensure the proxy follows internal networking rules.
3. **Sequential Activation**: Started Apache first to ensure the backend was "warm" before Nginx began attempting to proxy traffic to it.

---

## ⚠️ Important Production Notes

🔧 **Headers**: Always use `proxy_set_header` in Nginx to ensure Apache sees the original client's IP address instead of the proxy's IP.
🔐 **Security**: Consider closing port 5001 in the firewall to external traffic, forcing all users to go through the Nginx proxy (Port 8097).
📊 **Logs**: Monitor `/var/log/nginx/error.log` for proxy-related timeouts or failures.
🛡️ **Cleanup**: Remove default test pages from `/usr/share/nginx/html` to prevent accidental exposure.

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Deployed **Apache** on port **5001** as the application backend.
- Deployed **Nginx** on port **8097** as a **Reverse Proxy**.
- Successfully proxied traffic from port 8097 to port 5001.
- Deployed custom web content to the backend.
- Verified service persistence and operational health.

**🔍 Verification Completed**:
- **stbkp01**: Both services active | Proxy chain functional.
- **Content**: Expected "Welcome" message retrieved via Nginx port.

**🔒 Configuration Summary**:
- **Frontend**: Nginx (8097)
- **Backend**: Apache (5001)
- **Location**: stbkp01
- **Method**: Reverse Proxy
