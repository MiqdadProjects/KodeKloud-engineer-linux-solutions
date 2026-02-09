# 🔐 Task 2 - Update Message of the Day Banner on All Servers

**📌 Task Description**

During the monthly compliance meeting, it was pointed out that several servers in the **Stratos DC** do not have a valid banner. The security team has provided several approved templates which should be applied to the servers to maintain compliance. These will be displayed to the user upon a successful login. The **Message of the Day (MOTD)** is a system message displayed to users after successful authentication, typically used for security warnings, compliance notices, or system information.

**👉 Task Requirements**:
- **Target Servers**: All App Servers (stapp01, stapp02, stapp03) and DB Server (stdb01)
- **Source File**: `/home/thor/nautilus_banner` on Jump Host
- **Destination**: `/etc/motd` on all target servers
- **Operation Objectives**:
  - Copy approved banner template from Jump Host to all servers
  - Replace existing MOTD with approved template
  - Verify banner displays on login
  - Ensure compliance across all servers
- Use `scp` for secure file transfer
- Update `/etc/motd` file on each server
- Verify banner appears on new login sessions

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Verify Banner Template on Jump Host

```bash
ls -l /home/thor/nautilus_banner
```

**Purpose**: Confirm the approved banner template file exists on the Jump Host.

**Expected Output**:
```
-rw-r--r-- 1 thor thor 256 Feb 09 18:00 /home/thor/nautilus_banner
```

**Success Indicators**:
- ✅ File exists
- ✅ File is readable
- ✅ Ready to copy to servers

---

## 🔹 Step 2: View Banner Template Content (Optional)

```bash
cat /home/thor/nautilus_banner
```

**Purpose**: Review the approved banner content before deployment.

**Expected Output** (sample):
```
#########################################################################
#                                                                       #
#                   AUTHORIZED ACCESS ONLY                              #
#                                                                       #
#   This system is for authorized use only. Unauthorized access is     #
#   prohibited and will be prosecuted to the fullest extent of law.    #
#                                                                       #
#########################################################################
```

**Success Indicators**:
- ✅ Banner content displayed
- ✅ Content is appropriate for compliance
- ✅ Ready for deployment

---

## 🔹 Step 3: Copy Banner to App Server 1

```bash
scp /home/thor/nautilus_banner tony@stapp01:/tmp/
```

**Purpose**: Securely copy the banner template to App Server 1's temporary directory.

**Command Breakdown**:
- `scp`: Secure copy protocol
- `/home/thor/nautilus_banner`: Source file path
- `tony@stapp01`: Target user and hostname
- `/tmp/`: Destination directory (temporary location)

**Expected Output**:
```
tony@stapp01's password: 
nautilus_banner                                100%  256     0.3KB/s   00:00
```

**Success Indicators**:
- ✅ File transferred successfully
- ✅ 100% completion
- ✅ No error messages

---

## 🔹 Step 4: Copy Banner to App Server 2

```bash
scp /home/thor/nautilus_banner steve@stapp02:/tmp/
```

**Purpose**: Securely copy the banner template to App Server 2.

**Expected Output**:
```
steve@stapp02's password: 
nautilus_banner                                100%  256     0.3KB/s   00:00
```

**Success Indicators**:
- ✅ File transferred successfully

---

## 🔹 Step 5: Copy Banner to App Server 3

```bash
scp /home/thor/nautilus_banner banner@stapp03:/tmp/
```

**Purpose**: Securely copy the banner template to App Server 3.

**Expected Output**:
```
banner@stapp03's password: 
nautilus_banner                                100%  256     0.3KB/s   00:00
```

**Success Indicators**:
- ✅ File transferred successfully

---

## 🔹 Step 6: Copy Banner to Database Server

```bash
scp /home/thor/nautilus_banner peter@stdb01:/tmp/
```

**Purpose**: Securely copy the banner template to the Database Server.

**Expected Output**:
```
peter@stdb01's password: 
nautilus_banner                                100%  256     0.3KB/s   00:00
```

**Success Indicators**:
- ✅ File transferred successfully
- ✅ All servers now have the banner file

---

## 🔹 Step 7: Connect to App Server 1

```bash
ssh tony@stapp01
```

**Purpose**: Establish SSH connection to App Server 1 to deploy the banner.

**Expected Output**:
```
tony@stapp01's password: 
Welcome to App Server 1 - Stratos Datacenter
[tony@stapp01 ~]$
```

**Success Indicators**:
- ✅ Successfully authenticated
- ✅ Ready to deploy banner

---

## 🔹 Step 8: Verify Banner File on App Server 1

```bash
ls -l /tmp/nautilus_banner
```

**Purpose**: Confirm the banner file was copied successfully.

