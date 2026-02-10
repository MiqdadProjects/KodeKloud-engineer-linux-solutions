# 🔐 Task 10 - Install and Enable Squid Proxy Server on All App Servers

**📌 Task Description**

As per details shared by the **Nautilus project development team**, the new application release has specific backend dependencies. Several packages and services must be installed across all application servers in the **Stratos Datacenter**. This task specifically requires the installation and automation of the **Squid** service. Squid is a widely-used proxy and caching server that helps manage web traffic and improve performance.

**👉 Task Requirements**:
- **Target Servers**: All App Servers (stapp01, stapp02, stapp03) in Stratos Datacenter
- **Package**: `squid`
- **Operation Objectives**:
  - Install Squid package on all application servers
  - Ensure Squid is enabled to start automatically on system boot
  - Verify service status and enablement
- Use `yum` package manager for installation
- Enable service persistence using `systemctl`
- Ensure consistency across the entire application tier

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to App Server 1

```bash
ssh tony@stapp01
```

**Purpose**: Establish SSH connection to App Server 1 for package deployment.

**Expected Output**:
```
tony@stapp01's password: 
Welcome to App Server 1 - Stratos Datacenter
[tony@stapp01 ~]$
```

**Success Indicators**:
- ✅ Successfully authenticated as user `tony`
- ✅ Command prompt shows `stapp01` hostname
- ✅ Ready for administrative tasks

---

## 🔹 Step 2: Install Squid Package on App Server 1

```bash
sudo yum install -y squid
```

**Purpose**: Download and install the Squid package and its required dependencies.

**Command Breakdown**:
- `sudo`: Execute with root privileges
- `yum`: Package manager (RHEL/CentOS)
- `install -y`: Install specified package and automatically answer yes to prompts
- `squid`: The proxy server package name

**Expected Output** (sample):
```
Installed:
  squid-4.15-3.el8.x86_64
Complete!
```

**Success Indicators**:
- ✅ Package installed without errors
- ✅ Dependencies resolved
- ✅ "Complete!" message appeared

---

## 🔹 Step 3: Enable Squid Service on App Server 1

```bash
sudo systemctl enable squid
```

**Purpose**: Configure Squid to start automatically during the server boot process.

**Command Breakdown**:
- `sudo`: Administrative execution
- `systemctl`: System and service manager
- `enable`: Configure service for auto-start
- `squid`: Target service name

**Expected Output**:
```
Created symlink /etc/systemd/system/multi-user.target.wants/squid.service → /usr/lib/systemd/system/squid.service.
```

**Success Indicators**:
- ✅ Symlink created in systemd warrants directory
- ✅ Persistent configuration established

---

## 🔹 Step 4: Verify Enablement and Start Squid (Optional)

```bash
systemctl is-enabled squid
sudo systemctl start squid
sudo systemctl status squid
```

**Purpose**: Confirm the service is enabled and verify its runtime status.

**Expected Output**:
```
enabled
● squid.service - Squid caching proxy
   Loaded: loaded (/usr/lib/systemd/system/squid.service; enabled; vendor preset: disabled)
   Active: active (running) since Tue 2026-02-10 10:00:00 EST; 5s ago
```

**Success Indicators**:
- ✅ `is-enabled` returns `enabled`
- ✅ Status shows `active (running)`

---

## 🔹 Step 5: Connect to App Server 2

```bash
ssh steve@stapp02
```

**Purpose**: Establish SSH connection to App Server 2.

**Expected Output**:
```
steve@stapp02's password: 
Welcome to App Server 2 - Stratos Datacenter
[steve@stapp02 ~]$
```

---

## 🔹 Step 6: Install and Enable Squid on App Server 2

```bash
sudo yum install -y squid
sudo systemctl enable squid
```

**Purpose**: Replicate installation and enablement on App Server 2.

**Success Indicators**:
- ✅ Package `squid` installed
- ✅ Service enabled for boot-start

---

## 🔹 Step 7: Connect to App Server 3

```bash
ssh banner@stapp03
```

**Purpose**: Establish SSH connection to App Server 3.

**Expected Output**:
```
banner@stapp03's password: 
Welcome to App Server 3 - Stratos Datacenter
[banner@stapp03 ~]$
```

---

## 🔹 Step 8: Install and Enable Squid on App Server 3

```bash
sudo yum install -y squid
sudo systemctl enable squid
```

