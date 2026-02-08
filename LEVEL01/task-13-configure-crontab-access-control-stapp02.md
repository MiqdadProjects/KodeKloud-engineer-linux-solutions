# 🔐 Task 13 - Configure Crontab Access Control on App Server 2

**📌 Task Description**

In alignment with security compliance standards, the **Nautilus project team** has opted to impose restrictions on crontab access. Specifically, only designated users will be permitted to create or update cron jobs. The `/etc/cron.allow` and `/etc/cron.deny` files provide granular control over which users can schedule automated tasks, implementing the principle of least privilege for system automation.

**👉 Task Requirements**:
- **Target Server**: App Server 2 (stapp02) in Stratos Datacenter
- **Access Control Configuration**:
  - **Allow**: User `siva` (permitted to use crontab)
  - **Deny**: User `rod` (prohibited from using crontab)
- **Configuration Files**:
  - `/etc/cron.allow`: Whitelist of allowed users
  - `/etc/cron.deny`: Blacklist of denied users
- **Operation Objectives**:
  - Implement crontab access control
  - Grant crontab access to specific user
  - Deny crontab access to specific user
  - Verify access control configuration
- Configure user-based crontab restrictions
- Enforce security compliance for automated tasks
- Verify access control is working

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to App Server 2

```bash
ssh steve@stapp02
```

**Purpose**: Establish SSH connection to App Server 2 where crontab access control will be configured.

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

## 🔹 Step 2: Verify Users Exist

```bash
id siva
id rod
```

**Purpose**: Confirm that both users exist on the system before configuring access.

**Expected Output**:
```
uid=1006(siva) gid=1006(siva) groups=1006(siva)
uid=1007(rod) gid=1007(rod) groups=1007(rod)
```

**Success Indicators**:
- ✅ User `siva` exists
- ✅ User `rod` exists
- ✅ Ready to configure access control

**Note**: If users don't exist, create them first with `sudo useradd username`.

---

## 🔹 Step 3: Check Existing Cron Access Files (Optional)

```bash
ls -l /etc/cron.allow /etc/cron.deny 2>/dev/null
```

**Purpose**: Check if cron access control files already exist.

