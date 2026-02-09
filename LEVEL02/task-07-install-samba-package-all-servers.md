# 🔐 Task 7 - Install Samba Package on All App Servers

**📌 Task Description**

As per new application requirements shared by the **Nautilus project development team**, several new packages need to be installed on all app servers in Stratos Datacenter. Most of them are completed except for **Samba**. Samba is a free software re-implementation of the SMB networking protocol that provides file and print services for various operating systems. This task involves installing and enabling the Samba service across all application servers.

**👉 Task Requirements**:
- **Target Servers**: All App Servers (stapp01, stapp02, stapp03) in Stratos Datacenter
- **Package**: `samba`
- **Operation Objectives**:
  - Install samba package on all app servers
  - Enable SMB and NMB services for automatic startup
  - Verify package installation
  - Optionally start and verify services
- Use `dnf` package manager for installation
- Enable services to start on boot
- Ensure consistency across all servers

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to App Server 1

```bash
ssh tony@stapp01
```

**Purpose**: Establish SSH connection to App Server 1 for package installation.

**Expected Output**:
```
tony@stapp01's password: 
Welcome to App Server 1 - Stratos Datacenter
[tony@stapp01 ~]$
```

**Success Indicators**:
- ✅ Successfully authenticated as user `tony`
- ✅ Command prompt shows `stapp01` hostname
- ✅ Ready to execute administrative commands

---

## 🔹 Step 2: Install Samba Package on App Server 1

```bash
sudo dnf install -y samba
```

**Purpose**: Install the Samba package and its dependencies.

**Command Breakdown**:
- `sudo`: Execute with administrative privileges
- `dnf`: Package manager (Fedora/RHEL/CentOS)
- `install -y`: Install package with automatic yes to prompts
- `samba`: Package name

**Expected Output** (sample):
```
Last metadata expiration check: 0:05:23 ago on Sun 09 Feb 2026 07:00:00 PM EST.
Dependencies resolved.
================================================================================
 Package           Arch       Version              Repository           Size
================================================================================
Installing:
 samba             x86_64     4.14.5-7.el8         baseos              850 k
Installing dependencies:
 samba-common      noarch     4.14.5-7.el8         baseos              220 k
 samba-libs        x86_64     4.14.5-7.el8         baseos              180 k

Transaction Summary
================================================================================
Install  3 Packages

Total download size: 1.2 M
Installed size: 4.5 M
Downloading Packages:
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Installing       : samba-common-4.14.5-7.el8.noarch                     1/3
  Installing       : samba-libs-4.14.5-7.el8.x86_64                       2/3
  Installing       : samba-4.14.5-7.el8.x86_64                            3/3
  Verifying        : samba-4.14.5-7.el8.x86_64                            1/3
  Verifying        : samba-common-4.14.5-7.el8.noarch                     2/3
  Verifying        : samba-libs-4.14.5-7.el8.x86_64                       3/3

Installed:
  samba-4.14.5-7.el8.x86_64
  samba-common-4.14.5-7.el8.noarch
  samba-libs-4.14.5-7.el8.x86_64

Complete!
```

**Success Indicators**:
- ✅ Package installed successfully
- ✅ Dependencies resolved
- ✅ No error messages
- ✅ "Complete!" message displayed

---

## 🔹 Step 3: Verify Samba Installation on App Server 1

```bash
rpm -q samba
```

**Purpose**: Confirm the Samba package is installed.

**Command Breakdown**:
- `rpm`: RPM package manager query tool
- `-q`: Query if package is installed
- `samba`: Package name

**Expected Output**:
```
samba-4.14.5-7.el8.x86_64
```

**Success Indicators**:
- ✅ Package version displayed
- ✅ Installation confirmed

---

## 🔹 Step 4: Enable SMB and NMB Services on App Server 1

```bash
sudo systemctl enable smb nmb
```

**Purpose**: Enable Samba services to start automatically on system boot.

