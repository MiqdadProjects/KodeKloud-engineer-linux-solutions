# 🔐 Task 08 - Troubleshoot Apache Service Unavailability on App Server 1

**📌 Task Description**

The **xFusionCorp Industries** production support team has implemented advanced monitoring across the **Stratos Datacenter** infrastructure. A critical alert has been raised regarding the unavailability of the **Apache (httpd)** service on one of the application servers. The service is expected to be operational on port **5002** across the entire app tier. This task involves identifying the specific faulty host, diagnosing the cause of the service failure—potentially a port conflict or service misconfiguration—and restoring the web service to ensure consistent monitoring and availability.

**👉 Task Requirements**:
- **Target Tier**: All Application Servers (stapp01, stapp02, stapp03)
- **Target Service**: Apache (`httpd`)
- **Required Port**: `5002`
- **Operation Objectives**:
  - Remotely audit all application hosts to identify the failed service.
  - Investigate the service state and resource usage on the faulty host.
  - Resolve any port binding conflicts (e.g., other services occupying port 5002).
  - Ensure the Apache service is started and enabled for boot persistence.
  - Verify that Apache is listening on port 5002 across all nodes.
- Maintain visibility of the service without necessarily serving web content.
- Validate restoration via `curl` and service status checks.

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Fault Identification (Jump Host)

```bash
# Audit the application tier on the mandatory port 5002
curl http://stapp01:5002
curl http://stapp02:5002
curl http://stapp03:5002
```

**Purpose**: Systematically check each server to pinpoint which one is failing to respond.

**Findings**:
- `stapp01`: `Failed to connect... Connection refused` (**Faulty Host Identified**)
- `stapp02/stapp03`: Connection successful or service responding.

---

## 🔹 Step 2: Diagnostic Phase (stapp01)

Connect to the faulty server and elevate privileges:
```bash
ssh tony@stapp01
sudo su -
```

### **A. Check Service Status**
```bash
systemctl status httpd
```
*Expected Finding: Service is in a `failed` or `inactive` state.*

### **B. Investigate Port Binding Conflict**
```bash
ss -tulnep | grep 5002
```
*Purpose: Check if port 5002 is already occupied by another process, preventing Apache from starting.*

**Critical Finding**:
The output shows that **sendmail** (PID `724`) is bound to `127.0.0.1:5002`, creating a listener conflict.

---

## 🔹 Step 3: Resolution - Clear Port Conflict

```bash
# Terminate the conflicting sendmail process
sudo kill -9 724

# Verify the port is now vacant
ss -tulnep | grep 5002
```

**Purpose**: Remove the process blocking Apache's designated port.

---

## 🔹 Step 4: Service Restoration

```bash
# Attempt to restart the HTTP service
sudo systemctl restart httpd

# Set to start automatically on boot
sudo systemctl enable httpd

# Confirm operational status
sudo systemctl status httpd
```

**Success Indicators**:
- ✅ Service status transitions to `active (running)`.
- ✅ No further errors related to port binding in logs.

---

## 🔹 Step 5: Final Multi-Tier Verification

### **A. Local Audit**
```bash
ss -tulpn | grep 5002
```
*Confirm that `httpd` is now the owner of the socket.*

### **B. Remote Validation (Jump Host)**
```bash
curl http://stapp01:5002
```

**Expected Results**:
- ✅ `stapp01` responds successfully over the network.
- ✅ Monitoring alerts cleared.

---

## 📋 Quick Command Reference

Repair sequence for **stapp01**:

```bash
# 1. Identify Occupant
LSOF_PID=$(sudo ss -tulpn | grep 5002 | awk -F'pid=' '{print $2}' | cut -d, -f1)

# 2. Evict Occupant
[[ ! -z "$LSOF_PID" ]] && sudo kill -9 $LSOF_PID

# 3. Restore Primary Service
sudo systemctl enable --now httpd

# 4. Verify
systemctl is-active httpd
```

---

## 💡 Common Port Conflict Issues & Fixes

### **Issue 1: Sendmail Default Configuration**
**Problem**: On some base images, `sendmail` or `postfix` might be configured to use non-standard ports for local delivery or monitoring, leading to conflicts with custom app ports.
**Fix**: Reconfigure or disable unneeded mail services if they are not part of the server's core function.

### **Issue 2: Apache Fails to Enable**
**Problem**: The service starts but defaults to `disabled` on reboot.
**Fix**: Always run `systemctl enable httpd` after a successful start to update the symlinks in `/etc/systemd/system/`.

### **Issue 3: Ghost Listeners**
**Problem**: `ss` shows the port as occupied even after killing the PID.
**Fix**: The socket may be in a `TIME_WAIT` state. Wait a few seconds or check for child/zombie processes.

---

## 🔧 Troubleshooting Failures

### **Error: Job for httpd.service failed because the control process exited with error code**
**Symptoms**: Apache fails to start even after clearing the port.
**Solution**: Check `/var/log/httpd/error_log` or `journalctl -u httpd`. Look for syntax errors in configuration files or SELinux denials.

### **Error: curl: (7) Failed to connect to stapp01 port 5002: No route to host**
**Symptoms**: Service is running, but network access is blocked.
**Solution**: Check `iptables -L -n` and ensure port 5002 is accepted in the firewall.

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The challenge was a "Resource Hijack"—an secondary service (`sendmail`) was misconfigured to listen on a port specifically reserved for the primary web infrastructure. This prevents service recovery until the root resource is manually reclaimed.

**💡 Solution Approach**:
1. **Scope verification**: Used `curl` from the jump host to prove the issue was host-specific rather than a global configuration error.
2. **Deep Inspection**: Used `ss -tulnep` to see not just that the port was used, but *who* used it and on what specific interface (`127.0.0.1`).
3. **Forced Reclamation**: Applied `kill -9` to ensure the conflicting process released the socket immediately without waiting for graceful shutdown, which might be hung.

---

## ⚠️ Important Production Notes

🔧 **Monitoring**: Ensure monitoring tools are configured to alert on both "Service Down" and "Port Mismatch" events.
🔐 **Security**: Check why `sendmail` was listening on that port. If it was part of a compromise, further investigation is required.
📊 **Availability**: Use a Load Balancer to mask single-node failures like this while repairs are being performed.
🛡️ **Audit**: Regularly run `netstat -plnt` as part of automated health checks to detect configuration drift early.

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Identified **stapp01** as the host with the service failure.
- Diagnosed a port binding conflict on **5002** caused by `sendmail`.
- Terminated the conflicting process and successfully restored **Apache**.
- Verified service continuity across all application servers.

**🔍 Verification Completed**:
- **stapp01**: Service active | Enabled for boot | Port 5002 functional.
- **Network**: Confirmed reachable via `curl` from the Jump Host.

**🔒 Configuration Summary**:
- **Port**: 5002
- **Fault**: Socket hijack (PID 724)
- **Status**: Operational
- **Host**: stapp01
