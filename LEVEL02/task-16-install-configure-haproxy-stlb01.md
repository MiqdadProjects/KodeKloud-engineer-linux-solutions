# 🔐 Task 16 - Install and Configure HAProxy Load Balancer on stlb01

**📌 Task Description**

A static website is running within the **Stratos Datacenter** infrastructure. While the application servers are active and the code is deployed, a Load Balancer (LBR) is required to distribute incoming traffic efficiently. The team has selected **HAProxy** for this role. The application servers run Apache on a non-standard port (**6200**). This task involves installing HAProxy on the LBR server, configuring it to balance traffic across three backend servers, and ensuring it serves traffic on the default HTTP port.

**👉 Task Requirements**:
- **Target Server**: LBR Server (`stlb01`)
- **Load Balancer**: HAProxy (Installed via `yum`)
- **Backend Configuration**:
  - All three app servers must be added as backends.
  - Backend Port: `6200` (Apache)
- **Frontend Configuration**:
  - Listening Port: `80` (Default HTTP)
- **Special Requirement**: Do not remove the `stats socket /var/lib/haproxy/stats` entry from the default configuration.
- **Operation Objectives**:
  - Install HAProxy package
  - Modify `haproxy.cfg` with frontend and backend definitions
  - Validate configuration syntax
  - Enable and start the HAProxy service
- Ensure high availability and round-robin balancing
- Maintain default stats socket configuration

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to Load Balancer Server

```bash
ssh loki@stlb01
```

**Purpose**: Establish an administrative SSH session to the designated load balancer server.

**Expected Output**:
```
loki@stlb01's password: 
Welcome to LBR Server - Stratos Datacenter
[loki@stlb01 ~]$
```

**Success Indicators**:
- ✅ Successfully authenticated as user `loki`
- ✅ Switched to root using `sudo -i`

---

## 🔹 Step 2: Install HAProxy Package

```bash
sudo yum install -y haproxy
```

**Purpose**: Install the HAProxy load balancer software from the official repositories.

**Command Breakdown**:
- `yum install`: Red Hat package management installation
- `-y`: Non-interactive mode
- `haproxy`: The load balancer package name

**Success Indicators**:
- ✅ Package installed without errors
- ✅ Configuration directory `/etc/haproxy/` created

---

## 🔹 Step 3: Backup Default Configuration

```bash
sudo cp /etc/haproxy/haproxy.cfg /etc/haproxy/haproxy.cfg.bak
```

**Purpose**: Create a recovery point before making structural changes to the configuration.

**Success Indicators**:
- ✅ Backup file `haproxy.cfg.bak` exists

---

## 🔹 Step 4: Configure HAProxy Frontend and Backend

```bash
sudo vi /etc/haproxy/haproxy.cfg
```

**Purpose**: Define how HAProxy receives traffic (Frontend) and where it sends it (Backend).

**Configuration to Append**:
Navigate to the bottom of the file while ensuring the `global` section still contains the `stats socket /var/lib/haproxy/stats` entry.

```haproxy
# Frontend Definition
frontend http_front
    bind *:80
    mode http
    default_backend http_back

# Backend Definition
backend http_back
    mode http
    balance roundrobin
    server app1 172.16.238.10:6200 check
    server app2 172.16.238.11:6200 check
    server app3 172.16.238.12:6200 check
```

**Parameter Breakdown**:
- `bind *:80`: Listen on all interfaces on port 80.
- `balance roundrobin`: Distribute traffic equally across servers.
- `server ... check`: Enables health checking for the backend servers.
- `172.16.238.x:6200`: Target IP and port of the application servers.

**Success Indicators**:
- ✅ Configuration file updated with correct IP/Port mappings
- ✅ Existing `stats socket` entry preserved in the `global` block

---

## 🔹 Step 5: Validate Configuration Syntax

```bash
sudo haproxy -c -f /etc/haproxy/haproxy.cfg
```

**Purpose**: Ensure the configuration is syntactically correct before attempting to start the service.

**Expected Output**:
```
Configuration file is valid
```

**Success Indicators**:
- ✅ Syntax validation passed
- ✅ Ready for service activation

---

## 🔹 Step 6: Enable and Start HAProxy Service

```bash
sudo systemctl enable haproxy
sudo systemctl start haproxy
```

