# 🔐 Task 7 - Disable Direct SSH Root Login on All App Servers

**📌 Task Description**

Following security audits, the **xFusionCorp Industries security team** has rolled out new protocols, including the restriction of direct root SSH login. Direct root SSH access poses significant security risks, as it provides unrestricted system access and makes systems vulnerable to brute-force attacks. Disabling root login enforces the use of regular user accounts with sudo privileges, creating an audit trail and implementing the principle of least privilege.

**👉 Task Requirements**:
- **Target Servers**: All App Servers (stapp01, stapp02, stapp03) in Stratos Datacenter
- **Configuration Change**:
  - File: `/etc/ssh/sshd_config`
  - Setting: `PermitRootLogin no`
  - Service: Restart `sshd` after configuration change
- **Security Objectives**:
  - Prevent direct root SSH login
  - Enforce sudo-based privilege escalation
  - Create audit trail for administrative actions
  - Reduce attack surface for brute-force attempts
  - Implement principle of least privilege
- Modify SSH daemon configuration on all servers
- Restart SSH service to apply changes
- Verify configuration is active
- Ensure consistent security policy across datacenter

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to App Server 1

```bash
ssh tony@stapp01
```

**Purpose**: Establish SSH connection to App Server 1 to begin SSH security hardening.

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

## 🔹 Step 2: Backup SSH Configuration File

```bash
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.backup
```

**Purpose**: Create backup of SSH configuration before making changes.

**Expected Output**:
```
[no output indicates success]
```

**Success Indicators**:
- ✅ Backup file created
- ✅ Can restore configuration if needed
- ✅ Safety measure before critical changes

**Verification**:
```bash
ls -l /etc/ssh/sshd_config*
```

**Expected Output**:
```
-rw-------. 1 root root 3906 Feb 08 14:00 /etc/ssh/sshd_config
-rw-------. 1 root root 3906 Feb 08 14:00 /etc/ssh/sshd_config.backup
```

---

## 🔹 Step 3: Check Current Root Login Setting

```bash
sudo grep "^PermitRootLogin" /etc/ssh/sshd_config
```

**Purpose**: Verify current root login configuration before modification.

**Expected Output** (if setting exists):
```
PermitRootLogin yes
```

**Alternative Output** (if commented):
```
[no output - setting is commented]
```

**Note**: If no output, the setting is commented (default behavior varies by distribution).

---

## 🔹 Step 4: Edit SSH Configuration File

```bash
sudo vi /etc/ssh/sshd_config
```

**Purpose**: Modify SSH daemon configuration to disable root login.

**Find this line**:
```
#PermitRootLogin yes
```

**Or**:
```
PermitRootLogin yes
```

**Change it to**:
```
PermitRootLogin no
```

**Vi Editor Commands**:
1. Press `/` to search
2. Type `PermitRootLogin` and press Enter
3. Press `i` to enter insert mode
4. Remove `#` if present and change `yes` to `no`
5. Press `Esc` to exit insert mode
6. Type `:wq` and press Enter to save and quit

**Alternative Using sed** (automated):
```bash
sudo sed -i 's/^#*PermitRootLogin.*/PermitRootLogin no/' /etc/ssh/sshd_config
```

**Success Indicators**:
- ✅ Line is uncommented (no `#` at start)
- ✅ Value is set to `no`
- ✅ File saved successfully

---

## 🔹 Step 5: Verify Configuration Change

```bash
sudo grep "^PermitRootLogin" /etc/ssh/sshd_config
```

**Purpose**: Confirm the configuration change was applied correctly.

**Expected Output**:
```
PermitRootLogin no
```

**Success Indicators**:
- ✅ Line is uncommented (no `#` prefix)
- ✅ Value is `no`
- ✅ Configuration is ready to apply

---

## 🔹 Step 6: Test Configuration Syntax

```bash
sudo sshd -t
```

**Purpose**: Validate SSH configuration syntax before restarting service.

**Expected Output**:
```
[no output indicates success]
```

**Success Indicators**:
- ✅ No syntax errors
- ✅ Configuration is valid
- ✅ Safe to restart SSH service

**If Errors Occur**:
```
/etc/ssh/sshd_config: line 42: Bad configuration option: PermitRootLogin
```
Review and fix the configuration file before proceeding.

---