**Command Breakdown**:
- `sudo`: Execute with administrative privileges
- `systemctl enable`: Enable service(s) for automatic startup
- `smb`: Samba SMB daemon (file sharing)
- `nmb`: Samba NMB daemon (NetBIOS name service)

**Expected Output**:
```
Created symlink /etc/systemd/system/multi-user.target.wants/smb.service → /usr/lib/systemd/system/smb.service.
Created symlink /etc/systemd/system/multi-user.target.wants/nmb.service → /usr/lib/systemd/system/nmb.service.
```

**Success Indicators**:
- ✅ Symlinks created for both services
- ✅ Services will start on boot
- ✅ No error messages

---

## 🔹 Step 5: Start SMB and NMB Services (Optional)

```bash
sudo systemctl start smb nmb
```

**Purpose**: Start the Samba services immediately (optional, not required by task).

**Expected Output**:
```
[no output indicates success]
```

**Success Indicators**:
- ✅ Services started
- ✅ No error messages

---

## 🔹 Step 6: Verify Service Status (Optional)

```bash
sudo systemctl status smb nmb
```

**Purpose**: Check if Samba services are running.

**Expected Output** (sample):
```
● smb.service - Samba SMB Daemon
   Loaded: loaded (/usr/lib/systemd/system/smb.service; enabled; vendor preset: disabled)
   Active: active (running) since Sun 2026-02-09 19:05:00 EST; 1min ago
     Docs: man:smbd(8)
 Main PID: 12345 (smbd)
   Status: "smbd: ready to serve connections..."

● nmb.service - Samba NMB Daemon
   Loaded: loaded (/usr/lib/systemd/system/nmb.service; enabled; vendor preset: disabled)
   Active: active (running) since Sun 2026-02-09 19:05:00 EST; 1min ago
     Docs: man:nmbd(8)
 Main PID: 12346 (nmbd)
```

**Success Indicators**:
- ✅ Both services show `active (running)`
- ✅ Services are `enabled`
- ✅ Configuration complete for App Server 1

---

## 🔹 Step 7: Connect to App Server 2

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

## 🔹 Step 8: Install and Enable Samba on App Server 2

```bash
sudo dnf install -y samba
rpm -q samba
sudo systemctl enable smb nmb
```

**Purpose**: Install Samba and enable services on App Server 2.

**Success Indicators**:
- ✅ Package installed
- ✅ Installation verified
- ✅ Services enabled

---

## 🔹 Step 9: Connect to App Server 3

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

## 🔹 Step 10: Install and Enable Samba on App Server 3

```bash
sudo dnf install -y samba
rpm -q samba
sudo systemctl enable smb nmb
```

**Purpose**: Install Samba and enable services on App Server 3.

**Success Indicators**:
- ✅ Package installed
- ✅ Installation verified
- ✅ Services enabled
- ✅ All three servers configured

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **each App Server**:

```bash
# App Server 1
ssh tony@stapp01

# Install Samba
sudo dnf install -y samba

# Verify installation
rpm -q samba

# Enable services
sudo systemctl enable smb nmb

# Optional: Start services
sudo systemctl start smb nmb

# Optional: Check status
sudo systemctl status smb nmb

# Exit
exit

# App Server 2
ssh steve@stapp02

# Install and enable
sudo dnf install -y samba
rpm -q samba
sudo systemctl enable smb nmb

# Exit
exit

# App Server 3
ssh banner@stapp03

# Install and enable
sudo dnf install -y samba
rpm -q samba
sudo systemctl enable smb nmb

# Exit
exit
```

---

## 💡 Common Package Installation Issues & Fixes

### **Issue 1: Package Not Found**
**Problem**: dnf cannot find samba package.
**Fix**: Update package cache.
```bash
# Update metadata
sudo dnf clean all
sudo dnf makecache

# Retry installation
sudo dnf install -y samba
```

