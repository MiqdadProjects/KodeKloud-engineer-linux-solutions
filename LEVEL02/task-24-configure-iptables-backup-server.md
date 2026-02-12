# 🔐 Task 24 - Configure Iptables Firewall on Backup Server

**📌 Task Description**

The **Nautilus Backup Server (stbkp01)** hosts a critical backup management application UI. The architecture utilizes **Apache** as the application server (listening on port **5000**) and **Nginx** as a high-performance reverse proxy (listening on port **8098**). To secure this environment, the DevOps team requires a hardened firewall configuration using **iptables**. The objective is to ensure that external traffic only accesses the application through the Nginx proxy, while direct access to the Apache backend is strictly prohibited. These rules must be made permanent to survive system reboots.

**👉 Task Requirements**:
- **Target Server**: Nautilus Backup Server (`stlb01`)
- **Firewall Tool**: `iptables`
- **Proxy Port**: `8098` (Allow all incoming connections)
- **Backend Port**: `5000` (Block all incoming connections)
- **Persistence**: Rules must be saved and set to load automatically on boot.
- **Operation Objectives**:
  - Implement an ALLOW rule for the Nginx proxy port.
  - Implement a REJECT/DROP rule for the Apache backend port.
  - Install `iptables-services` if required for persistence.
  - Save the current firewall state to the system configuration.
  - Enable and restart the `iptables` service.
- Verify the rule hierarchy (Allow before Deny if using general blocks).
- Validate the configuration via `iptables -L`.

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to Backup Server

```bash
ssh clint@stbkp01
```

**Purpose**: Establish an administrative SSH session to the backup server.

**Expected Output**:
```
clint@stbkp01's password: 
Welcome to Nautilus Backup Server
[clint@stbkp01 ~]$
```

**Success Indicators**:
- ✅ Successfully authenticated as user `clint`
- ✅ Switched to root using `sudo -i` or `sudo su -`

---

## 🔹 Step 2: Configure Iptables Rules

### **A. Allow Nginx Proxy Traffic**
```bash
sudo iptables -A INPUT -p tcp --dport 8098 -j ACCEPT
```
*Purpose: Opens the reverse proxy port to external traffic.*

### **B. Block Apache Backend Traffic**
```bash
sudo iptables -A INPUT -p tcp --dport 5000 -j REJECT
```
*Purpose: Explicitly rejects traffic attempting to bypass the proxy and hit the backend directly.*

---

## 🔹 Step 3: Verify Active Rules

```bash
sudo iptables -L -n -v
```

**Purpose**: Inspect the current in-memory firewall chain to ensure the rules are applied in the correct order.

**Success Indicators**:
- ✅ Rule for `dport 8098` shows `ACCEPT`.
- ✅ Rule for `dport 5000` shows `REJECT`.

---

## 🔹 Step 4: Implement Persistence

### **A. Install Iptables Services**
```bash
sudo yum install -y iptables-services
```
*Purpose: Provides the necessary infrastructure to manage iptables as a standard systemd service.*

### **B. Save Rules to Configuration**
```bash
sudo service iptables save
```
*Or alternatively:*
```bash
sudo iptables-save | sudo tee /etc/sysconfig/iptables
```

### **C. Enable Service**
```bash
sudo systemctl enable iptables
sudo systemctl start iptables
```

**Success Indicators**:
- ✅ `iptables-services` package installed.
- ✅ Rules written to `/etc/sysconfig/iptables`.
- ✅ Service status is `active (exited)` (Standard for iptables).

---

## 🔹 Step 5: Final Validation

```bash
sudo systemctl status iptables
sudo iptables -S
```

**Purpose**: Confirm the service is active and the rules are correctly saved in the persistent configuration file.

---

## 📋 Quick Command Reference

Firewall rollout for **stbkp01**:

```bash
# Apply Rules
sudo iptables -A INPUT -p tcp --dport 8098 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 5000 -j REJECT

# Persist (CentOS/RHEL)
sudo yum install -y iptables-services
sudo service iptables save
sudo systemctl enable --now iptables

# Verify
sudo iptables -L -n
```

---

## 💡 Common Iptables Issues & Fixes

### **Issue 1: Rules Disappear After Reboot**
**Problem**: Rules were only applied in-memory and not saved.
**Fix**: Ensure `iptables-services` is installed and `service iptables save` is executed.

### **Issue 2: Locked Out of SSH**
**Problem**: A general `REJECT` or `DROP` rule was applied before allowing port 22.
**Fix**: Always ensure SSH (Port 22) is allowed at the top of the chain.
```bash
sudo iptables -I INPUT 1 -p tcp --dport 22 -j ACCEPT
```

### **Issue 3: Conflicting Firewalld**
**Problem**: Both `firewalld` and `iptables` are running, causing rule collisions.
**Fix**: Disable `firewalld` if the requirement is to specifically use `iptables`.
```bash
sudo systemctl stop firewalld && sudo systemctl disable firewalld
```

---

## 🔧 Troubleshooting Failures

### **Error: service: command not found**
**Symptoms**: `service iptables save` fails.
**Solution**: Use the binary directly: `iptables-save > /etc/sysconfig/iptables`.

### **Error: iptables: No chain/target/match by that name**
**Symptoms**: Rule insertion fails.
**Solution**: Check for typos in the protocol (`-p tcp`) or target (`-j ACCEPT/REJECT`).

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The use of `iptables` on modern RHEL/CentOS systems requires careful coordination with `systemd`. By default, these systems often use `firewalld`. Switching to `iptables` requires installing the `iptables-services` package to provide the `save` functionality that developers expect.

**💡 Solution Approach**:
1. **Targeted Rules**: Used the `-A` (Append) flag to add rules to the end of the input chain, assuming standard default behaviors were already established.
2. **Explicit Rejection**: Chose `REJECT` over `DROP` for the internal port 5000. `REJECT` sends an ICMP unreachable message back, which is often better for internal application debugging than `DROP` (which causes a timeout).
3. **Persistence Stack**: Deployed the full `iptables-services` stack to ensure that the security posture remains consistent even after a server maintenance window or reboot.

---

## ⚠️ Important Production Notes

🔧 **Order Matters**: `iptables` processes rules from top to bottom. If you have a "Reject All" rule at the top, none of your specific "Accept" rules will work.
🔐 **Least Privilege**: Only open port 8098 to the networks that actually need access to the UI.
📊 **Logging**: You can add a `LOG` target to see who is trying to hit port 5000 directly.
🛡️ **Audit**: Use `nmap` or `curl` from another server to verify that 8098 is open and 5000 is closed.

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Successfully configured **iptables** on the backup server.
- Opened public access to the **Nginx** proxy on port **8098**.
- Restricted access to the **Apache** backend on port **5000**.
- Installed and configured **iptables-services** for permanent storage of firewall rules.
- Verified service persistence and rule validation.

**🔍 Verification Completed**:
- **stbkp01**: `iptables -L` confirmed ports 8098 (Allow) and 5000 (Reject).
- **Persistence**: Checked `/etc/sysconfig/iptables` for saved state.

**🔒 Configuration Summary**:
- **Accept**: 8098/TCP
- **Reject**: 5000/TCP
- **Strategy**: Iptables Persistence
- **Server**: stbkp01
