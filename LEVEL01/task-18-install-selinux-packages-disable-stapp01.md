# 🔐 Task 18 - Install SELinux Packages and Disable SELinux on App Server 1

**📌 Task Description**

Following a security audit, the **xFusionCorp Industries security team** has opted to enhance application and server security with SELinux. To initiate testing, SELinux packages must be installed and SELinux must be temporarily disabled on **App Server 1** in the Stratos Datacenter. SELinux (Security-Enhanced Linux) is a mandatory access control (MAC) security mechanism that provides fine-grained access control, but requires careful configuration to avoid breaking applications.

**👉 Task Requirements**:
- **Target Server**: App Server 1 (stapp01) in Stratos Datacenter
- **SELinux Packages**: Install required SELinux packages
- **SELinux Status**: Permanently disable (will be re-enabled after configuration)
- **Constraint**: No reboot required (scheduled for tonight)
- **Operation Objectives**:
  - Install SELinux policy and management tools
  - Modify `/etc/selinux/config` to disable SELinux
  - Verify configuration for post-reboot state
  - Ensure change persists after scheduled reboot
- Install selinux-policy-targeted and policycoreutils packages
- Edit SELinux configuration file
- Set SELINUX=disabled in config

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to App Server 1

```bash
ssh tony@stapp01
```

**Purpose**: Establish SSH connection to App Server 1 for SELinux configuration.

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

## 🔹 Step 2: Check Current SELinux Status (Optional)

```bash
getenforce
```

**Purpose**: Check current SELinux enforcement status.

**Expected Output** (sample):
```
Enforcing
```

**Possible Values**:
- `Enforcing`: SELinux is active and enforcing policies
- `Permissive`: SELinux is active but only logging violations
- `Disabled`: SELinux is disabled

**Success Indicators**:
- ✅ Current status displayed
- ✅ Baseline established

---

## 🔹 Step 3: Install Required SELinux Packages

```bash
sudo dnf install -y selinux-policy-targeted policycoreutils policycoreutils-python-utils
```

**Purpose**: Install SELinux policy packages and management tools.

**Command Breakdown**:
- `sudo`: Execute with administrative privileges
- `dnf`: Package manager (Fedora/RHEL/CentOS)
- `install -y`: Install packages with automatic yes to prompts
- `selinux-policy-targeted`: Targeted SELinux policy
- `policycoreutils`: SELinux policy core utilities
- `policycoreutils-python-utils`: Python utilities for SELinux management

**Expected Output** (sample):
```
Last metadata expiration check: 0:05:23 ago on Sat 08 Feb 2026 03:35:00 PM EST.
Package selinux-policy-targeted-3.14.3-80.el8.noarch is already installed.
Package policycoreutils-2.9-16.el8.x86_64 is already installed.
Dependencies resolved.
================================================================================
 Package                          Arch    Version           Repository    Size
================================================================================
Installing:
 policycoreutils-python-utils     noarch  2.9-16.el8        baseos       252 k

Transaction Summary
================================================================================
Install  1 Package

Total download size: 252 k
Installed size: 2.0 M
Downloading Packages:
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Installing       : policycoreutils-python-utils-2.9-16.el8.noarch        1/1 
  Verifying        : policycoreutils-python-utils-2.9-16.el8.noarch        1/1 

Installed:
  policycoreutils-python-utils-2.9-16.el8.noarch

Complete!
```

**Success Indicators**:
- ✅ Packages installed or already present
- ✅ No error messages
- ✅ Transaction completed successfully

**Note**: Some packages may already be installed on the system.

---

## 🔹 Step 4: Verify Package Installation

```bash
rpm -qa | grep -E 'selinux-policy|policycoreutils'
```

**Purpose**: Confirm SELinux packages are installed.

**Expected Output** (sample):
```
selinux-policy-3.14.3-80.el8.noarch
selinux-policy-targeted-3.14.3-80.el8.noarch
policycoreutils-2.9-16.el8.x86_64
policycoreutils-python-utils-2.9-16.el8.noarch
```

**Success Indicators**:
- ✅ All required packages listed
- ✅ Packages installed successfully