## 🔹 Step 7: Restart SSH Service

```bash
sudo systemctl restart sshd
```

**Purpose**: Apply the configuration changes by restarting the SSH daemon.

**Expected Output**:
```
[no output indicates success]
```

**Success Indicators**:
- ✅ SSH service restarted successfully
- ✅ New configuration is active
- ✅ Existing SSH connections remain active

**⚠️ Important**: Existing SSH sessions are NOT terminated when restarting sshd. Only new connections will use the new configuration.

---

## 🔹 Step 8: Verify SSH Service Status

```bash
sudo systemctl status sshd
```

**Purpose**: Confirm SSH service is running after restart.

**Expected Output**:
```
● sshd.service - OpenSSH server daemon
   Loaded: loaded (/usr/lib/systemd/system/sshd.service; enabled; vendor preset: enabled)
   Active: active (running) since Sat 2026-02-08 14:00:00 UTC; 10s ago
     Docs: man:sshd(8)
           man:sshd_config(5)
 Main PID: 12345 (sshd)
    Tasks: 1
   Memory: 1.2M
   CGroup: /system.slice/sshd.service
           └─12345 /usr/sbin/sshd -D
```

**Success Indicators**:
- ✅ Status shows `active (running)`
- ✅ Service is enabled
- ✅ No error messages

---

## 🔹 Step 9: Verify Active Configuration

```bash
sudo sshd -T | grep permitrootlogin
```

**Purpose**: Display the active SSH configuration to confirm root login is disabled.

**Command Breakdown**:
- `sudo sshd -T`: Test mode - dump effective configuration
- `|`: Pipe output to next command
- `grep permitrootlogin`: Filter for root login setting

**Expected Output**:
```
permitrootlogin no
```

**Success Indicators**:
- ✅ Setting shows `no`
- ✅ Configuration is active
- ✅ Root login is disabled

---

## 🔹 Step 10: Exit from App Server 1

```bash
exit
```

**Purpose**: Close SSH connection to App Server 1.

**Expected Output**:
```
logout
Connection to stapp01 closed.
```

---

## 🔹 Step 11: Repeat for App Server 2

```bash
ssh steve@stapp02
```

**Purpose**: Connect to App Server 2 to apply the same security configuration.

**Expected Output**:
```
steve@stapp02's password: 
Welcome to App Server 2 - Stratos Datacenter
[steve@stapp02 ~]$
```

**Execute the same commands**:
```bash
# Backup configuration
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.backup

# Check current setting
sudo grep "^PermitRootLogin" /etc/ssh/sshd_config

# Edit configuration (automated method)
sudo sed -i 's/^#*PermitRootLogin.*/PermitRootLogin no/' /etc/ssh/sshd_config

# Verify change
sudo grep "^PermitRootLogin" /etc/ssh/sshd_config

# Test configuration
sudo sshd -t

# Restart SSH service
sudo systemctl restart sshd

# Verify service status
sudo systemctl status sshd

# Verify active configuration
sudo sshd -T | grep permitrootlogin
```

**Expected Output**:
```
permitrootlogin no
```

**Exit from App Server 2**:
```bash
exit
```

---

## 🔹 Step 12: Repeat for App Server 3

```bash
ssh banner@stapp03
```

**Purpose**: Connect to App Server 3 to complete security configuration across all servers.

**Expected Output**:
```
banner@stapp03's password: 
Welcome to App Server 3 - Stratos Datacenter
[banner@stapp03 ~]$
```

**Execute the same commands**:
```bash
# Backup configuration
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.backup

# Check current setting
sudo grep "^PermitRootLogin" /etc/ssh/sshd_config

# Edit configuration (automated method)
sudo sed -i 's/^#*PermitRootLogin.*/PermitRootLogin no/' /etc/ssh/sshd_config

# Verify change
sudo grep "^PermitRootLogin" /etc/ssh/sshd_config

# Test configuration
sudo sshd -t

# Restart SSH service
sudo systemctl restart sshd

# Verify service status
sudo systemctl status sshd

# Verify active configuration
sudo sshd -T | grep permitrootlogin
```

**Expected Output**:
```
permitrootlogin no
```

**Exit from App Server 3**:
```bash
exit
```

---

## 🔹 Step 13: Test Root Login is Disabled (Optional)

```bash
ssh root@stapp01
```

