# 🔐 Task 14 - Set Default Runlevel to Graphical on All App Servers

**📌 Task Description**

With the installation of new tools on the app servers within the **Stratos Datacenter**, certain functionalities now necessitate graphical user interface (GUI) access. In modern Linux systems using systemd, runlevels have been replaced by **targets**, with `graphical.target` providing full multi-user system with GUI support. This task requires changing the default boot target from `multi-user.target` (text mode) to `graphical.target` (GUI mode) across all app servers.

**👉 Task Requirements**:
- **Target Servers**: All App Servers (stapp01, stapp02, stapp03) in Stratos Datacenter
- **Current Default**: `multi-user.target` (runlevel 3 - text mode)
- **New Default**: `graphical.target` (runlevel 5 - GUI mode)
- **Constraint**: Do NOT reboot servers after configuration
- **Operation Objectives**:
  - Change default systemd target to graphical
  - Apply configuration on all three app servers
  - Verify target change on each server
  - Ensure configuration persists across reboots
- Use `systemctl set-default` to change boot target
- Verify configuration with `systemctl get-default`
- Maintain server uptime (no reboot required)

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to App Server 1

```bash
ssh tony@stapp01
```

**Purpose**: Establish SSH connection to App Server 1 for runlevel configuration.

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

## 🔹 Step 2: Check Current Default Target on App Server 1

```bash
systemctl get-default
```

**Purpose**: Verify the current default systemd target before making changes.

**Expected Output**:
```
multi-user.target
```

**Success Indicators**:
- ✅ Current default is `multi-user.target` (text mode)
- ✅ Ready to change to graphical target

---

## 🔹 Step 3: Set Default Target to Graphical on App Server 1

```bash
sudo systemctl set-default graphical.target
```

**Purpose**: Change the default boot target to graphical mode.

**Command Breakdown**:
- `sudo`: Execute with administrative privileges
- `systemctl`: Systemd control command
- `set-default`: Set default boot target
- `graphical.target`: Target for GUI mode (equivalent to runlevel 5)

**Expected Output**:
```
Removed /etc/systemd/system/default.target.
Created symlink /etc/systemd/system/default.target → /usr/lib/systemd/system/graphical.target.
```

**Success Indicators**:
- ✅ Symlink created to graphical.target
- ✅ Old default.target removed
- ✅ Configuration changed successfully

**⚠️ Important**: This creates a symlink at `/etc/systemd/system/default.target` pointing to `graphical.target`.

---

## 🔹 Step 4: Verify Default Target Change on App Server 1

```bash
systemctl get-default
```

**Purpose**: Confirm the default target has been changed to graphical.

**Expected Output**:
```
graphical.target
```

**Success Indicators**:
- ✅ Default target is now `graphical.target`
- ✅ Server will boot to GUI mode on next restart
- ✅ Configuration successful on App Server 1

---

## 🔹 Step 5: Connect to App Server 2

```bash
ssh steve@stapp02
```

**Purpose**: Establish SSH connection to App Server 2 for runlevel configuration.

**Expected Output**:
```
steve@stapp02's password: 
Welcome to App Server 2 - Stratos Datacenter
[steve@stapp02 ~]$
```

**Success Indicators**:
- ✅ Successfully authenticated as user `steve`
- ✅ Command prompt shows `stapp02` hostname
- ✅ Ready to execute administrative commands

---

## 🔹 Step 6: Check Current Default Target on App Server 2

```bash
systemctl get-default
```

**Purpose**: Verify the current default systemd target before making changes.

**Expected Output**:
```
multi-user.target
```

**Success Indicators**:
- ✅ Current default is `multi-user.target`
- ✅ Ready to change to graphical target

---

## 🔹 Step 7: Set Default Target to Graphical on App Server 2

```bash
sudo systemctl set-default graphical.target
```

**Purpose**: Change the default boot target to graphical mode.

**Expected Output**:
```
Removed /etc/systemd/system/default.target.
Created symlink /etc/systemd/system/default.target → /usr/lib/systemd/system/graphical.target.
```

**Success Indicators**:
- ✅ Symlink created to graphical.target
- ✅ Configuration changed successfully

---

## 🔹 Step 8: Verify Default Target Change on App Server 2

```bash
systemctl get-default
```

**Purpose**: Confirm the default target has been changed to graphical.

**Expected Output**:
```
graphical.target
```

