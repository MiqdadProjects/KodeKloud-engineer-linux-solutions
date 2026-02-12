# 🔐 Task 17 - Troubleshoot HAProxy Service Failure on stlb01

**📌 Task Description**

The monitoring tools at **xFusionCorp Industries** have detected a critical issue with the **HAProxy** service on the **LBR server (stlb01)** in the **Stratos Datacenter**. This failure has disrupted traffic distribution to the application backend, causing the static website to become unreachable. This task involves performing a diagnostic check on the HAProxy configuration, identifying the root cause of the service failure, and implementing a fix to restore load balancing functionality.

**👉 Task Requirements**:
- **Target Server**: LBR Server (`stlb01`)
- **Service**: `haproxy`
- **Operation Objectives**:
  - Investigate the HAProxy service status
  - Perform configuration syntax validation
  - Identify and fix structural or typographical errors in `haproxy.cfg`
  - Restart and enable the service
  - Verify website accessibility via the load balancer
- Ensure the service is persistent across reboots
- Validate the fix through the "StaticApp" interface

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface. Please note that the root cause of the service failure may vary depending on your specific task; the following solution addresses a common configuration typo encountered during this troubleshooting session.

---

## 🔹 Step 1: Connect to Load Balancer Server

```bash
ssh loki@stlb01
```

**Purpose**: Establish an administrative SSH session to the affected load balancer server.

**Expected Output**:
```
loki@stlb01's password: 
Welcome to LBR Server - Stratos Datacenter
[loki@stlb01 ~]$
```

**Success Indicators**:
- ✅ Successfully authenticated as user `loki`
- ✅ Switched to root using `sudo su -`

---

## 🔹 Step 2: Investigation - Check HAProxy Status

```bash
systemctl status haproxy
```

**Purpose**: Confirm the current operational state of the HAProxy service.

**Expected Output** (initial):
```
● haproxy.service - HAProxy Load Balancer
   Loaded: loaded (/usr/lib/systemd/system/haproxy.service; enabled; vendor preset: disabled)
   Active: failed (Result: exit-code) since Wed 2026-02-11 19:20:00 EST; 2min ago
 Main PID: 450 (code=exited, status=1/FAILURE)
```

**Success Indicators**:
- ✅ Service failure confirmed (status `failed`)

---

## 🔹 Step 3: Investigation - Validate Configuration Syntax

```bash
haproxy -c -f /etc/haproxy/haproxy.cfg
```

**Purpose**: Use HAProxy's built-in validation tool to find errors in the configuration file.

**Critical Error Found**:
```text
[ALERT] 041/140241 (461) : parsing [/etc/haproxy/haproxy.cfg:7] : cannot find user id for 'haprox' (0:Success)
[ALERT] 041/140241 (461) : Error(s) found in configuration file : /etc/haproxy/haproxy.cfg
[ALERT] 041/140241 (461) : Fatal errors found in configuration.
```

**Root Cause Analysis**:
The error `cannot find user id for 'haprox'` indicates a typographical error on line 7 of the configuration file. The service is trying to drop privileges to a user named `haprox`, which does not exist on the system. The correct system user is `haproxy`.

---

## 🔹 Step 4: Fix - Correct Configuration Typo

```bash
sudo vi /etc/haproxy/haproxy.cfg
```

**Purpose**: Edit the configuration file to correct the invalid user reference.

**Changes Implemented**:
Locate the `global` section (usually near the top) and update the `user` parameter:

**Change from**:
```haproxy
    user haprox
```

**Change to**:
```haproxy
    user haproxy
```

**Success Indicators**:
- ✅ Typo corrected in `haproxy.cfg`
- ✅ Configuration saved successfully

---

## 🔹 Step 5: Re-validate Configuration

```bash
sudo haproxy -c -f /etc/haproxy/haproxy.cfg
```

**Purpose**: Ensure that the typo was the only remaining error preventing the service from starting.

**Expected Output**:
```
Configuration file is valid
```

**Success Indicators**:
- ✅ Syntax validation passed
- ✅ No further fatal errors detected

---

## 🔹 Step 6: Service Restoration

```bash
sudo systemctl restart haproxy
sudo systemctl enable haproxy
```