**Purpose**: Verify that direct root SSH login is now blocked.

**Expected Output**:
```
root@stapp01's password: 
Permission denied, please try again.
```

**Success Indicators**:
- ✅ Root login is denied
- ✅ Security policy is enforced
- ✅ Configuration is working correctly

**Note**: Even with correct root password, login will be denied.

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **each App Server** (stapp01, stapp02, stapp03):

```bash
# Connect to App Server 1
ssh tony@stapp01



# Edit configuration (automated)
sudo sed -i 's/^#*PermitRootLogin.*/PermitRootLogin no/' /etc/ssh/sshd_config

# Verify change
sudo grep "^PermitRootLogin" /etc/ssh/sshd_config

# Test configuration syntax
sudo sshd -t

# Restart SSH service
sudo systemctl restart sshd

# Verify service status
sudo systemctl status sshd

# Verify active configuration
sudo sshd -T | grep permitrootlogin

# Exit
exit

# Repeat for App Server 2
ssh steve@stapp02
sudo sed -i 's/^#*PermitRootLogin.*/PermitRootLogin no/' /etc/ssh/sshd_config
sudo systemctl restart sshd
sudo sshd -T | grep permitrootlogin
exit

# Repeat for App Server 3
ssh banner@stapp03
sudo sed -i 's/^#*PermitRootLogin.*/PermitRootLogin no/' /etc/ssh/sshd_config
sudo systemctl restart sshd
sudo sshd -T | grep permitrootlogin
exit
```

---

## 💡 Common SSH Configuration Issues & Fixes

### **Issue 1: Configuration Not Taking Effect**
**Problem**: Root login still works after configuration change.
**Fix**: Ensure SSH service was restarted and configuration is uncommented.
```bash
# Verify configuration is uncommented
sudo grep "^PermitRootLogin" /etc/ssh/sshd_config

# Should show: PermitRootLogin no (no # at start)

# Restart SSH service
sudo systemctl restart sshd

# Verify active configuration
sudo sshd -T | grep permitrootlogin
```

### **Issue 2: Syntax Error in Configuration**
**Problem**: `sshd -t` reports configuration errors.
**Fix**: Review and fix syntax errors before restarting service.
```bash
# Test configuration
sudo sshd -t

# If errors, restore backup
sudo cp /etc/ssh/sshd_config.backup /etc/ssh/sshd_config

# Re-edit carefully
sudo vi /etc/ssh/sshd_config
```

### **Issue 3: SSH Service Fails to Restart**
**Problem**: `systemctl restart sshd` fails with error.
**Fix**: Check configuration syntax and service logs.
```bash
# Check configuration syntax
sudo sshd -t

# View service logs
sudo journalctl -u sshd -n 50

# Check service status
sudo systemctl status sshd

# If needed, restore backup
sudo cp /etc/ssh/sshd_config.backup /etc/ssh/sshd_config
sudo systemctl restart sshd
```

### **Issue 4: Locked Out of Server**
**Problem**: Cannot connect to server after changes.
**Fix**: This should not happen if you're logged in as regular user, not root.
```bash
# Always maintain an active SSH session while making changes
# If locked out, use console access to restore configuration

# Via console:
sudo cp /etc/ssh/sshd_config.backup /etc/ssh/sshd_config
sudo systemctl restart sshd
```

### **Issue 5: Multiple PermitRootLogin Lines**
**Problem**: Configuration file has multiple `PermitRootLogin` entries.
**Fix**: Ensure only one uncommented line exists.
```bash
# Find all instances
sudo grep -n "PermitRootLogin" /etc/ssh/sshd_config

# Remove duplicates, keep only one uncommented line
sudo vi /etc/ssh/sshd_config

# Verify only one active line
sudo grep "^PermitRootLogin" /etc/ssh/sshd_config
```

---

## 🔧 Troubleshooting Failures

### **Error: Permission Denied (Editing File)**
**Symptoms**: Cannot edit `/etc/ssh/sshd_config`.
**Solution**: Ensure using sudo for all administrative commands.
```bash
# Always use sudo
sudo vi /etc/ssh/sshd_config

# Or use sed with sudo
sudo sed -i 's/^#*PermitRootLogin.*/PermitRootLogin no/' /etc/ssh/sshd_config
```