---

## 🔹 Step 5: Backup SELinux Configuration File

```bash
sudo cp /etc/selinux/config /etc/selinux/config.backup
```

**Purpose**: Create backup of SELinux configuration before making changes.

**Expected Output**:
```
[no output indicates success]
```

**Success Indicators**:
- ✅ Backup created
- ✅ Can restore if needed
- ✅ Safety measure in place

---

## 🔹 Step 6: View Current SELinux Configuration

```bash
cat /etc/selinux/config
```

**Purpose**: Display current SELinux configuration before modification.

**Expected Output** (sample):
```
# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
#     enforcing - SELinux security policy is enforced.
#     permissive - SELinux prints warnings instead of enforcing.
#     disabled - No SELinux policy is loaded.
SELINUX=enforcing
# SELINUXTYPE= can take one of these three values:
#     targeted - Targeted processes are protected,
#     minimum - Modification of targeted policy. Only selected processes are protected.
#     mls - Multi Level Security protection.
SELINUXTYPE=targeted
```

**Success Indicators**:
- ✅ Current configuration shows `SELINUX=enforcing`
- ✅ Ready to modify configuration

---

## 🔹 Step 7: Edit SELinux Configuration File

```bash
sudo vi /etc/selinux/config
```

**Purpose**: Edit the SELinux configuration file to disable SELinux permanently.

**Change the line**:
```
SELINUX=enforcing
```

**To**:
```
SELINUX=disabled
```

**Vi Editor Commands**:
1. Find the line with `SELINUX=enforcing`
2. Press `i` to enter insert mode
3. Change `enforcing` to `disabled`
4. Press `Esc` to exit insert mode
5. Type `:wq` and press `Enter` to save and exit

**Alternative using sed**:
```bash
sudo sed -i 's/^SELINUX=enforcing/SELINUX=disabled/' /etc/selinux/config
```

**Success Indicators**:
- ✅ File modified successfully
- ✅ Configuration saved
- ✅ SELINUX parameter changed to disabled

---

## 🔹 Step 8: Verify Configuration Change

```bash
grep SELINUX /etc/selinux/config
```

**Purpose**: Confirm the SELINUX parameter has been changed to disabled.

**Expected Output**:
```
# SELINUX= can take one of these three values:
SELINUX=disabled
# SELINUXTYPE= can take one of these three values:
```

**Success Indicators**:
- ✅ `SELINUX=disabled` is present
- ✅ No `SELINUX=enforcing` line exists
- ✅ Configuration updated correctly

---

## 🔹 Step 9: Verify Specific SELINUX Setting

```bash
grep '^SELINUX=' /etc/selinux/config
```

**Purpose**: Display only the active SELINUX configuration line (excluding comments).

**Expected Output**:
```
SELINUX=disabled
```

**Success Indicators**:
- ✅ Shows `SELINUX=disabled`
- ✅ Configuration is correct
- ✅ Will take effect after reboot

---

## 🔹 Step 10: Check Current Runtime Status (Optional)

```bash
getenforce
```

**Purpose**: Verify current runtime SELinux status (before reboot).

**Expected Output**:
```
Enforcing
```

**Success Indicators**:
- ✅ May still show `Enforcing` (normal before reboot)
- ✅ Will change to `Disabled` after scheduled reboot
- ✅ Configuration change is persistent

**⚠️ Important**: The runtime status won't change until reboot. This is expected behavior.

---

## 🔹 Step 11: Verify SELinux Status Details (Optional)

```bash
sestatus
```

**Purpose**: Display detailed SELinux status information.

**Expected Output** (sample):
```
SELinux status:                 enabled
SELinuxfs mount:                /sys/fs/selinux
SELinux root directory:         /etc/selinux
Loaded policy name:             targeted
Current mode:                   enforcing
Mode from config file:          disabled
Policy MLS status:              enabled
Policy deny_unknown status:     allowed
Memory protection checking:     actual (secure)
Max kernel policy version:      31
```

