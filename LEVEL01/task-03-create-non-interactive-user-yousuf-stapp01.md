# 🔐 Task 3 - Create Non-Interactive Shell User on App Server 1

**📌 Task Description**

To accommodate the backup agent tool's specifications, the system admin team at **xFusionCorp Industries** requires the creation of a user with a non-interactive shell. Non-interactive users are essential for service accounts, automated processes, and security tools that need system access without allowing interactive login sessions. This approach enhances security by preventing unauthorized shell access while still enabling automated operations.

**👉 Task Requirements**:
- **Target Server**: App Server 1 (stapp01) in Stratos Datacenter
- **User Configuration**:
  - Username: `yousuf`
  - Shell: `/sbin/nologin` (non-interactive)
  - Purpose: Backup agent tool service account
- **Security Objectives**:
  - Prevent interactive shell login
  - Enable automated process execution
  - Restrict unauthorized access
  - Support backup agent operations
- Verify user creation with correct non-interactive shell
- Confirm user cannot login interactively
- Ensure proper system account configuration

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to App Server 1

```bash
ssh tony@stapp01
```

**Purpose**: Establish SSH connection to App Server 1 where the non-interactive user will be created.

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

## 🔹 Step 2: Verify User Does Not Exist

```bash
id yousuf
```

**Purpose**: Confirm that the user `yousuf` does not already exist before creation.

**Expected Output**:
```
id: 'yousuf': no such user
```

**Success Indicators**:
- ✅ Error message confirms user does not exist
- ✅ Safe to proceed with user creation

**Alternative Check**:
```bash
getent passwd yousuf
```