### **Error: sshd Service Not Found**
**Symptoms**: `systemctl` cannot find sshd service.
**Solution**: Service might be named differently on some systems.
```bash
# Try alternative service names
sudo systemctl restart ssh      # Debian/Ubuntu
sudo systemctl restart sshd     # RHEL/CentOS

# Check service name
systemctl list-units | grep ssh
```

### **Error: Configuration File Not Found**
**Symptoms**: `/etc/ssh/sshd_config` does not exist.
**Solution**: Verify SSH server is installed.
```bash
# Check if SSH server is installed
rpm -qa | grep openssh-server   # RHEL/CentOS
dpkg -l | grep openssh-server   # Debian/Ubuntu

# Install if missing
sudo yum install openssh-server  # RHEL/CentOS
sudo apt install openssh-server  # Debian/Ubuntu
```

### **Error: Root Can Still Login**
**Symptoms**: Root login works despite configuration change.
**Solution**: Verify configuration is active and service restarted.
```bash
# Check active configuration
sudo sshd -T | grep permitrootlogin

# Should show: permitrootlogin no

# If shows 'yes', restart service
sudo systemctl restart sshd

# Verify configuration file
sudo grep "^PermitRootLogin" /etc/ssh/sshd_config
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The primary challenge was implementing a critical security policy change across multiple servers while ensuring the configuration is correctly applied and SSH service remains operational. Understanding the difference between configuration file changes and active configuration is crucial for successful SSH hardening.

**💡 Solution Approach**:
1. **Backup First**: Created backup of SSH configuration before making changes.
2. **Configuration Modification**: Edited `/etc/ssh/sshd_config` to set `PermitRootLogin no`.
3. **Syntax Validation**: Used `sshd -t` to test configuration before applying.
4. **Service Restart**: Restarted sshd service to activate new configuration.
5. **Active Verification**: Used `sshd -T` to confirm active configuration matches file.
6. **Multi-Server Consistency**: Applied identical configuration across all three app servers.

**🎯 Key Success Factors**:
- **Backup Strategy**: Created backup before changes for easy rollback.
- **Syntax Testing**: Validated configuration before restarting service.
- **Active Verification**: Used `sshd -T` to confirm running configuration.
- **Service Management**: Properly restarted sshd to apply changes.
- **Consistent Application**: Applied same configuration across all servers.

**⚠️ Critical Fixes**:
- Always backup configuration files before making changes.
- Use `sshd -t` to test syntax before restarting service.
- Verify with `sshd -T` that active configuration matches file configuration.
- Ensure line is uncommented (no `#` prefix) for setting to take effect.
- Restart sshd service after configuration changes.

**🔒 Linux SSH Security Best Practices Applied**:
- **Disable Root Login**: Prevents direct root access via SSH.
- **Sudo-Based Access**: Enforces privilege escalation through sudo.
- **Audit Trail**: Creates logs for administrative actions.
- **Principle of Least Privilege**: Users login with minimal privileges.
- **Configuration Validation**: Tests syntax before applying changes.

**⚠️ Important Security Concepts**:
- **Root Login Risk**: Direct root access bypasses audit trails.
- **Sudo Advantage**: Logs who performed administrative actions.
- **Configuration vs Active**: File changes require service restart.
- **Backup Importance**: Enables quick recovery from misconfigurations.
- **Testing First**: Prevents service disruption from syntax errors.

---

## ⚠️ Important Production Notes

🔧 **Backup First**: Always backup `/etc/ssh/sshd_config` before making changes.
🔐 **Test Before Apply**: Use `sshd -t` to validate syntax before restarting service.
📊 **Active Session**: Maintain active SSH session while making changes to avoid lockout.
🛡️ **Gradual Rollout**: Test on one server before applying to all servers.
🗄️ **Configuration Management**: Use Ansible/Puppet for consistent SSH configuration across servers.
📝 **Audit Logging**: Monitor `/var/log/secure` for SSH authentication attempts.
🔍 **Regular Reviews**: Periodically audit SSH configuration for security compliance.
⚙️ **Additional Hardening**: Consider implementing key-based authentication and disabling password auth.

