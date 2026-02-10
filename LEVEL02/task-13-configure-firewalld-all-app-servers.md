# 🔐 Task 13 - Configure Firewalld on All App Servers

**📌 Task Description**

To secure the **Nautilus infrastructure** in the **Stratos Datacenter**, the DevOps team has decided to implement host-based firewalls using `firewalld` on all application servers. These servers run both **Apache (httpd)** and **Nginx** services, with Nginx acting as a reverse proxy for Apache. The goal is to strictly control traffic flow by allowing public access to the Nginx entry point while blocking direct access to the Apache backend port.

**👉 Task Requirements**:
- **Target Servers**: All App Servers (stapp01, stapp02, stapp03) in Stratos Datacenter
- **Services involved**:
  - **Nginx**: Running on port `80` (Allow Access)
  - **Apache (httpd)**: Running on port `8080` (Block Access)
- **Firewall Settings**:
  - Zone: `public`
  - Persistence: `--permanent`
- **Operation Objectives**:
  - Install and start `firewalld`
  - Ensure Apache and Nginx services are running and enabled
  - Open port `80` in the public zone
  - Ensure port `8080` is restricted/removed from the public zone
  - Reload firewall to apply permanent changes
- Maintain system-wide security consistency
- Verify rules against running services

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to App Servers

```bash
# Connect to stapp01, stapp02, or stapp03 sequentially
ssh tony@stapp01
ssh steve@stapp02
ssh banner@stapp03
```

**Purpose**: Establish SSH connection to each application server to perform security hardening.

**Expected Output**:
```
tony@stapp01's password: 
Welcome to App Server 1 - Stratos Datacenter
[tony@stapp01 ~]$
```

---

## 🔹 Step 2: Install and Start Firewalld

```bash
sudo yum install -y firewalld
sudo systemctl enable firewalld
sudo systemctl start firewalld
```

**Purpose**: Install the firewall daemon and configure it to persist across reboots.

**Command Breakdown**:
- `yum install -y firewalld`: Installs the service package.
- `systemctl enable`: Sets the service to start automatically at boot.
- `systemctl start`: Initiates the service immediately.

**Success Indicators**:
- ✅ `firewalld` package installed.
- ✅ Service status is `active (running)`.

---

## 🔹 Step 3: Ensure Apache and Nginx are Running

```bash
# For Apache
sudo systemctl enable httpd
sudo systemctl start httpd

# For Nginx
sudo systemctl enable nginx
sudo systemctl start nginx
```

**Purpose**: Fulfill the requirement to ensure both web server services are active and set to auto-start.

**Success Indicators**:
- ✅ Both `httpd` and `nginx` show `active (running)`.
- ✅ Services will persist after a system restart.

---

## 🔹 Step 4: Configure Port Rules in Public Zone

```bash
# Allow Nginx port (80)
sudo firewall-cmd --permanent --zone=public --add-port=80/tcp

# Explicitly ensure Apache port (8080) is not allowed
# (Note: Remove if already exists, or don't add if it's not there)
sudo firewall-cmd --permanent --zone=public --remove-port=8080/tcp
```

**Purpose**: Define the traffic policy for the web servers. Port 80 is required for public traffic, while port 8080 is an internal backend port that should be shielded.

**Command Breakdown**:
- `--permanent`: Ensures the rule is written to disk and survives a reload/reboot.
- `--zone=public`: Targets the default public facing zone.
- `--add-port=80/tcp`: Opens the Nginx port.
- `--remove-port=8080/tcp`: Ensures the Apache port is closed to public traffic.

**Success Indicators**:
- ✅ Commands return `success`.

---

## 🔹 Step 5: Reload and Verify Firewall Configuration

```bash
sudo firewall-cmd --reload
sudo firewall-cmd --zone=public --list-ports
```

**Purpose**: Apply the permanent changes to the runtime environment and verify the active rule set.

**Expected Output**:
```
80/tcp
```

**Success Indicators**:
- ✅ Output shows `80/tcp` only.
- ✅ `8080/tcp` is absent from the public port list.

---

## 📋 Quick Command Reference

Run this block on **each app server** for a rapid rollout:

```bash
# Installation & Service Prep
sudo yum install -y firewalld
sudo systemctl enable --now firewalld httpd nginx

# Firewall Configuration
sudo firewall-cmd --permanent --zone=public --add-port=80/tcp
sudo firewall-cmd --permanent --zone=public --remove-port=8080/tcp
sudo firewall-cmd --reload

# Verification
sudo firewall-cmd --zone=public --list-ports
```

---

## 💡 Common Firewalld Issues & Fixes

### **Issue 1: SSH Lockdown**
**Problem**: After starting firewalld, you lose SSH access.
**Fix**: Firewalld's default public zone usually allows the `ssh` service, but if it doesn't, ensure it's added.
```bash
sudo firewall-cmd --permanent --add-service=ssh
sudo firewall-cmd --reload
```

### **Issue 2: Rules Not Applying**
**Problem**: You ran `--permanent` commands but `list-ports` doesn't show them.
**Fix**: You MUST run `--reload` to move permanent configuration into runtime.

### **Issue 3: Port Conflict**
**Problem**: Apache or Nginx fails to start because the port is already in use.
**Fix**: Check for existing processes.
```bash
sudo ss -tulpn | grep :80
```

---

## 🔧 Troubleshooting Failures

### **Error: FirewallD is not running**
**Symptoms**: `firewall-cmd` returns an error about the daemon.
**Solution**: Check service status and start it.
```bash
sudo systemctl status firewalld
sudo systemctl start firewalld
```

### **Error: NOT_ENABLED: 8080:tcp**
**Symptoms**: `remove-port` returns an error.
**Solution**: This usually means the port wasn't open in the first place, which satisfies the "block" requirement. Verify with `list-ports`.

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The challenge here is maintaining service availability for Nginx while ensuring Apache remains private. Because firewalld follows a "deny by default" approach for ports not specified in a zone, ensuring 8080 is *removed* or *not added* is the primary goal.

**💡 Solution Approach**:
1. **Service Sync**: Verified that `httpd` and `nginx` are enabled *before* configuring the firewall to prevent start-up issues.
2. **Zone Targeting**: Explicitly called `--zone=public` to avoid accidental configuration of the wrong interface.
3. **Double Verification**: Switched between runtime and permanent checks to ensure the security posture is consistent.

---

## ⚠️ Important Production Notes

🔧 **Service Tags**: In production, it's often better to use `--add-service=http` instead of ports for standard services.
🔐 **Ingress Control**: Blocking port 8080 prevents external users from bypassing Nginx's security/logic layer.
📊 **Logging**: You can enable logging for dropped packets via `firewall-cmd --set-log-denied=all`.
⚙️ **Rich Rules**: For more complex scenarios (like allowing 8080 only from specific IPs), "Rich Rules" should be used.

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Installed and activated `firewalld` across all application servers.
- Ensured web services (**Apache** and **Nginx**) are running and enabled.
- Configured public zone to allow port `80` (Nginx).
- Explicitly blocked/removed port `8080` (Apache) from public access.
- Finalized rules as **permanent** configurations.

**🔍 Verification Completed**:
- **stapp01/02/03**: `firewall-cmd --list-ports` confirms `80/tcp` is open and `8080/tcp` is closed.
- **Service Audit**: `systemctl status` confirms all relevant daemons are healthy.

**🔒 Configuration Summary**:
- **Servers**: stapp01, stapp02, stapp03
- **Opened**: 80/tcp
- **Blocked**: 8080/tcp
- **Status**: Permanent & Applied
