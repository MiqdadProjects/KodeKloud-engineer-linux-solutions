# 🔐 Task 4 - Create Service User Without Home Directory on App Server 2

**📌 Task Description**

In response to the latest tool implementation at **xFusionCorp Industries**, the system admins require the creation of a service user account. Service users without home directories are commonly used for system services, daemons, and automated tools that don't require user-specific configuration files or persistent storage. This approach minimizes disk usage and reduces the attack surface by eliminating unnecessary directories.

**👉 Task Requirements**:
- **Target Server**: App Server 2 (stapp02) in Stratos Datacenter
- **User Configuration**:
  - Username: `ravi`
  - Home Directory: **None** (no home directory should be created)
  - Shell: `/sbin/nologin` (non-interactive)
  - Purpose: Service account for tool implementation
- **Security Objectives**:
  - Minimize disk footprint
  - Prevent interactive login
  - Create lightweight service account
  - Reduce attack surface
- Verify user creation without home directory
- Confirm non-interactive shell configuration
- Ensure proper service account setup

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to App Server 2

```bash
ssh steve@stapp02
```

**Purpose**: Establish SSH connection to App Server 2 where the service user will be created.

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

## 🔹 Step 2: Verify User Does Not Exist

```bash
id ravi
```

**Purpose**: Confirm that the user `ravi` does not already exist before creation.

**Expected Output**:
```
id: 'ravi': no such user
```

**Success Indicators**:
- ✅ Error message confirms user does not exist
- ✅ Safe to proceed with user creation

**Alternative Check**:
```bash
getent passwd ravi
```

