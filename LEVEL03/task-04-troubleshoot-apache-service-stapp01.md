# 🔐 Task 04 - Troubleshoot Apache Service on App Server 1

**📌 Task Description**

Our monitoring systems have flagged a critical issue with the **Apache (httpd)** service on **App Server 1 (stapp01)** in the **Stratos Datacenter**. The service is reported as unreachable on its configured port (**3002**). This disruption impacts the availability of web resources on the Nautilus infrastructure. This task involves performing a multi-layered diagnostic check using network and process-level tools to identify the root cause—be it a service failure, a port conflict, or a firewall restriction—and implementing the necessary fixes to restore connectivity.

**👉 Task Requirements**:
- **Target Server**: App Server 1 (`stapp01`)
- **Service**: Apache (`httpd`)
- **Configured Port**: `3002`
- **Operation Objectives**:
  - Audit connectivity from the Jump Host using `telnet` and `curl`.
  - Investigate the service status and port usage on the target server.
  - Identify and resolve resource conflicts (e.g., zombie processes on port 3002).
  - Verify and update firewall rules (`iptables`) to permit inbound traffic.
  - Restore the Apache service to a healthy state.
- Ensure security rules are maintained (do not compromise the overall firewall policy).
- **Prohibition**: Do not modify the existing `index.html` file.

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Initial Investigation (Jump Host)

```bash
# Test connectivity to all app servers on port 3002
telnet stapp01 3002
telnet stapp02 3002
telnet stapp03 3002
```

**Purpose**: Confirm the scope of the issue and verify if port 3002 is open on any other nodes.

**Initial Finding**:
- `stapp01`: `Connection refused` (Connectivity confirmed broken).
- `stapp02/stapp03`: Might respond or show no service depending on their specific tasks.

---

## 🔹 Step 2: Investigation - Service and Port Audit (stapp01)

Connect to **stapp01** and elevate privileges:
```bash
ssh tony@stapp01
sudo su -
```

### **A. Check Service Status**
```bash
systemctl status httpd
```
*Purpose: Check if the httpd process is active.*

### **B. Check Port Listeners**
```bash
netstat -lntp | grep 3002
```
*Purpose: Identify if port 3002 is bound by a different process.*

**Findings**:
A different process ID (e.g., PID `447`) is found holding port 3002, preventing Apache from binding.

---

## 🔹 Step 3: Fix - Resolve Port Conflict

```bash
# Terminate the conflicting process
sudo kill 447

# Verify port 3002 is now free
netstat -lntp | grep 3002
```

**Purpose**: Clear the listener conflict so Apache can successfully initialize on its designated port.

---

## 🔹 Step 4: Service Restoration

```bash
sudo systemctl restart httpd
sudo systemctl status httpd
```

**Purpose**: Restart Apache and confirm it successfully binds to port 3002.

**Expected Status**: `active (running)`.

---

## 🔹 Step 5: Fix - Adjust Firewall Rules

Even with the service running, external connectivity might be blocked by local firewall rules.

```bash
# List current iptables rules
iptables -L -n

# Insert a rule at the top of the INPUT chain to accept traffic on 3002
sudo iptables -I INPUT 1 -p tcp --dport 3002 -j ACCEPT

# Verify the rule insertion
iptables -L -n
```

**Purpose**: Explicitly permit inbound TCP traffic on port 3002 to satisfy security and accessibility requirements.

---

## 🔹 Step 6: Final Verification (Jump Host)

```bash
# Test connection with telnet
telnet stapp01 3002

# Retrieve content with curl
curl http://stapp01:3002
```

**Expected Output**:
```html
Welcome to xFusionCorp Industries!
```

**Success Indicators**:
- ✅ Telnet connection successful.
- ✅ `curl` returns the original website content.
- ✅ No "Connection refused" or "No route to host" errors.

---

## 📋 Quick Command Reference

Repair sequence for **stapp01**:

```bash
# 1. Clear Conflicts
sudo kill $(sudo netstat -lntp | grep 3002 | awk '{print $7}' | cut -d/ -f1)

# 2. Reset Service
sudo systemctl restart httpd

# 3. Patch Firewall
sudo iptables -I INPUT 1 -p tcp --dport 3002 -j ACCEPT

# 4. Persistence (Optional but recommended)
sudo iptables-save | sudo tee /etc/sysconfig/iptables
```

---

## 💡 Common Troubleshooting Pitfalls

### **Issue 1: Multiple Conflicting Processes**
**Problem**: After killing one process, another one spawns or binds to the port.
**Fix**: Check if a watchdog or supervisor service is running the conflicting process.

### **Issue 2: Firewall Reject vs Drop**
**Problem**: Existing rules might have a `REJECT-WITH-ICMP-HOST-PROHIBITED` at the bottom.
**Fix**: Always use `-I INPUT 1` to place the `ACCEPT` rule at the very top of the chain.

### **Issue 3: SELinux Interference**
**Problem**: Apache fails to start after freeing the port because of security contexts.
**Fix**: Ensure `semanage port` allows http_port_t on 3002.

---

## 🔧 Troubleshooting Failures

### **Error: telnet: connect to address ...: Connection refused**
**Symptoms**: No process is listening on the port.
**Solution**: Check `systemctl status httpd` and `/var/log/httpd/error_log` for binder failures.

### **Error: telnet: connect to address ...: No route to host**
**Symptoms**: The service is running, but the firewall is blocking access.
**Solution**: Re-insert the `iptables -I` rule and verify other tiers (e.g. AWS Security Groups or Corporate Firewalls) aren't blocking.

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The challenge was the "Hidden Blockage"—the service failing because of a silent port conflict followed by an external blockade from the OS-level firewall. 

**💡 Solution Approach**:
1. **Network Layer Separation**: Used `telnet` to differentiate between "No Listener" (Refused) and "Firewall Block" (Timeout/Host Prohibited).
2. **Aggressive Reclamation**: Identified the specific PID holding the socket using `netstat -p` and removed it immediately.
3. **Hierarchy-Aware Firewalling**: Used `iptables -I` instead of `-A` to override existing global drop rules that might exist in default Nautilus images.

---

## ⚠️ Important Production Notes

🔧 **Persistence**: Ensure that the `iptables` rule is saved using `service iptables save` or equivalent.
🔐 **Security**: Only open port 3002; do not disable the entire firewall to fix a single-port issue.
📊 **Logs**: Monitor `/var/log/secure` for unauthorized connection attempts to non-standard ports.
🛡️ **Audit**: Use `lsof -i :3002` regularly to ensure only `httpd` is the owner of the socket.

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Diagnosed unreachable Apache service on **stapp01**.
- Cleared a port conflict involving PID **447**.
- Successfully restored **Apache** service on port **3002**.
- Patched **iptables** to allow external traffic to the web service.
- Verified end-to-end connectivity from the Jump Host.

**🔍 Verification Completed**:
- **stapp01**: Service active | Port 3002 open | Firewall rule patched.
- **Connectivity**: `curl` test successful.

**🔒 Configuration Summary**:
- **Port**: 3002
- **Firewall Chain**: INPUT (Position 1)
- **Protocol**: TCP
- **Status**: Recovered