**Expected Output**:
```
-rw-r--r-- 1 tony tony 256 Feb 09 18:05 /tmp/nautilus_banner
```

**Success Indicators**:
- ✅ File exists in /tmp
- ✅ File size matches original

---

## 🔹 Step 9: Backup Existing MOTD on App Server 1 (Optional)

```bash
sudo cp /etc/motd /etc/motd.backup
```

**Purpose**: Create backup of existing MOTD before replacement.

**Expected Output**:
```
[no output indicates success]
```

**Success Indicators**:
- ✅ Backup created
- ✅ Can restore if needed

---

## 🔹 Step 10: Deploy Banner to MOTD on App Server 1

```bash
sudo mv /tmp/nautilus_banner /etc/motd
```

**Purpose**: Move the approved banner to replace the system MOTD file.

**Command Breakdown**:
- `sudo`: Execute with administrative privileges
- `mv`: Move (rename) file
- `/tmp/nautilus_banner`: Source file
- `/etc/motd`: Destination (Message of the Day file)

**Expected Output**:
```
[no output indicates success]
```

**Success Indicators**:
- ✅ File moved successfully
- ✅ `/etc/motd` now contains approved banner
- ✅ Old file replaced

---

## 🔹 Step 11: Verify MOTD Content on App Server 1

```bash
cat /etc/motd
```

**Purpose**: Confirm the MOTD file contains the approved banner content.

**Expected Output**:
```
#########################################################################
#                                                                       #
#                   AUTHORIZED ACCESS ONLY                              #
#                                                                       #
#   This system is for authorized use only. Unauthorized access is     #
#   prohibited and will be prosecuted to the fullest extent of law.    #
#                                                                       #
#########################################################################
```

**Success Indicators**:
- ✅ Content matches approved banner
- ✅ File is properly formatted

---

## 🔹 Step 12: Exit and Re-login to Verify Banner Display

```bash
exit
ssh tony@stapp01
```

**Purpose**: Test that the banner displays on new login sessions.

**Expected Output**:
```
tony@stapp01's password: 
#########################################################################
#                                                                       #
#                   AUTHORIZED ACCESS ONLY                              #
#                                                                       #
#   This system is for authorized use only. Unauthorized access is     #
#   prohibited and will be prosecuted to the fullest extent of law.    #
#                                                                       #
#########################################################################
Last login: Sun Feb 09 18:10:00 2026 from jump_host
[tony@stapp01 ~]$
```

**Success Indicators**:
- ✅ Banner displays after password entry
- ✅ Banner appears before command prompt
- ✅ Compliance requirement met for App Server 1

---

## 🔹 Step 13: Deploy Banner on App Server 2

```bash
ssh steve@stapp02
sudo mv /tmp/nautilus_banner /etc/motd
cat /etc/motd
exit
ssh steve@stapp02
```

**Purpose**: Repeat deployment process on App Server 2.

**Success Indicators**:
- ✅ Banner deployed
- ✅ Banner displays on login

---

## 🔹 Step 14: Deploy Banner on App Server 3

```bash
ssh banner@stapp03
sudo mv /tmp/nautilus_banner /etc/motd
cat /etc/motd
exit
ssh banner@stapp03
```

**Purpose**: Repeat deployment process on App Server 3.

**Success Indicators**:
- ✅ Banner deployed
- ✅ Banner displays on login

---

## 🔹 Step 15: Deploy Banner on Database Server

```bash
ssh peter@stdb01
sudo mv /tmp/nautilus_banner /etc/motd
cat /etc/motd
exit
ssh peter@stdb01
```

**Purpose**: Repeat deployment process on Database Server.

**Success Indicators**:
- ✅ Banner deployed
- ✅ Banner displays on login
- ✅ All servers now compliant

---

## 📋 Quick Command Reference

For quick copy-paste, execute from **Jump Host**:

```bash
# Verify source file
ls -l /home/thor/nautilus_banner
cat /home/thor/nautilus_banner

# Copy to all servers
scp /home/thor/nautilus_banner tony@stapp01:/tmp/
scp /home/thor/nautilus_banner steve@stapp02:/tmp/
scp /home/thor/nautilus_banner banner@stapp03:/tmp/
scp /home/thor/nautilus_banner peter@stdb01:/tmp/

# Deploy on App Server 1
ssh tony@stapp01
sudo mv /tmp/nautilus_banner /etc/motd
cat /etc/motd
exit
ssh tony@stapp01  # Verify banner displays
exit

# Deploy on App Server 2
ssh steve@stapp02
sudo mv /tmp/nautilus_banner /etc/motd
cat /etc/motd
exit
ssh steve@stapp02  # Verify banner displays
exit

# Deploy on App Server 3
ssh banner@stapp03
sudo mv /tmp/nautilus_banner /etc/motd
cat /etc/motd
exit
ssh banner@stapp03  # Verify banner displays
exit

# Deploy on Database Server
ssh peter@stdb01
sudo mv /tmp/nautilus_banner /etc/motd
cat /etc/motd
exit
ssh peter@stdb01  # Verify banner displays
exit
```