### **Issue 2: Dependency Conflicts**
**Problem**: Package dependencies cannot be resolved.
**Fix**: Update system or resolve conflicts.
```bash
# Update system
sudo dnf update -y

# Install with dependency resolution
sudo dnf install -y samba --allowerasing
```

### **Issue 3: Service Enable Failed**
**Problem**: Cannot enable smb or nmb services.
**Fix**: Verify services exist and systemd is running.
```bash
# List available samba services
systemctl list-unit-files | grep -E 'smb|nmb'

# Enable individually
sudo systemctl enable smb
sudo systemctl enable nmb
```

### **Issue 4: Service Start Failed**
**Problem**: Services won't start.
**Fix**: Check logs and configuration.
```bash
# Check service status
sudo systemctl status smb nmb

# View logs
sudo journalctl -u smb -n 50
sudo journalctl -u nmb -n 50

# Check configuration
sudo testparm
```

### **Issue 5: Already Installed**
**Problem**: Package already installed but different version.
**Fix**: Reinstall or update package.
```bash
# Reinstall
sudo dnf reinstall samba

# Update to latest
sudo dnf update samba
```

---

## 🔧 Troubleshooting Failures

### **Error: Insufficient Permissions**
**Symptoms**: Permission denied during installation.
**Solution**: Use sudo for administrative operations.
```bash
# Always use sudo
sudo dnf install -y samba
```

### **Error: Network Unreachable**
**Symptoms**: Cannot download packages.
**Solution**: Check network connectivity and repository configuration.
```bash
# Test connectivity
ping -c 3 8.8.8.8

# Check repositories
sudo dnf repolist

# Verify repository configuration
cat /etc/yum.repos.d/*.repo
```

### **Error: Disk Space**
**Symptoms**: No space left on device.
**Solution**: Clean up disk space.
```bash
# Check disk space
df -h

# Clean package cache
sudo dnf clean all

# Remove old kernels
sudo dnf remove $(dnf repoquery --installonly --latest-limit=-1 -q)
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The primary challenge was installing the Samba package consistently across all three app servers and ensuring the services are enabled for automatic startup. Understanding the difference between installing, enabling, and starting services is crucial for proper system configuration.

**💡 Solution Approach**:
1. **Package Installation**: Used `dnf install -y samba` on all servers.
2. **Verification**: Confirmed installation with `rpm -q samba`.
3. **Service Enable**: Enabled both smb and nmb services with `systemctl enable`.
4. **Multi-Server**: Repeated process on all three app servers.
5. **Optional Testing**: Started and verified services for immediate functionality.

**🎯 Key Success Factors**:
- **Consistent Command**: Same installation command on all servers.
- **Verification**: Used rpm -q to confirm installation.
- **Service Enable**: Ensures services start on boot.
- **Both Services**: Enabled both smb (file sharing) and nmb (name service).
- **Multi-Server**: Applied configuration to all three servers.

**⚠️ Critical Fixes**:
- Always use sudo for package installation.
- Enable both smb and nmb services, not just smb.
- Verify installation with rpm -q before proceeding.
- Apply to all specified servers (stapp01, stapp02, stapp03).
- Starting services is optional but enabling is required.

**🔒 System Administration Best Practices Applied**:
- **Consistency**: Same configuration across all servers.
- **Verification**: Confirmed each step before proceeding.
- **Service Management**: Proper use of systemctl for service control.
- **Documentation**: Clear record of installation steps.
- **Automation Ready**: Services enabled for automatic startup.

**⚠️ Important Samba Concepts**:
- **SMB Service**: Handles file and printer sharing.
- **NMB Service**: Handles NetBIOS name resolution.
- **Enable vs Start**: Enable = auto-start on boot, Start = run now.
- **Package Manager**: dnf is used on RHEL/CentOS 8+.
- **Dependencies**: Samba requires several library packages.

---

## ⚠️ Important Production Notes

🔧 **Service Dependencies**: SMB and NMB services work together for full functionality.
🔐 **Security**: Configure firewall rules if Samba will be used externally.
📊 **Configuration**: Default config at `/etc/samba/smb.conf`.
🛡️ **Firewall**: May need to allow ports 139, 445 (SMB) and 137, 138 (NMB).
🗄️ **Shares**: Configure shares in smb.conf before use.
📝 **Documentation**: Document Samba configuration and shares.
🔍 **Monitoring**: Monitor Samba logs at `/var/log/samba/`.
⚙️ **Testing**: Use `testparm` to validate configuration.

**Samba Service Reference**:
```bash
# Service management
sudo systemctl start smb nmb      # Start services
sudo systemctl stop smb nmb       # Stop services
sudo systemctl restart smb nmb    # Restart services
sudo systemctl enable smb nmb     # Enable auto-start
sudo systemctl disable smb nmb    # Disable auto-start
sudo systemctl status smb nmb     # Check status