**Additional SSH Security Hardening**:
```bash
# Disable password authentication (use SSH keys only)
PasswordAuthentication no

# Disable empty passwords
PermitEmptyPasswords no

# Limit SSH protocol to version 2
Protocol 2

# Restrict SSH access to specific users
AllowUsers tony steve banner

# Restrict SSH access to specific groups
AllowGroups sshusers

# Change default SSH port (security through obscurity)
Port 2222

# Disable X11 forwarding if not needed
X11Forwarding no

# Set idle timeout
ClientAliveInterval 300
ClientAliveCountMax 2

# Disable host-based authentication
HostbasedAuthentication no

# Disable rhosts authentication
IgnoreRhosts yes
```

**SSH Configuration Best Practices**:
- Use SSH key-based authentication instead of passwords
- Implement fail2ban to prevent brute-force attacks
- Use strong encryption algorithms
- Regularly update OpenSSH to latest version
- Monitor SSH logs for suspicious activity
- Implement two-factor authentication (2FA)
- Use SSH bastion/jump hosts for production access
- Restrict SSH access by IP address using firewall rules

**Verification Commands**:
```bash
# View all active SSH configuration
sudo sshd -T

# Test configuration syntax
sudo sshd -t

# View SSH service logs
sudo journalctl -u sshd -f

# Monitor SSH authentication attempts
sudo tail -f /var/log/secure

# List active SSH sessions
who

# View failed login attempts
sudo grep "Failed password" /var/log/secure
```

**Rollback Procedure**:
```bash
# If issues occur, restore backup
sudo cp /etc/ssh/sshd_config.backup /etc/ssh/sshd_config

# Test restored configuration
sudo sshd -t

# Restart service
sudo systemctl restart sshd

# Verify service is running
sudo systemctl status sshd
```

**Security Recommendations**:
- Implement SSH key-based authentication for all users
- Disable password authentication after key deployment
- Use fail2ban or similar tools to block brute-force attempts
- Implement network-level restrictions (firewall, security groups)
- Monitor SSH logs for unauthorized access attempts
- Regularly rotate SSH keys
- Use SSH certificates for large-scale deployments
- Implement jump hosts/bastion servers for production access
- Enable two-factor authentication (2FA) for SSH
- Set up alerts for root login attempts

**Compliance Considerations**:
- Document SSH configuration changes for audit purposes
- Ensure changes comply with organizational security policies
- Maintain change logs for configuration modifications
- Implement peer review for SSH configuration changes
- Test changes in non-production environment first
- Schedule maintenance windows for SSH configuration changes
- Notify stakeholders before implementing security changes

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Disabled direct SSH root login on all three App servers
- Modified `/etc/ssh/sshd_config` with `PermitRootLogin no`
- Restarted SSH service on all servers to apply changes
- Verified active configuration on all servers
- Implemented consistent security policy across Stratos Datacenter

**🔍 Verification Completed**:
- Configuration file modified: `PermitRootLogin no`
- SSH service restarted: `systemctl restart sshd`
- Active configuration verified: `sshd -T | grep permitrootlogin`
- Service status confirmed: `systemctl status sshd`
- Root login blocked: Direct root SSH login denied
- Consistent across servers: stapp01, stapp02, stapp03

**🚀 Next Steps** (Optional):
- Implement SSH key-based authentication
- Disable password authentication
- Configure fail2ban for brute-force protection
- Set up SSH logging and monitoring
- Implement two-factor authentication (2FA)
- Document SSH security policy
- Train users on sudo-based access

**🔒 Security Validation**:
- ✅ Direct root SSH login is disabled on all servers
- ✅ Users must login with regular accounts
- ✅ Administrative actions require sudo (creates audit trail)
- ✅ Principle of least privilege enforced
- ✅ Attack surface reduced for brute-force attempts
- ✅ Configuration is consistent across datacenter

**📊 Security Impact**:
- **Before**: Root could login directly via SSH (high risk)
- **After**: Root login disabled, sudo required (reduced risk)
- **Audit Trail**: All administrative actions logged with user identity
- **Compliance**: Meets security audit requirements
- **Best Practice**: Aligns with industry security standards

**🔔 Important Reminders**:
- Root login via SSH is now **disabled** on all app servers
- Users must login with their regular accounts (tony, steve, banner)
- Administrative tasks require `sudo` command
- All sudo actions are logged in `/var/log/secure`
- Backup configuration files exist at `/etc/ssh/sshd_config.backup`
- Test root login blocking: `ssh root@stapp01` (should be denied)