**Purpose**: Activate the load balancer and ensure it starts automatically on boot.

**Success Indicators**:
- ✅ Service status shows `active (running)`
- ✅ HAProxy listening on port `80`

---

## 🔹 Step 7: Final Verification

```bash
sudo systemctl status haproxy
```

**Verification Steps**:
1. Confirm the service is healthy.
2. Click the **StaticApp** button in the KodeKloud lab interface.
3. Verify that the webpage displays: `Welcome to xFusionCorp Industries!`

**Success Indicators**:
- ✅ Service is active
- ✅ Website content is accessible through the load balancer

---

## 📋 Quick Command Reference

Use these commands for a rapid HAProxy deployment:

```bash
# Installation
sudo yum install -y haproxy

# Config Patch (Append)
cat <<EOF | sudo tee -a /etc/haproxy/haproxy.cfg

frontend http_front
    bind *:80
    mode http
    default_backend http_back

backend http_back
    mode http
    balance roundrobin
    server app1 172.16.238.10:6200 check
    server app2 172.16.238.11:6200 check
    server app3 172.16.238.12:6200 check
EOF

# Validation & Rollout
sudo haproxy -c -f /etc/haproxy/haproxy.cfg
sudo systemctl enable --now haproxy
```

---

## 💡 Common HAProxy Issues & Fixes

### **Issue 1: Port 80 Already Bound**
**Problem**: Another service (like Apache or Nginx) is already using port 80 on the LBR server.
**Fix**: Identify and stop the service.
```bash
sudo ss -tulpn | grep :80
sudo systemctl stop <service_name>
```

### **Issue 2: Layer 7 HTTP Error (503 Service Unavailable)**
**Problem**: HAProxy is running but cannot reach the backends.
**Fix**: Verify backends are listening on port 6200 and check connectivity.
```bash
# On LBR, test connectivity to app1
curl -I http://172.16.238.10:6200
```

### **Issue 3: Health Checks Failing**
**Problem**: Backends are marked as "DOWN".
**Fix**: Ensure the `check` keyword is configured correctly and the app servers respond to simple GET requests.

---

## 🔧 Troubleshooting Failures

### **Error: [ALERT] ... : Starting frontend http_front: cannot bind socket [*:80]**
**Symptoms**: HAProxy fails to start.
**Solution**: Check for other processes using port 80 or ensure you are running as root/using sudo.

### **Error: Configuration file is NOT valid**
**Symptoms**: Validation command fails.
**Solution**: Carefully check indentation in `haproxy.cfg`. HAProxy is sensitive to spacing and the structure of blocks.

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The non-standard port (`6200`) on the backend app servers requires precise mapping in the `backend` configuration block. If the ports are mismatched, the load balancer will be unable to retrieve content, resulting in a gateway error.

**💡 Solution Approach**:
1. **IP Discovery**: Identified the specific management IPs for stapp01, stapp02, and stapp03.
2. **Health Monitoring**: Included the `check` parameter to ensure HAProxy only sends traffic to healthy instances.
3. **Preservation of Defaults**: Carefully appended the new frontend/backend blocks without interfering with the existing `global` and `defaults` sections, specifically keeping the `stats socket` intact as requested.

---

## ⚠️ Important Production Notes

🔧 **Persistence**: For applications requiring user sessions, consider using `sticky sessions` (cookie-based persistence).
🔐 **Security**: In production, terminate SSL/TLS at the load balancer (Port 443) and proxy to backends via HTTP.
📊 **Stats Page**: You can enable the HAProxy stats UI by adding a `stats enable` block to provide real-time metrics.
🛡️ **Firewall**: Ensure port 80 is open in the local firewall of `stlb01`.

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Successfully installed **HAProxy** on the LBR server.
- Configured three backend application servers running on port **6200**.
- Configured HAProxy to serve on the default **HTTP port 80**.
- Preserved mandatory `stats socket` entries in the configuration.
- Verified service stability and accessibility of the static website.

**🔍 Verification Completed**:
- **stlb01**: HAProxy service verified as **ACTIVE**.
- **User Experience**: "StaticApp" content verified as reachable.

**🔒 Configuration Summary**:
- **Listening Port**: 80
- **Balancing Method**: Roundrobin
- **Backend Count**: 3 servers
- **Backend Port**: 6200