**Success Indicators**:
- ✅ Default target is now `graphical.target`
- ✅ Configuration successful on App Server 2

---

## 🔹 Step 9: Connect to App Server 3

```bash
ssh banner@stapp03
```

**Purpose**: Establish SSH connection to App Server 3 for runlevel configuration.

**Expected Output**:
```
banner@stapp03's password: 
Welcome to App Server 3 - Stratos Datacenter
[banner@stapp03 ~]$
```

**Success Indicators**:
- ✅ Successfully authenticated as user `banner`
- ✅ Command prompt shows `stapp03` hostname
- ✅ Ready to execute administrative commands

---

## 🔹 Step 10: Check Current Default Target on App Server 3

```bash
systemctl get-default
```

**Purpose**: Verify the current default systemd target before making changes.

**Expected Output**:
```
multi-user.target
```

**Success Indicators**:
- ✅ Current default is `multi-user.target`
- ✅ Ready to change to graphical target

---

## 🔹 Step 11: Set Default Target to Graphical on App Server 3

```bash
sudo systemctl set-default graphical.target
```

**Purpose**: Change the default boot target to graphical mode.

**Expected Output**:
```
Removed /etc/systemd/system/default.target.
Created symlink /etc/systemd/system/default.target → /usr/lib/systemd/system/graphical.target.
```

**Success Indicators**:
- ✅ Symlink created to graphical.target
- ✅ Configuration changed successfully

---

## 🔹 Step 12: Verify Default Target Change on App Server 3

```bash
systemctl get-default
```

**Purpose**: Confirm the default target has been changed to graphical.

**Expected Output**:
```
graphical.target
```

**Success Indicators**:
- ✅ Default target is now `graphical.target`
- ✅ Configuration successful on App Server 3
- ✅ All three app servers configured

---

## 🔹 Step 13: Verify Symlink on All Servers (Optional)

**On each server**:
```bash
ls -l /etc/systemd/system/default.target
```

**Purpose**: Verify the symlink points to graphical.target.

**Expected Output**:
```
lrwxrwxrwx 1 root root 40 Feb 08 15:00 /etc/systemd/system/default.target -> /usr/lib/systemd/system/graphical.target
```

**Success Indicators**:
- ✅ Symlink exists
- ✅ Points to graphical.target
- ✅ Configuration persisted

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **each App Server**:

```bash
# App Server 1
ssh tony@stapp01

# Check current default
systemctl get-default

# Set default to graphical
sudo systemctl set-default graphical.target

# Verify change
systemctl get-default

# Verify symlink (optional)
ls -l /etc/systemd/system/default.target

# Exit to jump host
exit

# App Server 2
ssh steve@stapp02

# Check current default
systemctl get-default

# Set default to graphical
sudo systemctl set-default graphical.target

# Verify change
systemctl get-default

# Exit to jump host
exit

# App Server 3
ssh banner@stapp03

# Check current default
systemctl get-default

# Set default to graphical
sudo systemctl set-default graphical.target

# Verify change
systemctl get-default

# Exit to jump host
exit
```

---

## 💡 Common Systemd Target Issues & Fixes

### **Issue 1: Permission Denied**
**Problem**: Cannot set default target without sudo.
**Fix**: Always use sudo for systemctl set-default.
```bash
# WRONG - Will fail
systemctl set-default graphical.target

# CORRECT - Use sudo
sudo systemctl set-default graphical.target
```

### **Issue 2: Target Not Found**
**Problem**: graphical.target doesn't exist.
**Fix**: Install desktop environment packages.
```bash
# Check if target exists
systemctl list-units --type=target | grep graphical

# Install GNOME desktop (RHEL/CentOS)
sudo yum groupinstall "GNOME Desktop" -y

# Install GNOME desktop (Debian/Ubuntu)
sudo apt install ubuntu-desktop -y
```

### **Issue 3: Change Not Persisting**
**Problem**: Default target reverts after configuration.
**Fix**: Verify symlink was created correctly.
```bash
# Check symlink
ls -l /etc/systemd/system/default.target

# Recreate if needed
sudo systemctl set-default graphical.target

# Verify
systemctl get-default
```

### **Issue 4: Want to Revert to Text Mode**
**Problem**: Need to change back to multi-user target.
**Fix**: Set default to multi-user.target.
```bash
# Set default to text mode
sudo systemctl set-default multi-user.target

# Verify
systemctl get-default
```