**Expected Output** (if files don't exist):
```
ls: cannot access '/etc/cron.allow': No such file or directory
ls: cannot access '/etc/cron.deny': No such file or directory
```

**Success Indicators**:
- ✅ Files may or may not exist
- ✅ Ready to create or modify files

---

## 🔹 Step 4: Add User siva to cron.allow

```bash
echo "siva" | sudo tee /etc/cron.allow
```

**Purpose**: Create `/etc/cron.allow` and add user `siva` to allow crontab access.

**Command Breakdown**:
- `echo "siva"`: Output the username
- `|`: Pipe to next command
- `sudo tee`: Write to file with administrative privileges
- `/etc/cron.allow`: Crontab allow list file

**Expected Output**:
```
siva
```

**Success Indicators**:
- ✅ File `/etc/cron.allow` created
- ✅ User `siva` added to allow list
- ✅ Output shows username was written

**⚠️ Important**: If `/etc/cron.allow` exists, only users listed in it can use crontab. All others are denied.

---

## 🔹 Step 5: Add User rod to cron.deny

```bash
echo "rod" | sudo tee /etc/cron.deny
```

**Purpose**: Create `/etc/cron.deny` and add user `rod` to deny crontab access.

**Command Breakdown**:
- `echo "rod"`: Output the username
- `|`: Pipe to next command
- `sudo tee`: Write to file with administrative privileges
- `/etc/cron.deny`: Crontab deny list file

**Expected Output**:
```
rod
```

**Success Indicators**:
- ✅ File `/etc/cron.deny` created
- ✅ User `rod` added to deny list
- ✅ Output shows username was written

**⚠️ Important**: `/etc/cron.deny` is only checked if `/etc/cron.allow` doesn't exist or doesn't contain the user.

---

## 🔹 Step 6: Verify cron.allow Contents

```bash
cat /etc/cron.allow
```

**Purpose**: Confirm user `siva` is listed in the allow file.

**Expected Output**:
```
siva
```

**Success Indicators**:
- ✅ File contains `siva`
- ✅ Allow list configured correctly
- ✅ User `siva` can use crontab

---

## 🔹 Step 7: Verify cron.deny Contents

```bash
cat /etc/cron.deny
```

**Purpose**: Confirm user `rod` is listed in the deny file.

**Expected Output**:
```
rod
```

**Success Indicators**:
- ✅ File contains `rod`
- ✅ Deny list configured correctly
- ✅ User `rod` cannot use crontab

---

## 🔹 Step 8: Check File Permissions

```bash
ls -l /etc/cron.allow /etc/cron.deny
```

**Purpose**: Verify proper permissions on cron access control files.

**Expected Output**:
```
-rw-r--r-- 1 root root 5 Feb 08 14:00 /etc/cron.allow
-rw-r--r-- 1 root root 4 Feb 08 14:00 /etc/cron.deny
```

**Success Indicators**:
- ✅ Files owned by root
- ✅ Permissions are 644 (readable by all, writable by root)
- ✅ Files are properly secured

---

## 🔹 Step 9: Test Crontab Access for User siva

```bash
sudo -u siva crontab -l
```

**Purpose**: Verify user `siva` can access crontab.

**Expected Output**:
```
no crontab for siva
```

**Success Indicators**:
- ✅ No permission denied error
- ✅ User `siva` can access crontab
- ✅ Allow list is working

**Note**: "no crontab for siva" means the user has no cron jobs yet, but has access to create them.

---

## 🔹 Step 10: Test Crontab Access for User rod

```bash
sudo -u rod crontab -l
```

**Purpose**: Verify user `rod` is denied crontab access.

**Expected Output**:
```
You (rod) are not allowed to use this program (crontab)
See crontab(1) for more information
```

**Success Indicators**:
- ✅ Permission denied error appears
- ✅ User `rod` cannot access crontab
- ✅ Deny list is working

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **App Server 2**:

```bash
# Connect to App Server 2
ssh steve@stapp02

# Verify users exist
id siva
id rod

# Check existing cron access files (optional)
ls -l /etc/cron.allow /etc/cron.deny 2>/dev/null

# Add user siva to cron.allow
echo "siva" | sudo tee /etc/cron.allow

# Add user rod to cron.deny
echo "rod" | sudo tee /etc/cron.deny

# Verify cron.allow contents
cat /etc/cron.allow

# Verify cron.deny contents
cat /etc/cron.deny

# Check file permissions
ls -l /etc/cron.allow /etc/cron.deny

# Test crontab access for siva (should work)
sudo -u siva crontab -l

# Test crontab access for rod (should be denied)
sudo -u rod crontab -l
```

---

## 💡 Common Crontab Access Control Issues & Fixes

### **Issue 1: cron.allow Takes Precedence**
**Problem**: User in cron.deny can still use crontab.
**Fix**: Understand that cron.allow takes precedence over cron.deny.
```bash
# If cron.allow exists, ONLY users in it can use crontab
# cron.deny is ignored when cron.allow exists

# To deny rod when cron.allow exists:
# Simply don't add rod to cron.allow

# Verify
cat /etc/cron.allow  # Should only contain siva
```

### **Issue 2: Multiple Users in Files**
**Problem**: Need to add multiple users to allow or deny lists.
**Fix**: Add one username per line.
```bash
# Add multiple users to cron.allow
echo "siva" | sudo tee /etc/cron.allow
echo "admin" | sudo tee -a /etc/cron.allow  # Note: -a to append

# Add multiple users to cron.deny
echo "rod" | sudo tee /etc/cron.deny
echo "guest" | sudo tee -a /etc/cron.deny

# Verify
cat /etc/cron.allow
cat /etc/cron.deny
```

### **Issue 3: File Overwritten Instead of Appended**
**Problem**: Using `tee` without `-a` overwrites file.
**Fix**: Use `tee -a` to append to existing file.
```bash
# WRONG - Overwrites file
echo "user2" | sudo tee /etc/cron.allow

# CORRECT - Appends to file
echo "user2" | sudo tee -a /etc/cron.allow

# Verify
cat /etc/cron.allow
```

### **Issue 4: Root User Access**
**Problem**: Wondering if root is affected by cron.allow/deny.
**Fix**: Root always has crontab access regardless of these files.
```bash
# Root is never restricted
sudo crontab -l  # Always works

# Regular users are restricted
sudo -u rod crontab -l  # Denied if in cron.deny
```

### **Issue 5: Changes Not Taking Effect**
**Problem**: Access control not working after configuration.
**Fix**: Restart cron service (usually not needed, but can help).
```bash
# Restart cron service
sudo systemctl restart crond  # RHEL/CentOS
sudo systemctl restart cron   # Debian/Ubuntu

# Verify service is running
sudo systemctl status crond
```

---

## 🔧 Troubleshooting Failures

### **Error: Permission Denied Creating Files**
**Symptoms**: Cannot create `/etc/cron.allow` or `/etc/cron.deny`.
**Solution**: Use sudo to create files in /etc directory.
```bash
# Always use sudo for /etc files
echo "siva" | sudo tee /etc/cron.allow
echo "rod" | sudo tee /etc/cron.deny
```

### **Error: User Not Found**
**Symptoms**: User doesn't exist when testing access.
**Solution**: Create users before configuring access control.
```bash
# Create users if they don't exist
sudo useradd siva
sudo useradd rod

# Verify users exist
id siva
id rod

# Configure access control
echo "siva" | sudo tee /etc/cron.allow
echo "rod" | sudo tee /etc/cron.deny
```

### **Error: Both Users Can Access Crontab**
**Symptoms**: User `rod` can still use crontab despite being in cron.deny.
**Solution**: Ensure cron.allow doesn't contain `rod`.
```bash
# Check cron.allow
cat /etc/cron.allow

# If rod is in cron.allow, remove it
sudo sed -i '/^rod$/d' /etc/cron.allow

# Verify
cat /etc/cron.allow
```

### **Error: No Users Can Access Crontab**
**Symptoms**: Even allowed users cannot use crontab.
**Solution**: Verify cron.allow contains correct usernames.
```bash
# Check cron.allow contents
cat /etc/cron.allow

# Ensure usernames are correct (one per line)
echo "siva" | sudo tee /etc/cron.allow

# Test access
sudo -u siva crontab -l
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The primary challenge was implementing granular crontab access control using `/etc/cron.allow` and `/etc/cron.deny` files. Understanding the precedence rules (cron.allow takes priority) and proper file configuration is crucial for effective access control.

**💡 Solution Approach**:
1. **User Verification**: Confirmed both users exist on the system.
2. **Allow List Creation**: Created `/etc/cron.allow` with user `siva`.
3. **Deny List Creation**: Created `/etc/cron.deny` with user `rod`.
4. **Verification**: Confirmed file contents with `cat` command.
5. **Access Testing**: Tested crontab access for both users to verify configuration.

**🎯 Key Success Factors**:
- **Correct File Paths**: Used `/etc/cron.allow` and `/etc/cron.deny`.
- **Proper Syntax**: One username per line in access control files.
- **Precedence Understanding**: Knew that cron.allow takes priority.
- **Verification**: Tested actual crontab access for both users.
- **File Creation**: Used `tee` with sudo for proper permissions.

**⚠️ Critical Fixes**:
- Always use sudo when creating files in /etc directory.
- Understand that cron.allow takes precedence over cron.deny.
- Use one username per line in access control files.
- Test actual crontab access to verify configuration.
- Use `tee -a` to append, not overwrite existing files.

**🔒 Linux Crontab Access Control Best Practices Applied**:
- **Whitelist Approach**: Used cron.allow for explicit permission grants.
- **Blacklist Approach**: Used cron.deny for explicit denials.
- **Least Privilege**: Only authorized users can schedule automated tasks.
- **Verification**: Tested access for both allowed and denied users.
- **Documentation**: Clear configuration in well-known system files.

**⚠️ Important Crontab Access Control Concepts**:
- **Precedence**: If `/etc/cron.allow` exists, only users in it can use crontab.
- **Deny List**: `/etc/cron.deny` is only checked if user is not in cron.allow.
- **Root Exception**: Root always has crontab access.
- **File Format**: One username per line, no comments or extra characters.
- **Default Behavior**: If neither file exists, all users can use crontab.

---

## ⚠️ Important Production Notes

🔧 **Precedence Rules**: cron.allow takes absolute precedence over cron.deny.
🔐 **Root Access**: Root user is never restricted by cron.allow or cron.deny.
📊 **File Format**: One username per line, no wildcards or patterns.
🛡️ **Security**: Use cron.allow for whitelist approach (more secure).
🗄️ **Backup**: Backup access control files before modifications.
📝 **Documentation**: Document which users have crontab access and why.
🔍 **Regular Audits**: Review crontab access lists periodically.
⚙️ **Automation**: Use configuration management for consistent access control.

**Crontab Access Control Logic**:
```
Decision Flow:
1. Is user root? → Allow (always)
2. Does /etc/cron.allow exist?
   - Yes: Is user in cron.allow? → Allow, else Deny
   - No: Continue to step 3
3. Does /etc/cron.deny exist?
   - Yes: Is user in cron.deny? → Deny, else Allow
   - No: Allow (default)
```

**File Management Commands**:
```bash
# Create cron.allow with single user
echo "username" | sudo tee /etc/cron.allow

# Append user to cron.allow
echo "username" | sudo tee -a /etc/cron.allow

# Add multiple users at once
sudo tee /etc/cron.allow <<EOF
user1
user2
user3
EOF

# Remove user from cron.allow
sudo sed -i '/^username$/d' /etc/cron.allow

# Clear entire file
sudo truncate -s 0 /etc/cron.allow

# Delete file
sudo rm /etc/cron.allow
```

**Access Control Strategies**:
```bash
# Strategy 1: Whitelist only (most secure)
# Create cron.allow with authorized users
# Don't create cron.deny
echo "admin" | sudo tee /etc/cron.allow
echo "sysadmin" | sudo tee -a /etc/cron.allow

# Strategy 2: Blacklist specific users
# Don't create cron.allow (allows all by default)
# Create cron.deny with restricted users
echo "guest" | sudo tee /etc/cron.deny
echo "temp" | sudo tee -a /etc/cron.deny

# Strategy 3: Combined (cron.allow takes precedence)
# Use cron.allow for explicit permissions
# Use cron.deny as documentation only
```

**Security Recommendations**:
- Use whitelist approach (cron.allow) for production systems
- Regularly audit users in cron.allow
- Remove access for users who no longer need it
- Document business justification for crontab access
- Monitor crontab usage through system logs
- Implement approval process for crontab access requests
- Use configuration management to enforce consistent access control
- Backup cron access control files in version control

**Crontab Security Best Practices**:
```bash
# Restrict crontab file permissions
sudo chmod 644 /etc/cron.allow
sudo chmod 644 /etc/cron.deny

# Monitor crontab changes
sudo auditctl -w /etc/cron.allow -p wa -k cron_access
sudo auditctl -w /etc/cron.deny -p wa -k cron_access

# Review active crontabs
for user in $(cut -f1 -d: /etc/passwd); do
  sudo crontab -u $user -l 2>/dev/null && echo "User: $user"
done

# List users with crontab access
if [ -f /etc/cron.allow ]; then
  echo "Allowed users:"
  cat /etc/cron.allow
fi
```

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Configured crontab access control on App Server 2
- Granted crontab access to user `siva` via `/etc/cron.allow`
- Denied crontab access to user `rod` via `/etc/cron.deny`
- Verified access control configuration
- Tested actual crontab access for both users

**🔍 Verification Completed**:
- Users exist: `siva` and `rod` confirmed
- cron.allow created: Contains `siva`
- cron.deny created: Contains `rod`
- File permissions: Both files owned by root with 644 permissions
- Access test for siva: Successful (no permission error)
- Access test for rod: Denied (permission error displayed)

**🚀 Next Steps** (Optional):
- Document crontab access policy
- Implement monitoring for crontab usage
- Add additional users to allow/deny lists as needed
- Set up audit logging for crontab access attempts
- Create automated reports of crontab access
- Implement approval workflow for crontab access requests

**🔒 Security Validation**:
- ✅ Only authorized user `siva` can create cron jobs
- ✅ User `rod` is explicitly denied crontab access
- ✅ Access control files properly secured (root owned)
- ✅ Whitelist approach implemented with cron.allow
- ✅ Compliance with security standards achieved
- ✅ Principle of least privilege enforced

**📊 Configuration Summary**:
- **File**: `/etc/cron.allow`
  - **Contents**: `siva`
  - **Effect**: Only `siva` can use crontab
- **File**: `/etc/cron.deny`
  - **Contents**: `rod`
  - **Effect**: `rod` explicitly denied (redundant with cron.allow)
- **Access Control**: Whitelist approach active
- **Root Access**: Unaffected (always allowed)

**🔍 Key Learning Points**:
- `/etc/cron.allow` takes absolute precedence over `/etc/cron.deny`
- If cron.allow exists, only users listed in it can use crontab
- Root user always has crontab access regardless of these files
- One username per line in access control files
- Use `tee` with sudo to create files in /etc directory
- Test actual access to verify configuration works