**Success Indicators**:
- ✅ `Current mode: enforcing` (runtime, before reboot)
- ✅ `Mode from config file: disabled` (will apply after reboot)
- ✅ Configuration correctly set for post-reboot

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **App Server 1**:

```bash
# Connect to App Server 1
ssh tony@stapp01

# Check current SELinux status (optional)
getenforce

# Install required SELinux packages
sudo dnf install -y selinux-policy-targeted policycoreutils policycoreutils-python-utils

# Verify package installation
rpm -qa | grep -E 'selinux-policy|policycoreutils'

# Backup configuration file
sudo cp /etc/selinux/config /etc/selinux/config.backup

# View current configuration (optional)
cat /etc/selinux/config

# Edit SELinux configuration
sudo vi /etc/selinux/config
# Change: SELINUX=enforcing
# To:     SELINUX=disabled

# Or use sed to make the change
sudo sed -i 's/^SELINUX=enforcing/SELINUX=disabled/' /etc/selinux/config

# Verify configuration change
grep SELINUX /etc/selinux/config

# Verify specific setting
grep '^SELINUX=' /etc/selinux/config

# Check current runtime status (optional)
getenforce

# Check detailed status (optional)
sestatus
```

---

## 💡 Common SELinux Configuration Issues & Fixes

### **Issue 1: SELinux Still Enforcing After Configuration**
**Problem**: SELinux still shows enforcing after editing config.
**Fix**: Configuration requires reboot to take effect.
```bash
# Check config file
grep '^SELINUX=' /etc/selinux/config

# Should show: SELINUX=disabled

# Check current runtime (before reboot)
getenforce
# May still show: Enforcing

# Reboot required for change to take effect
sudo reboot
```

### **Issue 2: Wrong SELinux Mode Set**
**Problem**: Set to permissive instead of disabled.
**Fix**: Ensure SELINUX=disabled in config file.
```bash
# Check current setting
grep '^SELINUX=' /etc/selinux/config

# Should be:
SELINUX=disabled

# Not:
SELINUX=permissive
SELINUX=enforcing

# Fix if wrong
sudo sed -i 's/^SELINUX=.*/SELINUX=disabled/' /etc/selinux/config
```

### **Issue 3: Package Installation Failed**
**Problem**: Cannot install SELinux packages.
**Fix**: Update package cache and retry.
```bash
# Update package cache
sudo dnf clean all
sudo dnf makecache

# Retry installation
sudo dnf install -y selinux-policy-targeted policycoreutils policycoreutils-python-utils

# Check for errors
sudo dnf check
```

### **Issue 4: Configuration File Not Found**
**Problem**: /etc/selinux/config doesn't exist.
**Fix**: Create the file with proper content.
```bash
# Check if file exists
ls -l /etc/selinux/config

# If missing, create it
sudo mkdir -p /etc/selinux
sudo tee /etc/selinux/config > /dev/null <<EOF
# This file controls the state of SELinux on the system.
SELINUX=disabled
SELINUXTYPE=targeted
EOF
```

### **Issue 5: Want to Re-enable SELinux**
**Problem**: Need to re-enable SELinux after testing.
**Fix**: Change config back to enforcing and reboot.
```bash
# Edit configuration
sudo sed -i 's/^SELINUX=disabled/SELINUX=enforcing/' /etc/selinux/config

# Verify change
grep '^SELINUX=' /etc/selinux/config

# Reboot to apply
sudo reboot

# After reboot, verify
getenforce
# Should show: Enforcing
```

---

## 🔧 Troubleshooting Failures

### **Error: Package Not Found**
**Symptoms**: `No match for argument: policycoreutils-python-utils`.
**Solution**: Package name may vary by distribution version.
```bash
# For older RHEL/CentOS versions
sudo yum install -y policycoreutils-python

# For newer versions
sudo dnf install -y policycoreutils-python-utils

# Search for available packages
dnf search policycoreutils
```

### **Error: Permission Denied**
**Symptoms**: Cannot edit /etc/selinux/config.
**Solution**: Use sudo for system configuration files.
```bash
# Always use sudo
sudo vi /etc/selinux/config

# Or use sed with sudo
sudo sed -i 's/^SELINUX=enforcing/SELINUX=disabled/' /etc/selinux/config
```