**Purpose**: Complete the rollout on App Server 3.

**Success Indicators**:
- ✅ Package `squid` installed
- ✅ Service enabled for boot-start
- ✅ Task verified on all application servers

---

## 📋 Quick Command Reference

Execute these commands on **each App Server** to fulfill the task:

```bash
# Connect to stapp01, stapp02, or stapp03
# ssh <user>@<server>

# Install Squid package
sudo yum install -y squid

# Enable service for boot
sudo systemctl enable squid

# Verify enablement
systemctl is-enabled squid

# (Optional) Start and check status
sudo systemctl start squid
sudo systemctl status squid
```

---

## 💡 Common Squid Installation Issues & Fixes

### **Issue 1: Repository Metadata Errors**
**Problem**: Yum fails to find the Squid package due to corrupted cache.
**Fix**: Clean yum cache and rebuild.
```bash
sudo yum clean all
sudo yum makecache
```

### **Issue 2: Dependency Conflicts**
**Problem**: Existing packages conflict with Squid dependencies.
**Fix**: Update system before installing.
```bash
sudo yum update -y
sudo yum install -y squid
```

### **Issue 3: Service Fails to Enable**
**Problem**: Unit file not found or corrupted.
**Fix**: Reload systemd daemon and check package integrity.
```bash
sudo systemctl daemon-reload
rpm -V squid
```

### **Issue 4: Permission Denied**
**Problem**: Attempting to install without sudo privileges.
**Fix**: Ensure you are using `sudo` with an authorized account (tony, steve, or banner).

---

## 🔧 Troubleshooting Failures

### **Error: No package squid available**
**Symptoms**: `Error: Unable to find a match: squid`.
**Solution**: Check enabled repositories and internet connectivity.
```bash
sudo yum repolist
ping -c 3 google.com
```

### **Error: Failed to enable unit: File exists**
**Symptoms**: Systemd error when enabling.
**Solution**: Force re-enablement or remove existing symlinks manually.
```bash
sudo systemctl reenable squid
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The secondary requirement of ensuring the service starts during boot is a critical step for application availability. Simply installing the package is insufficient; the `systemctl enable` command is vital for persistence after server reboots or maintenance.

**💡 Solution Approach**:
1. **Automated Installation**: Used `-y` flag with `yum install` for non-interactive setup.
2. **Service Orchestration**: Used `systemctl enable` to hook the service into correct target units for boot persistence.
3. **Multi-Server Execution**: Followed a consistent workflow across stapp01, stapp02, and stapp03 to maintain environment parity.
4. **Post-Task Verification**: Used `systemctl is-enabled` to confirm the persistence flag was successfully set.

**🎯 Key Success Factors**:
- **Consistency**: Using the same sequence on all servers avoids drift.
- **Persistence**: `enable` vs `start` - prioritizing boot-start configuration.
- **Verification**: Explicitly checking the enablement status.

**⚠️ Critical Fixes**:
- Ensure `yum` is used with `sudo` for administrative access.
- Always run `systemctl enable` after installation to satisfy requirement (b).
- Verify on all servers to ensure 100% compliance.

---

## ⚠️ Important Production Notes

🔧 **Configuration Path**: Central config is located at `/etc/squid/squid.conf`.
🔐 **Default Security**: By default, Squid usually allows local access but may need ACL configuration for production use.
📊 **Logging**: Access logs are typically found at `/var/log/squid/access.log`.
🛡️ **Firewall**: Ensure port `3128/tcp` is open if Squid is serving external clients.
⚙️ **Performance**: Consider tuning caching parameters in `squid.conf` for optimized traffic handling.

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Successfully installed the `squid` package on all three application servers.
- Enabled the `squid` service to start persistent across reboots via `systemctl enable`.
- Verified installation and enablement status across the Stratos Datacenter infrastructure.

**🔍 Verification Completed**:
- **App Server 1 (stapp01)**: Package installed | Service enabled.
- **App Server 2 (stapp02)**: Package installed | Service enabled.
- **App Server 3 (stapp03)**: Package installed | Service enabled.

**🔒 Configuration Summary**:
- **Package**: squid
- **Service Status**: Enabled (Boot-start active)
- **Deployment Tier**: All App Servers (stapp01, stapp02, stapp03)
- **Tooling**: Yum / Systemctl