---

## 💡 Common MOTD Configuration Issues & Fixes

### **Issue 1: Banner Not Displaying**
**Problem**: MOTD doesn't show on login.
**Fix**: Verify PAM configuration and file permissions.
```bash
# Check if pam_motd is enabled
grep pam_motd /etc/pam.d/sshd

# Check file permissions
ls -l /etc/motd

# Should be readable by all
sudo chmod 644 /etc/motd
```

### **Issue 2: Permission Denied**
**Problem**: Cannot move file to /etc/motd.
**Fix**: Use sudo for system file modifications.
```bash
# WRONG - Will fail
mv /tmp/nautilus_banner /etc/motd

# CORRECT - Use sudo
sudo mv /tmp/nautilus_banner /etc/motd
```

### **Issue 3: File Not Found in /tmp**
**Problem**: Banner file missing from /tmp after SCP.
**Fix**: Verify SCP transfer completed successfully.
```bash
# Check if file exists
ls -l /tmp/nautilus_banner

# Re-copy if needed
scp /home/thor/nautilus_banner user@server:/tmp/
```

### **Issue 4: Banner Shows Old Content**
**Problem**: Old banner still displays after update.
**Fix**: Verify file was actually replaced.
```bash
# Check current content
cat /etc/motd

# Verify file timestamp
ls -l /etc/motd

# Re-deploy if needed
sudo mv /tmp/nautilus_banner /etc/motd
```

### **Issue 5: Multiple Banners Display**
**Problem**: Both old and new banners appear.
**Fix**: Check for multiple MOTD sources.
```bash
# Check dynamic MOTD scripts
ls -l /etc/update-motd.d/

# Disable if needed
sudo chmod -x /etc/update-motd.d/*

# Or check /etc/motd.d/
ls -l /etc/motd.d/
```

---

## 🔧 Troubleshooting Failures

### **Error: SCP Connection Refused**
**Symptoms**: `ssh: connect to host server port 22: Connection refused`.
**Solution**: Verify SSH service is running on target server.
```bash
# Check SSH service status
sudo systemctl status sshd

# Start if needed
sudo systemctl start sshd
```

### **Error: Permission Denied (publickey)**
**Symptoms**: `Permission denied (publickey,password)`.
**Solution**: Verify credentials and SSH configuration.
```bash
# Use correct username for each server
# stapp01: tony
# stapp02: steve
# stapp03: banner
# stdb01: peter

# Verify password authentication is enabled
grep PasswordAuthentication /etc/ssh/sshd_config
```

### **Error: No Space Left on Device**
**Symptoms**: Cannot write to /etc/motd.
**Solution**: Check disk space and clean up if needed.
```bash
# Check disk usage
df -h /

# Clean up /tmp if needed
sudo rm -f /tmp/old_files
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The primary challenge was distributing a compliance banner template from the Jump Host to multiple servers and ensuring it displays correctly on user login. Understanding the MOTD mechanism and using SCP for secure file distribution is crucial for compliance management.

**💡 Solution Approach**:
1. **Source Verification**: Confirmed banner template exists on Jump Host.
2. **Secure Distribution**: Used SCP to copy file to all servers' /tmp directories.
3. **Deployment**: Moved banner from /tmp to /etc/motd on each server.
4. **Verification**: Logged out and back in to verify banner displays.
5. **Multi-Server**: Repeated process consistently across all 4 servers.

**🎯 Key Success Factors**:
- **SCP Usage**: Secure file transfer from Jump Host to all servers.
- **Temporary Location**: Used /tmp for staging before final deployment.
- **Sudo Privileges**: Required for modifying /etc/motd.
- **Verification**: Tested actual login to confirm banner display.
- **Consistency**: Applied identical configuration across all servers.

**⚠️ Critical Fixes**:
- Always use sudo to move files to /etc/motd.
- Copy to /tmp first (user-writable), then move to /etc.
- Verify banner displays by logging out and back in.
- Apply to all servers (app servers AND database server).
- Use correct username for each server.

**🔒 Linux MOTD Best Practices Applied**:
- **Compliance**: Used approved template for legal protection.
- **Consistency**: Applied same banner across all servers.
- **Verification**: Tested actual display, not just file content.
- **Backup**: Created backup of original MOTD (optional but recommended).
- **Security**: Used secure transfer method (SCP).

**⚠️ Important MOTD Concepts**:
- **MOTD Location**: `/etc/motd` is the standard location.
- **Display Timing**: Shows after authentication, before shell prompt.
- **PAM Integration**: Controlled by pam_motd module.
- **Dynamic MOTD**: Some systems use /etc/update-motd.d/ scripts.
- **Compliance**: Often required for legal/security compliance.

---

## ⚠️ Important Production Notes

🔧 **MOTD vs Issue**: `/etc/motd` displays after login; `/etc/issue` displays before login.
🔐 **Legal Protection**: Proper banners provide legal protection against unauthorized access.
📊 **Compliance**: Many regulations require login banners (PCI-DSS, HIPAA, etc.).
🛡️ **Content Review**: Ensure banner content is legally reviewed and approved.
🗄️ **Consistency**: All servers in same environment should have identical banners.
📝 **Documentation**: Document banner approval and deployment dates.
🔍 **Monitoring**: Regularly audit servers to ensure banners remain in place.
⚙️ **Automation**: Use configuration management for consistent banner deployment.

**MOTD File Locations**:
```bash
/etc/motd              # Static message of the day
/etc/issue             # Pre-login banner (local)
/etc/issue.net         # Pre-login banner (network/SSH)
/etc/update-motd.d/    # Dynamic MOTD scripts (Ubuntu/Debian)
/etc/motd.d/           # Additional MOTD snippets (some distros)
```

**PAM Configuration**:
```bash
# Check PAM configuration for MOTD
grep pam_motd /etc/pam.d/sshd
grep pam_motd /etc/pam.d/login