### **Error: SELinux Prevents Changes**
**Symptoms**: SELinux blocks configuration changes.
**Solution**: Temporarily set to permissive or use proper context.
```bash
# Set to permissive temporarily (runtime only)
sudo setenforce 0

# Make configuration changes
sudo vi /etc/selinux/config

# Verify change
grep '^SELINUX=' /etc/selinux/config
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The primary challenge was installing SELinux packages and permanently disabling SELinux without requiring an immediate reboot. Understanding the difference between runtime SELinux status and persistent configuration in `/etc/selinux/config` is crucial for proper SELinux management.

**💡 Solution Approach**:
1. **Package Installation**: Installed required SELinux packages using dnf.
2. **Configuration Backup**: Created backup of config file before modifications.
3. **Configuration Edit**: Changed `SELINUX=enforcing` to `SELINUX=disabled` in `/etc/selinux/config`.
4. **Verification**: Confirmed configuration change with grep.
5. **Runtime vs Persistent**: Understood that runtime status remains until reboot.

**🎯 Key Success Factors**:
- **Correct Packages**: Installed selinux-policy-targeted and policycoreutils packages.
- **Persistent Configuration**: Modified `/etc/selinux/config` for permanent change.
- **Proper Syntax**: Used `SELINUX=disabled` (not `selinux` or other variations).
- **Verification**: Confirmed change with grep command.
- **Understanding**: Recognized runtime status won't change until reboot.

**⚠️ Critical Fixes**:
- Always use sudo to edit /etc/selinux/config.
- Change `SELINUX=` parameter, not comments.
- Understand runtime vs persistent configuration.
- Verify change with grep after editing.
- Reboot required for change to take effect.

**🔒 SELinux Management Best Practices Applied**:
- **Backup First**: Created backup before modifying configuration.
- **Persistent Configuration**: Used config file for permanent changes.
- **Verification**: Confirmed configuration with multiple checks.
- **Documentation**: Clear understanding of SELinux modes.
- **Planning**: Coordinated with scheduled maintenance reboot.

**⚠️ Important SELinux Concepts**:
- **Enforcing**: SELinux actively enforces security policies.
- **Permissive**: SELinux logs violations but doesn't enforce.
- **Disabled**: SELinux is completely disabled.
- **Runtime vs Config**: Runtime status vs persistent configuration.
- **Reboot Required**: Configuration changes require reboot to take effect.

---

## ⚠️ Important Production Notes

🔧 **Reboot Required**: SELinux mode changes require system reboot to take effect.
🔐 **Security Impact**: Disabling SELinux reduces system security significantly.
📊 **Temporary Measure**: Only disable for testing, re-enable after configuration.
🛡️ **Permissive Mode**: Consider using permissive instead of disabled for testing.
🗄️ **Audit Logs**: Review SELinux audit logs before disabling.
📝 **Documentation**: Document reason and timeline for SELinux being disabled.
🔍 **Monitoring**: Monitor system security while SELinux is disabled.
⚙️ **Re-enablement Plan**: Have clear plan to re-enable SELinux.

**SELinux Modes**:
```
Enforcing:
- SELinux actively enforces security policies
- Denies access based on policy rules
- Logs all denials
- Recommended for production

Permissive:
- SELinux logs policy violations but doesn't enforce
- Useful for testing and troubleshooting
- Helps identify policy issues before enforcing
- Safer than disabled for testing

Disabled:
- SELinux is completely disabled
- No policies loaded or enforced
- Reduces system security
- Use only when absolutely necessary
```

**SELinux Configuration File**:
```bash
# /etc/selinux/config

# SELINUX parameter (required)
SELINUX=enforcing   # Active enforcement
SELINUX=permissive  # Logging only
SELINUX=disabled    # Completely disabled

# SELINUXTYPE parameter (required if not disabled)
SELINUXTYPE=targeted   # Targeted policy (default)
SELINUXTYPE=minimum    # Minimal policy
SELINUXTYPE=mls        # Multi-Level Security
```

**SELinux Commands Reference**:
```bash
# Check current mode
getenforce