**Expected Output** (no output indicates user doesn't exist):
```

```

---

## 🔹 Step 3: Create Service User Without Home Directory

```bash
sudo useradd -M -s /sbin/nologin ravi
```

**Purpose**: Create the user `ravi` without a home directory and with a non-interactive shell.

**Command Breakdown**:
- `sudo`: Execute with administrative privileges
- `useradd`: User creation command
- `-M`: Do **NOT** create home directory (opposite of `-m`)
- `-s /sbin/nologin`: Set shell to `/sbin/nologin` (prevents interactive login)
- `ravi`: Username to create

**Expected Output**:
```
[no output indicates success]
```

**Success Indicators**:
- ✅ User created successfully
- ✅ No home directory created
- ✅ Shell set to `/sbin/nologin`

**⚠️ Important**: The `-M` flag explicitly prevents home directory creation, making this a minimal service account.

---

## 🔹 Step 4: Verify User Creation

```bash
id ravi
```

**Purpose**: Confirm the user was created with correct UID and group assignments.

**Expected Output**:
```
uid=1004(ravi) gid=1004(ravi) groups=1004(ravi)
```

**Success Indicators**:
- ✅ User `ravi` exists with valid UID
- ✅ Primary group created automatically: `ravi`
- ✅ User has system identity

**Note**: The UID value (1004) may vary depending on existing users on the system.

---

## 🔹 Step 5: Verify User Configuration in Password Database

```bash
getent passwd ravi
```

**Purpose**: Confirm the user entry in the password database with correct shell and home directory settings.

**Expected Output**:
```
ravi:x:1004:1004::/home/ravi:/sbin/nologin
```

**Field Breakdown**:
- `ravi`: Username
- `x`: Password placeholder (stored in `/etc/shadow`)
- `1004`: UID (User ID)
- `1004`: GID (Group ID)
- ``: Comment field (empty)
- `/home/ravi`: Home directory path (defined but **NOT created**)
- `/sbin/nologin`: Shell (non-interactive)

**Success Indicators**:
- ✅ Shell is set to `/sbin/nologin`
- ✅ Home directory path is listed but directory doesn't exist
- ✅ User entry is properly configured

**⚠️ Important Note**: The home directory path appears in `/etc/passwd` but the actual directory does **NOT** exist on the filesystem due to the `-M` flag.

---

## 🔹 Step 6: Verify Home Directory Does NOT Exist

```bash
ls -ld /home/ravi
```

**Purpose**: Confirm that the home directory was NOT created.

**Expected Output**:
```
ls: cannot access '/home/ravi': No such file or directory
```

**Success Indicators**:
- ✅ Error message confirms directory does not exist
- ✅ `-M` flag worked correctly
- ✅ Service account has no home directory

**Alternative Verification**:
```bash
[ -d /home/ravi ] && echo "Directory exists" || echo "Directory does not exist"
```

**Expected Output**:
```
Directory does not exist
```

---

## 🔹 Step 7: Verify Non-Interactive Shell Configuration

```bash
grep "^ravi:" /etc/passwd | cut -d: -f7
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
getent passwd ravi | awk -F: '{print $7}'
```

**Expected Output**:
```
/sbin/nologin
```

---

## 🔹 Step 8: Test Non-Interactive Shell

```bash
sudo su - ravi
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

## 🔹 Step 9: Verify User in System Files

```bash
sudo cat /etc/passwd | grep ravi
```

**Purpose**: Confirm user entry exists in the system password database.

**Expected Output**:
```
ravi:x:1004:1004::/home/ravi:/sbin/nologin
```

**Success Indicators**:
- ✅ User entry is present in `/etc/passwd`
- ✅ Shell field shows `/sbin/nologin`
- ✅ System recognizes the user

---

## 🔹 Step 10: Verify User Can Execute Processes

```bash
sudo -u ravi whoami
```

**Purpose**: Confirm the user can execute processes even without a home directory.

**Expected Output**:
```
ravi
```

**Success Indicators**:
- ✅ User can execute commands
- ✅ Service account is functional for process execution
- ✅ No home directory is required for basic operations

**Note**: Service users without home directories can still run processes, which is essential for system services and daemons.

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **App Server 2**:

```bash
# Connect to App Server 2
ssh steve@stapp02

# Verify user doesn't exist
id ravi

# Create user without home directory and with non-interactive shell
sudo useradd -M -s /sbin/nologin ravi

# Verify user creation
id ravi

# Verify user configuration
getent passwd ravi

# Verify home directory does NOT exist
ls -ld /home/ravi

# Verify shell configuration
grep "^ravi:" /etc/passwd | cut -d: -f7

# Test non-interactive shell (should deny access)
sudo su - ravi

# Test process execution (should work)
sudo -u ravi whoami
```

---

## 💡 Common Service User Issues & Fixes

### **Issue 1: Home Directory Was Created**
**Problem**: Home directory exists despite using `-M` flag.
**Fix**: Remove the home directory or recreate user correctly.
```bash
# Remove existing home directory
sudo rm -rf /home/ravi

# Or recreate user with correct flags
sudo userdel ravi
sudo useradd -M -s /sbin/nologin ravi
```

### **Issue 2: User Cannot Execute Processes**
**Problem**: Processes fail when running as user `ravi`.
**Fix**: Verify user exists and check process requirements.
```bash
# Verify user exists
id ravi

# Test process execution
sudo -u ravi whoami

# Check if process requires home directory (some do)
# If needed, create minimal home directory
sudo mkdir -p /home/ravi
sudo chown ravi:ravi /home/ravi
```

### **Issue 3: Wrong Shell Assigned**
**Problem**: Shell is not `/sbin/nologin`.
**Fix**: Update user's shell using `usermod`.
```bash
# Verify current shell
getent passwd ravi | cut -d: -f7

# Update shell
sudo usermod -s /sbin/nologin ravi

# Verify change
getent passwd ravi
```

### **Issue 4: User Already Exists with Home Directory**
**Problem**: User exists with home directory from previous creation.
**Fix**: Remove user and recreate without home directory.
```bash
# Remove user and home directory
sudo userdel -r ravi

# Recreate user without home directory
sudo useradd -M -s /sbin/nologin ravi

# Verify no home directory
ls -ld /home/ravi
```

### **Issue 5: Service Requires Home Directory**
**Problem**: Application fails because it expects a home directory.
**Fix**: Create minimal home directory or use alternative working directory.
```bash
# Option 1: Create minimal home directory
sudo mkdir -p /home/ravi
sudo chown ravi:ravi /home/ravi

# Option 2: Use alternative working directory
# Configure service to use /var/lib/ravi or /tmp
sudo mkdir -p /var/lib/ravi
sudo chown ravi:ravi /var/lib/ravi
```

---

## 🔧 Troubleshooting Failures

### **Error: User Already Exists**
**Symptoms**: `useradd: user 'ravi' already exists`.
**Solution**: Check existing user and update configuration or remove and recreate.
```bash
# Verify existing user
id ravi
getent passwd ravi

# Option 1: Update existing user
sudo usermod -s /sbin/nologin ravi

# Option 2: Remove and recreate
sudo userdel ravi
sudo useradd -M -s /sbin/nologin ravi
```

### **Error: Home Directory Exists**
**Symptoms**: Home directory `/home/ravi` exists despite `-M` flag.
**Solution**: Remove directory or verify `-M` flag was used.
```bash
# Check if directory exists
ls -ld /home/ravi

# Remove if it exists
sudo rm -rf /home/ravi

# Verify user was created with -M flag
getent passwd ravi
```

### **Error: Process Fails Due to Missing Home**
**Symptoms**: Application or service fails with "no such file or directory" for home.
**Solution**: Some applications require home directory - create minimal one if needed.
```bash
# Create minimal home directory
sudo mkdir -p /home/ravi
sudo chown ravi:ravi /home/ravi
sudo chmod 700 /home/ravi

# Or configure application to use alternative directory
# Edit application config to use /var/lib/ravi or /tmp
```

### **Error: Permission Denied**
**Symptoms**: Cannot create user without sudo.
**Solution**: Ensure using sudo for administrative commands.
```bash
# Always use sudo for user management
sudo useradd -M -s /sbin/nologin ravi
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The primary challenge was creating a minimal service account without a home directory while ensuring the user can still execute processes for system services and tools. Understanding the difference between home directory path in `/etc/passwd` and actual directory existence is crucial.

**💡 Solution Approach**:
1. **No Home Directory Flag**: Used `-M` flag to explicitly prevent home directory creation.
2. **Non-Interactive Shell**: Combined with `-s /sbin/nologin` for security.
3. **Verification**: Confirmed home directory does NOT exist using `ls -ld /home/ravi`.
4. **Process Testing**: Verified user can still execute processes with `sudo -u ravi whoami`.
5. **Configuration Check**: Examined `/etc/passwd` to understand home path vs actual directory.

**🎯 Key Success Factors**:
- **Correct Flag Usage**: Used `-M` (uppercase) to prevent home directory creation.
- **Combined Security**: Applied both no-home and non-interactive shell for minimal footprint.
- **Proper Verification**: Checked both user existence and home directory absence.
- **Process Validation**: Confirmed service account functionality despite no home directory.
- **Understanding Behavior**: Recognized that home path in `/etc/passwd` doesn't mean directory exists.

**⚠️ Critical Fixes**:
- Always use uppercase `-M` flag to prevent home directory creation (lowercase `-m` creates it).
- Verify home directory does NOT exist with `ls -ld` after user creation.
- Understand that some services may still require a home directory - create minimal one if needed.
- Test process execution to ensure service account is functional.

**🔒 Linux Service User Best Practices Applied**:
- **Minimal Footprint**: No home directory reduces disk usage and attack surface.
- **Non-Interactive Shell**: Prevents unauthorized login attempts.
- **Service Account Pattern**: Created user specifically for system services, not human login.
- **Security Isolation**: Minimal configuration reduces potential security vulnerabilities.
- **Resource Efficiency**: No unnecessary files or directories created.

**⚠️ Important Security Concepts**:
- **Home Directory vs Path**: Path in `/etc/passwd` doesn't guarantee directory exists.
- **Service Account Minimalism**: Less is more - minimal configuration reduces risk.
- **Process Execution**: Users can run processes without home directories.
- **Working Directory**: Processes use current working directory or specified path.
- **Security Hardening**: No home directory means no user-specific config files to exploit.

---

## ⚠️ Important Production Notes

🔧 **Flag Awareness**: Use `-M` (uppercase) to prevent home directory, `-m` (lowercase) creates it.
🔐 **Home Directory Necessity**: Most system services don't need home directories, but verify application requirements.
📊 **Disk Efficiency**: Service users without home directories save disk space in large-scale deployments.
🛡️ **Security Hardening**: No home directory eliminates potential attack vectors from user config files.
🗄️ **Backup Considerations**: Service users without homes simplify backup strategies (fewer directories to backup).
📝 **Documentation**: Always document why a service user doesn't have a home directory.
🔍 **Application Testing**: Test service/application thoroughly to ensure it doesn't require home directory.
⚙️ **Alternative Directories**: Use `/var/lib/<service>` or `/var/run/<service>` for service-specific data.

**Service User Without Home Directory Use Cases**:
- System daemons and services (nginx, apache, mysql)
- Automated scripts and cron jobs
- CI/CD pipeline execution users
- Monitoring and logging agents
- Backup and synchronization tools
- Container runtime users
- API service accounts

**When Home Directory IS Required**:
- Users need SSH access with key-based authentication (requires `~/.ssh/`)
- Applications store configuration in `~/.config/`
- Services require temporary file storage in `~/tmp/`
- Applications use `~/.cache/` for caching
- User-specific environment variables in `~/.bashrc` or `~/.profile`

**Alternative Working Directories**:
```bash
# Use /var/lib for service-specific data
sudo mkdir -p /var/lib/ravi
sudo chown ravi:ravi /var/lib/ravi

# Use /var/run for runtime data
sudo mkdir -p /var/run/ravi
sudo chown ravi:ravi /var/run/ravi

# Use /tmp for temporary operations
# (automatically available, world-writable with sticky bit)
```

**Security Recommendations**:
- Never set passwords for service accounts unless absolutely required
- Use system UID range for service accounts: `useradd -r -M -s /sbin/nologin ravi`
- Implement sudo rules for specific commands if elevated privileges are required
- Apply SELinux contexts for service account processes if using SELinux
- Monitor `/var/log/secure` for unauthorized login attempts
- Use `systemd` user units for service account process management
- Regularly audit service accounts to identify unused ones

**systemd Service Integration**:
```ini
[Unit]
Description=Service running as ravi user

[Service]
Type=simple
User=ravi
Group=ravi
WorkingDirectory=/var/lib/ravi
ExecStart=/usr/local/bin/service-binary

[Install]
WantedBy=multi-user.target
```

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Created user `ravi` on App Server 2 (stapp02)
- Configured without home directory using `-M` flag
- Set non-interactive shell (`/sbin/nologin`)
- Verified user cannot login interactively
- Confirmed user can execute processes for service operations
- Implemented minimal service account configuration

**🔍 Verification Completed**:
- User exists: `id ravi`
- No home directory: `ls -ld /home/ravi` (returns error)
- Shell is non-interactive: `getent passwd ravi`
- Login is prevented: `sudo su - ravi` (denied)
- Process execution works: `sudo -u ravi whoami` (succeeds)
- System configuration correct: `/etc/passwd` entry verified

**🚀 Next Steps** (Optional):
- Configure service/tool to run as user `ravi`
- Create alternative working directory if needed (`/var/lib/ravi`)
- Set file ownership for service-specific files
- Configure systemd service unit with `User=ravi`
- Implement monitoring for service processes
- Document service account purpose and usage

**🔒 Security Validation**:
- ✅ User cannot obtain interactive shell
- ✅ SSH login is prevented
- ✅ No home directory reduces attack surface
- ✅ User can execute automated processes
- ✅ Minimal service account follows security best practices
- ✅ Disk footprint minimized

**📊 Comparison: With vs Without Home Directory**:

| Aspect | With Home (`-m`) | Without Home (`-M`) |
|--------|------------------|---------------------|
| Disk Usage | Higher (~100KB) | Minimal (~0KB) |
| Attack Surface | Larger (config files) | Smaller (no files) |
| SSH Key Auth | Possible (`~/.ssh/`) | Not possible |
| User Config | Supported (`~/.bashrc`) | Not supported |
| Use Case | Interactive users | Service accounts |
| Security | Standard | Hardened |