**Purpose**: Restart the service to apply the fix and ensure it starts automatically on boot.

**Success Indicators**:
- ✅ Service restarted without errors
- ✅ Systemd reported successful activation

---

## 🔹 Step 7: Final Verification

```bash
systemctl status haproxy
```

**Expected Output**:
```
● haproxy.service - HAProxy Load Balancer
   Loaded: loaded (/usr/lib/systemd/system/haproxy.service; enabled; vendor preset: disabled)
   Active: active (running) since Wed 2026-02-11 19:25:00 EST; 10s ago
```

**Verification Steps**:
1. Confirm the status is `active (running)`.
2. Click the **StaticApp** button in the top bar of the lab interface.
3. Verify the message: `Welcome to xFusionCorp Industries!`

**Success Indicators**:
- ✅ Service is active and stable
- ✅ Website content is accessible and properly balanced

---

## 📋 Quick Command Reference

Use these diagnostic and recovery commands for HAProxy issues:

```bash
# Diagnostic validation
haproxy -c -f /etc/haproxy/haproxy.cfg

# Check logs for detailed failure info
journalctl -u haproxy -n 50 --no-pager

# One-liner fix for the specific typo (if applicable)
sudo sed -i 's/user haprox/user haproxy/g' /etc/haproxy/haproxy.cfg

# Restart and confirm
sudo systemctl restart haproxy && systemctl is-active haproxy
```

---

## 💡 Common HAProxy Troubleshooting Scenarios

### **Issue 1: Group ID Missing**
**Problem**: Similar to the user typo, the `group` parameter might have a typo (e.g., `group haprox`).
**Fix**: Ensure `group haproxy` is used in the `global` section.

### **Issue 2: Binding Port Conflicts**
**Problem**: HAProxy cannot start because port 80 is occupied.
**Fix**: Check for other web servers and stop them.
```bash
sudo ss -tulpn | grep :80
```

### **Issue 3: Permission Denied on Logs/Stats**
**Problem**: HAProxy cannot write to the stats socket.
**Fix**: Ensure the directory `/var/lib/haproxy` exists and has proper permissions.

---

## 🔧 Troubleshooting Failures

### **Error: [ALERT] ... : Starting frontend http_front: cannot bind socket [*:80]**
**Symptoms**: Service fails to bind to the external port.
**Solution**: Verify that no other process is using port 80 and that you are running as root.

### **Error: 503 Service Unavailable (Browsing)**
**Symptoms**: Service is running, but browser shows an error.
**Solution**: Check the `backend` section IPs and ports. Ensure app servers are reachable from the LBR.

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The challenge was identifying a small typographical error within a large configuration file. A missing 'y' in the username `haprox` caused a fatal error during the process privilege drop phase, which is not always immediately obvious without using the configuration validation tool.

**💡 Solution Approach**:
1. **Tool-First Diagnosis**: Instead of guessing, we used `haproxy -c` to get the exact line number and error description.
2. **Standard Alignment**: Aligned the configuration with standard Linux system accounts (`haproxy`).
3. **Loop Closure**: Verified both the service health and the end-user experience (website accessibility).

---

## ⚠️ Important Production Notes

🔧 **Validation Checks**: Always run `haproxy -c` before restarting the service to prevent downtime.
🔐 **Privilege Drop**: HAProxy starts as root but should always drop to a non-privileged user (`haproxy`) for security.
📊 **Logging**: Ensure `log 127.0.0.1 local2` is configured to capture traffic and error metrics in syslog.
🛡️ **Config Backups**: Keep copies of known-working configurations (`haproxy.cfg.bak`).

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Diagnosed HAProxy service failure on **stlb01**.
- Identified and corrected a typographical error in the `global` section of `haproxy.cfg`.
- Successfully restored the **HAProxy** service and verified its status.
- Validated high-level connectivity to the static website.

**🔍 Verification Completed**:
- **stlb01**: `systemctl status` confirmed as **ACTIVE**.
- **User Experience**: "StaticApp" content verified as reachable.

**🔒 Configuration Summary**:
- **Fixed Line**: user haproxy
- **Service Status**: Active/Enabled
- **Validation tool**: haproxy -c