# Configuration testing
sudo testparm                     # Test configuration
sudo testparm -s                  # Show configuration

# User management
sudo smbpasswd -a username        # Add Samba user
sudo smbpasswd -x username        # Delete Samba user
sudo pdbedit -L                   # List Samba users
```

**Firewall Configuration**:
```bash
# Allow Samba through firewall
sudo firewall-cmd --permanent --add-service=samba
sudo firewall-cmd --reload

# Or allow specific ports
sudo firewall-cmd --permanent --add-port=139/tcp
sudo firewall-cmd --permanent --add-port=445/tcp
sudo firewall-cmd --permanent --add-port=137/udp
sudo firewall-cmd --permanent --add-port=138/udp
sudo firewall-cmd --reload
```

**Security Recommendations**:
- Configure firewall to restrict Samba access
- Use strong passwords for Samba users
- Limit share access with proper permissions
- Regular security updates for Samba
- Monitor access logs for suspicious activity
- Use encrypted connections when possible
- Implement user authentication
- Regular configuration audits

**Basic Samba Configuration**:
```bash
# Edit configuration
sudo vi /etc/samba/smb.conf

# Example share configuration
[shared]
    path = /srv/samba/shared
    browseable = yes
    writable = yes
    guest ok = no
    valid users = @smbgroup

# Create share directory
sudo mkdir -p /srv/samba/shared
sudo chmod 2770 /srv/samba/shared
sudo chown root:smbgroup /srv/samba/shared

# Restart services
sudo systemctl restart smb nmb
```

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Installed Samba package on all three app servers
- Verified installation with rpm -q command
- Enabled SMB and NMB services for automatic startup
- Configuration consistent across all servers

**🔍 Verification Completed**:
- **App Server 1 (stapp01)**: Samba installed and services enabled
- **App Server 2 (stapp02)**: Samba installed and services enabled
- **App Server 3 (stapp03)**: Samba installed and services enabled

**🚀 Next Steps** (Optional):
- Configure Samba shares in /etc/samba/smb.conf
- Create Samba users with smbpasswd
- Configure firewall rules for Samba access
- Test file sharing functionality
- Document share configuration
- Implement backup strategy for Samba data

**🔒 Configuration Validation**:
- ✅ Samba package installed on all servers
- ✅ Installation verified with rpm -q
- ✅ SMB service enabled for auto-start
- ✅ NMB service enabled for auto-start
- ✅ Consistent configuration across infrastructure
- ✅ Ready for Samba configuration and use

**📊 Configuration Summary**:
- **Servers**: stapp01, stapp02, stapp03
- **Package**: samba
- **Package Manager**: dnf
- **Services Enabled**: smb, nmb
- **Auto-start**: Yes (enabled)
- **Status**: Installed and ready for configuration

**🔍 Key Learning Points**:
- `dnf` is the package manager for RHEL/CentOS 8+
- Samba requires both smb and nmb services
- `systemctl enable` configures auto-start on boot
- `rpm -q` verifies package installation
- Consistent configuration across servers is critical
- Enabling services doesn't start them immediately