# Typical entry:
# session    optional     pam_motd.so

# Disable dynamic MOTD if needed
# Comment out or remove pam_motd.so line
```

**Security Recommendations**:
- Use legally approved banner text
- Include authorization warning
- Mention monitoring and logging
- Avoid welcoming language (implies invitation)
- Keep banner concise and clear
- Apply consistently across all systems
- Regular compliance audits
- Document banner approval process

**Sample Compliance Banner Templates**:
```bash
# Minimal compliance banner
cat > /etc/motd << 'EOF'
AUTHORIZED ACCESS ONLY
Unauthorized access prohibited
All activity monitored and logged
EOF

# Detailed compliance banner
cat > /etc/motd << 'EOF'
#########################################################################
#                   AUTHORIZED USERS ONLY                               #
#                                                                       #
# This system is for authorized use only. Users have no expectation    #
# of privacy. Unauthorized access is prohibited and will be prosecuted #
# to the fullest extent of law. All activities are monitored and       #
# logged.                                                               #
#########################################################################
EOF
```

**Dynamic MOTD Management**:
```bash
# List dynamic MOTD scripts (Ubuntu/Debian)
ls -l /etc/update-motd.d/

# Disable specific script
sudo chmod -x /etc/update-motd.d/10-help-text

# Enable specific script
sudo chmod +x /etc/update-motd.d/10-help-text

# Disable all dynamic MOTD
sudo chmod -x /etc/update-motd.d/*

# Test MOTD generation
sudo run-parts /etc/update-motd.d/
```

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Copied approved banner template from Jump Host to all servers
- Deployed banner to /etc/motd on all app servers and database server
- Verified banner displays on login for all servers
- Achieved compliance across entire infrastructure

**🔍 Verification Completed**:
- **App Server 1 (stapp01)**: Banner deployed and verified
- **App Server 2 (stapp02)**: Banner deployed and verified
- **App Server 3 (stapp03)**: Banner deployed and verified
- **Database Server (stdb01)**: Banner deployed and verified

**🚀 Next Steps** (Optional):
- Schedule regular compliance audits
- Implement automated banner verification
- Document banner approval in change management
- Configure monitoring for MOTD file changes
- Update banner if compliance requirements change

**🔒 Configuration Validation**:
- ✅ All 4 servers have identical approved banner
- ✅ Banner displays after successful authentication
- ✅ Banner content matches approved template
- ✅ Configuration persists across reboots
- ✅ Compliance requirements met
- ✅ Legal protection in place

**📊 Configuration Summary**:
- **Servers Configured**: 4 (stapp01, stapp02, stapp03, stdb01)
- **Source File**: /home/thor/nautilus_banner (Jump Host)
- **Destination**: /etc/motd (all servers)
- **Transfer Method**: SCP (secure copy)
- **Deployment**: Manual move to /etc/motd
- **Verification**: Login test on all servers

**🔍 Key Learning Points**:
- `/etc/motd` is the standard Message of the Day file
- MOTD displays after authentication, before shell prompt
- SCP is used for secure file transfer between servers
- Sudo required to modify system files like /etc/motd
- Verification requires actual login test, not just file check
- Compliance banners protect organization legally