**Expected Output** (no output indicates user doesn't exist):
```

```

---

## 🔹 Step 3: Create User with Non-Interactive Shell

```bash
sudo useradd -s /sbin/nologin yousuf
```

**Purpose**: Create the user `yousuf` with a non-interactive shell to prevent login sessions.

**Command Breakdown**:
- `sudo`: Execute with administrative privileges
- `useradd`: User creation command
- `-s /sbin/nologin`: Set shell to `/sbin/nologin` (prevents interactive login)
- `yousuf`: Username to create

**Expected Output**:
```
[no output indicates success]
```

**Success Indicators**:
- ✅ User created successfully
- ✅ Shell set to `/sbin/nologin`
- ✅ User cannot login interactively

**⚠️ Important**: The `/sbin/nologin` shell prevents interactive login but allows the user to run automated processes and services.

---

## 🔹 Step 4: Verify User Creation

```bash
id yousuf
```

**Purpose**: Confirm the user was created with correct UID and group assignments.

**Expected Output**:
```
uid=1003(yousuf) gid=1003(yousuf) groups=1003(yousuf)
```

**Success Indicators**:
- ✅ User `yousuf` exists with valid UID
- ✅ Primary group created automatically: `yousuf`
- ✅ User has system identity

**Note**: The UID value (1003) may vary depending on existing users on the system.

---

## 🔹 Step 5: Verify Non-Interactive Shell Configuration

```bash
getent passwd yousuf
```

**Purpose**: Confirm the user entry in the password database with correct shell assignment.

**Expected Output**:
```
yousuf:x:1003:1003::/home/yousuf:/sbin/nologin
```

**Field Breakdown**:
- `yousuf`: Username
- `x`: Password placeholder (stored in `/etc/shadow`)
- `1003`: UID (User ID)
- `1003`: GID (Group ID)
- ``: Comment field (empty)
- `/home/yousuf`: Home directory
- `/sbin/nologin`: Shell (non-interactive)

**Success Indicators**:
- ✅ Shell is set to `/sbin/nologin`
- ✅ User has home directory
- ✅ User entry is properly configured

---

## 🔹 Step 6: Verify Shell Assignment

```bash
grep "^yousuf:" /etc/passwd | cut -d: -f7
```

**Purpose**: Extract and verify only the shell field from the user's password entry.

**Expected Output**:
```
/sbin/nologin
```

**Success Indicators**:
- ✅ Shell is exactly `/sbin/nologin`
- ✅ Non-interactive shell is properly configured

**Alternative Verification**:
```bash
getent passwd yousuf | awk -F: '{print $7}'
```

**Expected Output**:
```
/sbin/nologin
```

---

## 🔹 Step 7: Test Non-Interactive Shell (Optional)

```bash
sudo su - yousuf
```

**Purpose**: Attempt to switch to the user to verify non-interactive shell behavior.

**Expected Output**:
```
This account is currently not available.
```

**Success Indicators**:
- ✅ Login attempt is denied
- ✅ Message indicates account is not available for interactive use
- ✅ Non-interactive shell is working correctly

**Note**: This confirms the user cannot obtain an interactive shell session, which is the desired security behavior.

---

## 🔹 Step 8: Verify User in System Files

```bash
sudo cat /etc/passwd | grep yousuf
```

**Purpose**: Confirm user entry exists in the system password database.

**Expected Output**:
```
yousuf:x:1003:1003::/home/yousuf:/sbin/nologin
```

**Success Indicators**:
- ✅ User entry is present in `/etc/passwd`
- ✅ Shell field shows `/sbin/nologin`
- ✅ System recognizes the user

---

## 🔹 Step 9: Check User's Home Directory

```bash
ls -ld /home/yousuf
```

**Purpose**: Verify the home directory was created with proper ownership.

**Expected Output**:
```
drwx------. 2 yousuf yousuf 62 Feb 08 12:00 /home/yousuf
```

**Success Indicators**:
- ✅ Directory exists at `/home/yousuf`
- ✅ Owner: `yousuf`
- ✅ Group: `yousuf`
- ✅ Permissions: `drwx------` (700) - owner has full access

**Note**: Home directory is created by default even though user cannot login interactively. This allows processes running as this user to have a working directory.

---

## 🔹 Step 10: Verify User Cannot Login via SSH

```bash
# This command would be run from another terminal or machine
ssh yousuf@stapp01
```

**Purpose**: Confirm the user cannot establish SSH sessions.

**Expected Output**:
```
yousuf@stapp01's password: 
This account is currently not available.
Connection to stapp01 closed.
```

**Success Indicators**:
- ✅ SSH connection is rejected
- ✅ Non-interactive shell prevents login
- ✅ Security objective achieved

**Note**: Even if a password is set, the user cannot login due to the `/sbin/nologin` shell.

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **App Server 1**:

```bash
# Connect to App Server 1
ssh tony@stapp01

# Verify user doesn't exist
id yousuf

# Create user with non-interactive shell
sudo useradd -s /sbin/nologin yousuf

# Verify user creation
id yousuf

# Verify shell configuration
getent passwd yousuf

# Verify shell field
grep "^yousuf:" /etc/passwd | cut -d: -f7

# Test non-interactive shell (should deny access)
sudo su - yousuf

# Verify home directory
ls -ld /home/yousuf
```

---

## 💡 Common Non-Interactive User Issues & Fixes

### **Issue 1: Wrong Shell Path**
**Problem**: Shell set to `/usr/sbin/nologin` instead of `/sbin/nologin`.
**Fix**: Both paths are valid, but `/sbin/nologin` is more common. Verify the path exists.
```bash
# Check if shell exists
ls -l /sbin/nologin
ls -l /usr/sbin/nologin

# Update shell if needed
sudo usermod -s /sbin/nologin yousuf
```

### **Issue 2: User Can Still Login**
**Problem**: User can login despite `/sbin/nologin` shell.
**Fix**: Verify shell is correctly set and the nologin binary exists.
```bash
# Verify shell assignment
getent passwd yousuf | cut -d: -f7

# Verify nologin exists
which nologin
ls -l /sbin/nologin

# Update shell if incorrect
sudo usermod -s /sbin/nologin yousuf
```

### **Issue 3: Home Directory Not Created**
**Problem**: Home directory doesn't exist.
**Fix**: Create home directory manually or use `-m` flag during user creation.
```bash
# Create home directory
sudo mkdir -p /home/yousuf

# Set ownership
sudo chown yousuf:yousuf /home/yousuf

# Set permissions
sudo chmod 700 /home/yousuf
```

### **Issue 4: User Needs Different Shell**
**Problem**: Requirement changed to use `/bin/false` instead of `/sbin/nologin`.
**Fix**: Update user's shell using `usermod`.
```bash
# Change to /bin/false
sudo usermod -s /bin/false yousuf

# Verify change
getent passwd yousuf
```

**Shell Comparison**:
- `/sbin/nologin`: Displays polite message, logs attempt, recommended for service accounts
- `/bin/false`: Silently exits, no message, more restrictive
- `/bin/true`: Exits successfully, rarely used

### **Issue 5: Permission Denied**
**Problem**: Cannot create user without sudo.
**Fix**: Ensure using sudo for administrative commands.
```bash
# Always use sudo for user management
sudo useradd -s /sbin/nologin yousuf
```

---

## 🔧 Troubleshooting Failures

### **Error: User Already Exists**
**Symptoms**: `useradd: user 'yousuf' already exists`.
**Solution**: Check existing user and update shell if needed.
```bash
# Verify existing user
id yousuf
getent passwd yousuf

# Update shell to non-interactive
sudo usermod -s /sbin/nologin yousuf

# Verify change
getent passwd yousuf
```

### **Error: Invalid Shell**
**Symptoms**: Shell path doesn't exist or is invalid.
**Solution**: Verify shell exists and is listed in `/etc/shells`.
```bash
# Check valid shells
cat /etc/shells

# Verify nologin exists
ls -l /sbin/nologin

# If nologin doesn't exist, use alternative
sudo useradd -s /bin/false yousuf
```

### **Error: Cannot Create Home Directory**
**Symptoms**: Permission denied when creating home directory.
**Solution**: Ensure parent directory exists and has correct permissions.
```bash
# Verify /home exists
ls -ld /home

# Create user without home directory first
sudo useradd -s /sbin/nologin -M yousuf

# Create home directory manually
sudo mkdir -p /home/yousuf
sudo chown yousuf:yousuf /home/yousuf
sudo chmod 700 /home/yousuf
```

### **Error: User Can Still Execute Commands**
**Symptoms**: User can run commands despite non-interactive shell.
**Solution**: This is expected behavior - non-interactive shell prevents login but allows process execution.
```bash
# Non-interactive users can still run processes
sudo -u yousuf whoami
# Output: yousuf

# But cannot get interactive shell
sudo su - yousuf
# Output: This account is currently not available.
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The primary challenge was creating a service account user that prevents interactive login while still allowing automated processes (like backup agents) to execute under that user's context. This requires understanding the difference between login shells and process execution.

**💡 Solution Approach**:
1. **Non-Interactive Shell Selection**: Used `/sbin/nologin` as the shell to prevent interactive login sessions.
2. **User Creation**: Created user with `useradd -s /sbin/nologin` to set shell during creation.
3. **Verification**: Confirmed shell assignment using `getent passwd` and `id` commands.
4. **Login Test**: Attempted `su - yousuf` to verify non-interactive behavior.
5. **System Validation**: Checked `/etc/passwd` to ensure correct configuration.

**🎯 Key Success Factors**:
- **Correct Shell Path**: Used `/sbin/nologin` which is the standard non-interactive shell.
- **Proper Shell Assignment**: Set shell during user creation with `-s` flag.
- **Home Directory**: Allowed default home directory creation for process working directory.
- **Verification**: Confirmed non-interactive behavior by testing login attempt.
- **Security Objective**: Achieved goal of preventing interactive access while enabling process execution.

**⚠️ Critical Fixes**:
- Always verify shell path exists before assigning it to a user.
- Use `/sbin/nologin` instead of `/bin/false` for service accounts (provides better logging and user feedback).
- Test non-interactive behavior with `su - username` to confirm configuration.
- Understand that non-interactive shell prevents login but allows process execution.

**🔒 Linux Non-Interactive User Best Practices Applied**:
- **Service Account Pattern**: Created user specifically for automated processes, not human login.
- **Shell Restriction**: Used `/sbin/nologin` to prevent interactive shell access.
- **Home Directory**: Maintained home directory for process working directory needs.
- **Minimal Permissions**: User has only necessary system access without login capability.
- **Security Isolation**: Prevents unauthorized interactive access while enabling automation.

**⚠️ Important Security Concepts**:
- **Non-Interactive Shell**: Prevents login but allows process execution under user context.
- **Service Accounts**: Dedicated users for applications and automated processes.
- **Login Prevention**: `/sbin/nologin` displays message and prevents shell access.
- **Process Execution**: User can still run processes via `sudo -u` or service managers.
- **Audit Trail**: Login attempts are logged even when denied.

---

## ⚠️ Important Production Notes

🔧 **Shell Selection**: Use `/sbin/nologin` for service accounts (better than `/bin/false` for logging and user feedback).
🔐 **Password Policy**: Service accounts typically don't need passwords unless required for specific authentication.
📊 **UID Range**: Consider using system UID range (below 1000) for service accounts: `useradd -r -s /sbin/nologin yousuf`.
🛡️ **Permission Hardening**: Service accounts should have minimal permissions and group memberships.
🗄️ **Backup Considerations**: Include `/etc/passwd` and `/etc/shadow` in backup strategies.
📝 **Audit Trail**: Monitor login attempts for non-interactive users to detect unauthorized access attempts.
🔍 **Regular Audits**: Periodically review service accounts to identify unused or orphaned accounts.
⚙️ **Automation**: Use configuration management tools (Ansible, Puppet) for consistent service account creation.

**Service Account Best Practices**:
- Use descriptive usernames that indicate the service or application
- Document the purpose of each service account
- Set appropriate file ownership for application files
- Use dedicated groups for service accounts
- Implement least privilege access control
- Regularly audit service account permissions
- Disable or remove unused service accounts

**Backup Agent Integration Notes**:
- Configure backup agent to run processes as user `yousuf`
- Set file ownership for backup directories to `yousuf:yousuf`
- Grant necessary read permissions for backup source directories
- Configure backup agent service to use `User=yousuf` in systemd unit files
- Ensure backup destination directories are writable by `yousuf`
- Monitor backup agent logs for permission issues

**Security Recommendations**:
- Never set passwords for service accounts unless absolutely required
- Use SSH key authentication if remote process execution is needed
- Implement sudo rules for specific commands if elevated privileges are required
- Apply SELinux contexts for service account processes if using SELinux
- Monitor `/var/log/secure` for unauthorized login attempts
- Use `systemd` user units for service account process management
- Implement file system ACLs for granular permission control

**Alternative Non-Interactive Shells**:
- `/sbin/nologin`: Standard, displays message, logs attempts (recommended)
- `/bin/false`: Silently exits with error code, more restrictive
- `/bin/true`: Silently exits with success code, rarely used
- `/usr/sbin/nologin`: Alternative path on some distributions

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Created user `yousuf` on App Server 1 (stapp01)
- Configured non-interactive shell (`/sbin/nologin`)
- Verified user cannot login interactively
- Confirmed user can be used for automated processes
- Implemented secure service account configuration

**🔍 Verification Completed**:
- User exists: `id yousuf`
- Shell is non-interactive: `getent passwd yousuf`
- Login is prevented: `sudo su - yousuf` (denied)
- Home directory created: `/home/yousuf`
- System configuration correct: `/etc/passwd` entry verified

**🚀 Next Steps** (Optional):
- Configure backup agent to run as user `yousuf`
- Set file ownership for backup directories
- Configure sudo rules if elevated privileges are needed
- Implement monitoring for backup agent processes
- Document service account purpose and usage
- Set up log rotation for backup agent logs

**🔒 Security Validation**:
- ✅ User cannot obtain interactive shell
- ✅ SSH login is prevented
- ✅ User can execute automated processes
- ✅ Home directory has restrictive permissions
- ✅ Service account follows security best practices