### **Issue 5: Current Session Not Graphical**
**Problem**: Want GUI immediately without reboot.
**Fix**: Switch to graphical target in current session.
```bash
# Switch to graphical target now (requires GUI packages)
sudo systemctl isolate graphical.target

# Or start graphical session
sudo systemctl start graphical.target
```

---

## 🔧 Troubleshooting Failures

### **Error: Failed to Set Default Target**
**Symptoms**: `Failed to set default target: File exists`.
**Solution**: Remove existing symlink and recreate.
```bash
# Remove existing symlink
sudo rm /etc/systemd/system/default.target

# Set default again
sudo systemctl set-default graphical.target

# Verify
systemctl get-default
```

### **Error: Target Does Not Exist**
**Symptoms**: `Failed to set default target: No such file or directory`.
**Solution**: Verify graphical.target exists on system.
```bash
# Check if target exists
ls -l /usr/lib/systemd/system/graphical.target

# List available targets
systemctl list-units --type=target --all

# If missing, install desktop environment
sudo yum groupinstall "Server with GUI" -y
```

### **Error: Insufficient Permissions**
**Symptoms**: `Failed to set default target: Permission denied`.
**Solution**: Use sudo for administrative operations.
```bash
# Always use sudo
sudo systemctl set-default graphical.target

# Verify you have sudo access
sudo -l
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The primary challenge was changing the default systemd boot target from `multi-user.target` (text mode) to `graphical.target` (GUI mode) across all three app servers without rebooting. Understanding systemd targets and the `systemctl set-default` command is crucial for managing system boot behavior.

**💡 Solution Approach**:
1. **Current State Verification**: Checked current default target on each server.
2. **Target Change**: Used `systemctl set-default graphical.target` on all servers.
3. **Verification**: Confirmed change with `systemctl get-default`.
4. **Multi-Server Execution**: Repeated process on all three app servers.
5. **No Reboot**: Configuration persists without requiring immediate restart.

**🎯 Key Success Factors**:
- **Correct Command**: Used `systemctl set-default` for persistent change.
- **Proper Target**: Specified `graphical.target` for GUI mode.
- **Sudo Usage**: Applied sudo for administrative operations.
- **Verification**: Confirmed change on each server.
- **Multi-Server Consistency**: Applied identical configuration across all servers.

**⚠️ Critical Fixes**:
- Always use sudo for `systemctl set-default` command.
- Verify change with `systemctl get-default` after configuration.
- Apply configuration to all specified servers.
- Do not reboot servers after configuration (change persists).
- Understand that change takes effect on next boot, not immediately.

**🔒 Linux Systemd Target Best Practices Applied**:
- **Persistent Configuration**: Used `set-default` for permanent change.
- **Verification**: Confirmed configuration with `get-default`.
- **No Disruption**: Changed default without affecting current session.
- **Consistency**: Applied identical configuration across all servers.
- **Documentation**: Clear understanding of targets vs runlevels.

**⚠️ Important Systemd Target Concepts**:
- **Targets vs Runlevels**: Systemd uses targets instead of traditional runlevels.
- **multi-user.target**: Equivalent to runlevel 3 (text mode, multi-user).
- **graphical.target**: Equivalent to runlevel 5 (GUI mode, multi-user).
- **Symlink Method**: `set-default` creates symlink at `/etc/systemd/system/default.target`.
- **Persistence**: Change persists across reboots without immediate effect.

---

## ⚠️ Important Production Notes

🔧 **Runlevels vs Targets**: Modern systemd uses targets instead of traditional runlevels.
🔐 **No Immediate Effect**: Change takes effect on next boot, not current session.
📊 **GUI Requirements**: Ensure desktop environment packages are installed.
🛡️ **Resource Usage**: Graphical target uses more resources than multi-user.
🗄️ **Verification**: Always verify change with `systemctl get-default`.
📝 **Documentation**: Document runlevel/target changes in change logs.
🔍 **Monitoring**: Monitor resource usage after switching to graphical mode.
⚙️ **Automation**: Use configuration management for consistent target settings.

**Systemd Target Reference**:
```bash
# Common systemd targets (runlevel equivalents)
poweroff.target     # Runlevel 0 - Shutdown
rescue.target       # Runlevel 1 - Single user mode
multi-user.target   # Runlevel 3 - Multi-user, text mode
graphical.target    # Runlevel 5 - Multi-user, GUI mode
reboot.target       # Runlevel 6 - Reboot