# Set mode temporarily (runtime only)
sudo setenforce 0  # Permissive
sudo setenforce 1  # Enforcing

# Check detailed status
sestatus

# View SELinux booleans
getsebool -a

# Set SELinux boolean
sudo setsebool -P httpd_can_network_connect on

# Check SELinux context
ls -Z /path/to/file
ps -eZ

# Restore SELinux context
sudo restorecon -Rv /path/to/directory

# View audit logs
sudo ausearch -m avc -ts recent
sudo grep AVC /var/log/audit/audit.log
```

**Security Recommendations**:
- Only disable SELinux when absolutely necessary
- Use permissive mode for testing instead of disabled
- Review audit logs to identify policy issues
- Create custom policies instead of disabling
- Document business justification for disabling
- Set timeline for re-enabling SELinux
- Implement compensating controls while disabled
- Monitor system security closely

**Re-enabling SELinux**:
```bash
# Step 1: Edit configuration
sudo sed -i 's/^SELINUX=disabled/SELINUX=enforcing/' /etc/selinux/config

# Step 2: Verify change
grep '^SELINUX=' /etc/selinux/config

# Step 3: Reboot system
sudo reboot

# Step 4: After reboot, verify
getenforce
# Should show: Enforcing

# Step 5: Check for denials
sudo ausearch -m avc -ts recent

# Step 6: Fix any context issues
sudo restorecon -Rv /
```

**Troubleshooting SELinux Issues**:
```bash
# Check for recent denials
sudo ausearch -m avc -ts today

# Generate human-readable suggestions
sudo ausearch -m avc -ts recent | audit2why

# Generate policy module to allow
sudo ausearch -m avc -ts recent | audit2allow -M mypolicy
sudo semodule -i mypolicy.pp

# Check loaded modules
sudo semodule -l

# Temporarily disable for testing
sudo setenforce 0
# Test application
# Re-enable
sudo setenforce 1
```

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Installed required SELinux packages on App Server 1
- Configured SELinux to be disabled after reboot
- Modified `/etc/selinux/config` with `SELINUX=disabled`
- Verified configuration change
- No reboot performed (scheduled for tonight)

**🔍 Verification Completed**:
- Packages installed: selinux-policy-targeted, policycoreutils, policycoreutils-python-utils
- Configuration file: `/etc/selinux/config` modified
- SELINUX parameter: Changed from `enforcing` to `disabled`
- Verification: `grep '^SELINUX=' /etc/selinux/config` shows `SELINUX=disabled`
- Runtime status: Still enforcing (expected before reboot)
- Post-reboot status: Will be disabled

**🚀 Next Steps** (After Scheduled Reboot):
- Verify SELinux is disabled with `getenforce`
- Perform application testing without SELinux
- Document any SELinux-related issues found
- Create custom SELinux policies as needed
- Plan re-enablement of SELinux
- Test applications with SELinux in permissive mode
- Re-enable SELinux in enforcing mode

**🔒 Configuration Validation**:
- ✅ SELinux packages installed
- ✅ Configuration file backed up
- ✅ SELINUX=disabled set in config file
- ✅ Configuration persists across reboots
- ✅ Change will take effect after scheduled reboot
- ✅ Runtime status correctly shows enforcing (before reboot)

**📊 Configuration Summary**:
- **Server**: App Server 1 (stapp01)
- **Packages Installed**: selinux-policy-targeted, policycoreutils, policycoreutils-python-utils
- **Configuration File**: /etc/selinux/config
- **SELINUX Parameter**: disabled
- **Current Runtime**: Enforcing (before reboot)
- **Post-Reboot Status**: Disabled
- **Reboot Required**: Yes (scheduled for tonight)

**🔍 Key Learning Points**:
- SELinux mode changes require system reboot
- Runtime status (getenforce) differs from config file setting
- `/etc/selinux/config` controls persistent SELinux configuration
- Three modes: enforcing, permissive, disabled
- Disabling SELinux reduces system security
- Should be temporary measure for testing only