# List all targets
systemctl list-units --type=target --all

# Show target dependencies
systemctl list-dependencies graphical.target
```

**Systemctl Target Commands**:
```bash
# Get current default target
systemctl get-default

# Set default target (persistent)
sudo systemctl set-default graphical.target
sudo systemctl set-default multi-user.target

# Switch to target immediately (current session)
sudo systemctl isolate graphical.target
sudo systemctl isolate multi-user.target

# Check current active targets
systemctl list-units --type=target

# View target details
systemctl cat graphical.target
systemctl show graphical.target
```

**Target Switching Without Reboot**:
```bash
# Switch to graphical mode immediately
sudo systemctl isolate graphical.target

# Switch to text mode immediately
sudo systemctl isolate multi-user.target

# Start graphical session
sudo systemctl start graphical.target

# Stop graphical session
sudo systemctl stop graphical.target
```

**Security Recommendations**:
- Only enable GUI on servers that require it
- Monitor resource usage with graphical target
- Implement access controls for GUI sessions
- Use VNC or remote desktop for secure GUI access
- Disable GUI on production servers when not needed
- Regular security updates for desktop environment packages
- Audit GUI access and user sessions
- Consider using multi-user.target for headless servers

**Resource Considerations**:
```bash
# Check memory usage
free -h

# Monitor resource usage
top
htop

# Check running services
systemctl list-units --type=service --state=running

# Graphical target typically requires:
# - 2GB+ RAM minimum
# - Desktop environment packages
# - X server or Wayland
# - Display manager (gdm, lightdm, etc.)
```

**Desktop Environment Installation**:
```bash
# RHEL/CentOS - GNOME
sudo yum groupinstall "GNOME Desktop" -y
sudo yum groupinstall "Server with GUI" -y

# RHEL/CentOS - KDE
sudo yum groupinstall "KDE Plasma Workspaces" -y

# Debian/Ubuntu - GNOME
sudo apt install ubuntu-desktop -y

# Debian/Ubuntu - KDE
sudo apt install kubuntu-desktop -y

# Verify installation
systemctl list-units --type=target | grep graphical
```

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Changed default runlevel to graphical on all three app servers
- App Server 1: Default target set to `graphical.target`
- App Server 2: Default target set to `graphical.target`
- App Server 3: Default target set to `graphical.target`
- Verified configuration on each server
- No server reboots performed (as required)

**🔍 Verification Completed**:
- **App Server 1 (stapp01)**:
  - Before: `multi-user.target`
  - After: `graphical.target`
  - Symlink verified
- **App Server 2 (stapp02)**:
  - Before: `multi-user.target`
  - After: `graphical.target`
  - Symlink verified
- **App Server 3 (stapp03)**:
  - Before: `multi-user.target`
  - After: `graphical.target`
  - Symlink verified

**🚀 Next Steps** (Optional):
- Install desktop environment packages if not present
- Configure remote desktop access (VNC/RDP)
- Test GUI applications after next reboot
- Monitor resource usage with graphical target
- Document GUI access procedures
- Train team on GUI tool usage

**🔒 Configuration Validation**:
- ✅ All three servers configured identically
- ✅ Default target changed from multi-user to graphical
- ✅ Symlinks created at `/etc/systemd/system/default.target`
- ✅ Configuration persists across reboots
- ✅ No immediate reboot required
- ✅ Servers will boot to GUI mode on next restart

**📊 Configuration Summary**:
- **Servers Configured**: 3 (stapp01, stapp02, stapp03)
- **Previous Default**: `multi-user.target` (text mode)
- **New Default**: `graphical.target` (GUI mode)
- **Method**: `systemctl set-default graphical.target`
- **Persistence**: Permanent (via symlink)
- **Immediate Effect**: None (takes effect on next boot)

**🔍 Key Learning Points**:
- Systemd uses targets instead of traditional runlevels
- `systemctl set-default` creates persistent configuration
- `graphical.target` is equivalent to runlevel 5 (GUI)
- `multi-user.target` is equivalent to runlevel 3 (text)
- Configuration persists via symlink at `/etc/systemd/system/default.target`
- Change takes effect on next boot, not immediately
