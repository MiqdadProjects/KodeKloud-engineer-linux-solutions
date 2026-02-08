# 🔐 Task 17 - Set Process Limits for nfsuser on Storage Server

**📌 Task Description**

In the **Stratos Datacenter**, the Storage server is encountering performance degradation due to excessive processes held by the **nfsuser** user. To mitigate this issue, we need to enforce limitations on its maximum processes. The `/etc/security/limits.conf` file provides a mechanism to set resource limits for users and groups, implementing system-level controls to prevent resource exhaustion and ensure system stability.

**👉 Task Requirements**:
- **Target Server**: Storage Server (ststor01) in Stratos Datacenter
- **Target User**: `nfsuser`
- **Resource Limit**: Maximum number of processes (nproc)
- **Soft Limit**: 1027 processes
- **Hard Limit**: 2025 processes
- **Operation Objectives**:
  - Configure user process limits in limits.conf
  - Set soft limit to prevent normal operation issues
  - Set hard limit as absolute maximum
  - Verify limits are applied correctly
- Edit `/etc/security/limits.conf` file
- Apply limits for specific user
- Verify configuration with `ulimit` command

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to Storage Server

```bash
ssh natasha@ststor01
```

**Purpose**: Establish SSH connection to the Storage Server for resource limit configuration.

**Expected Output**:
```
natasha@ststor01's password: 
Welcome to Storage Server - Stratos Datacenter
[natasha@ststor01 ~]$
```

**Success Indicators**:
- ✅ Successfully authenticated as user `natasha`
- ✅ Command prompt shows `ststor01` hostname
- ✅ Ready to execute administrative commands

---

## 🔹 Step 2: Verify User Exists

```bash
id nfsuser
```

**Purpose**: Confirm the nfsuser exists on the system.

**Expected Output**:
```
uid=1008(nfsuser) gid=1008(nfsuser) groups=1008(nfsuser)
```

**Success Indicators**:
- ✅ User `nfsuser` exists
- ✅ User ID and group information displayed
- ✅ Ready to configure limits

---

## 🔹 Step 3: Check Current Process Limits (Optional)

```bash
sudo -u nfsuser bash -c 'ulimit -u'
```

**Purpose**: Check current soft limit for maximum processes before making changes.

**Expected Output** (sample):
```
unlimited
```

**Success Indicators**:
- ✅ Current limit displayed
- ✅ Baseline established for comparison

---

## 🔹 Step 4: Backup limits.conf File

```bash
sudo cp /etc/security/limits.conf /etc/security/limits.conf.backup
```

**Purpose**: Create backup of limits.conf before making changes.

**Expected Output**:
```
[no output indicates success]
```

**Success Indicators**:
- ✅ Backup created
- ✅ Can restore if needed
- ✅ Safety measure in place

---

## 🔹 Step 5: Edit limits.conf File

```bash
sudo vi /etc/security/limits.conf
```

**Purpose**: Open the limits configuration file for editing.

**Add the following lines at the end of the file**:
```
nfsuser    soft    nproc    1027
nfsuser    hard    nproc    2025
```

**Line Breakdown**:
- `nfsuser`: Username to apply limits to
- `soft`: Soft limit (warning threshold, can be increased up to hard limit)
- `hard`: Hard limit (absolute maximum, cannot be exceeded)
- `nproc`: Number of processes
- `1027`: Soft limit value (1027 processes)
- `2025`: Hard limit value (2025 processes)

**Vi Editor Commands**:
1. Press `Shift + G` to go to end of file
2. Press `o` to insert new line
3. Type: `nfsuser    soft    nproc    1027`
4. Press `Enter`
5. Type: `nfsuser    hard    nproc    2025`
6. Press `Esc`
7. Type `:wq` and press `Enter` to save and exit

**Success Indicators**:
- ✅ Lines added to end of file
- ✅ File saved successfully
- ✅ Configuration updated

---

## 🔹 Step 6: Verify limits.conf Configuration

```bash
sudo tail -5 /etc/security/limits.conf
```

**Purpose**: Confirm the limits were added correctly to the file.

**Expected Output**:
```
# End of file
nfsuser    soft    nproc    1027
nfsuser    hard    nproc    2025
```

**Success Indicators**:
- ✅ Both lines present in file
- ✅ Syntax is correct
- ✅ Configuration saved

---

## 🔹 Step 7: Verify Configuration with grep

```bash
sudo grep nfsuser /etc/security/limits.conf
```

**Purpose**: Search for nfsuser entries in limits.conf.

**Expected Output**:
```
nfsuser    soft    nproc    1027
nfsuser    hard    nproc    2025
```

**Success Indicators**:
- ✅ Both soft and hard limits configured
- ✅ Values are correct (1027 and 2025)
- ✅ Configuration complete

---

## 🔹 Step 8: Switch to nfsuser Account

```bash
sudo su - nfsuser
```

**Purpose**: Switch to nfsuser account to verify limits are applied.

**Expected Output**:
```
[nfsuser@ststor01 ~]$
```

**Success Indicators**:
- ✅ Successfully switched to nfsuser
- ✅ Prompt shows nfsuser
- ✅ Ready to check limits

**Note**: Limits are applied when user logs in, so switching to the user is necessary.

---

## 🔹 Step 9: Check Soft Limit for nfsuser

```bash
ulimit -u
```

**Purpose**: Display the soft limit for maximum user processes.

**Command Breakdown**:
- `ulimit`: Display or set resource limits
- `-u`: Maximum number of user processes

**Expected Output**:
```
1027
```

**Success Indicators**:
- ✅ Soft limit shows 1027
- ✅ Configuration applied correctly
- ✅ Soft limit is active

---

## 🔹 Step 10: Check Hard Limit for nfsuser

```bash
ulimit -Hu
```

**Purpose**: Display the hard limit for maximum user processes.

**Command Breakdown**:
- `ulimit`: Display or set resource limits
- `-H`: Display hard limit
- `-u`: Maximum number of user processes

**Expected Output**:
```
2025
```

**Success Indicators**:
- ✅ Hard limit shows 2025
- ✅ Configuration applied correctly
- ✅ Hard limit is active

---

## 🔹 Step 11: Display All Limits for nfsuser (Optional)

```bash
ulimit -a
```

**Purpose**: Display all resource limits for the current user.

**Expected Output** (partial):
```
core file size          (blocks, -c) 0
data seg size           (kbytes, -d) unlimited
scheduling priority             (-e) 0
file size               (blocks, -f) unlimited
pending signals                 (-i) 7862
max locked memory       (kbytes, -l) 64
max memory size         (kbytes, -m) unlimited
open files                      (-n) 1024
pipe size            (512 bytes, -p) 8
POSIX message queues     (bytes, -q) 819200
real-time priority              (-r) 0
stack size              (kbytes, -s) 8192
cpu time               (seconds, -t) unlimited
max user processes              (-u) 1027
virtual memory          (kbytes, -v) unlimited
file locks                      (-x) unlimited
```

**Success Indicators**:
- ✅ Max user processes shows 1027 (soft limit)
- ✅ All limits displayed
- ✅ Configuration verified

---

## 🔹 Step 12: Exit nfsuser Session

```bash
exit
```

**Purpose**: Return to natasha user session.

**Expected Output**:
```
[natasha@ststor01 ~]$
```

**Success Indicators**:
- ✅ Returned to original user
- ✅ Prompt shows natasha
- ✅ Task complete

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **Storage Server**:

```bash
# Connect to Storage Server
ssh natasha@ststor01

# Verify user exists
id nfsuser

# Check current limits (optional)
sudo -u nfsuser bash -c 'ulimit -u'

# Backup limits.conf
sudo cp /etc/security/limits.conf /etc/security/limits.conf.backup

# Edit limits.conf
sudo vi /etc/security/limits.conf
# Add these lines at the end:
# nfsuser    soft    nproc    1027
# nfsuser    hard    nproc    2025

# Verify configuration
sudo tail -5 /etc/security/limits.conf
sudo grep nfsuser /etc/security/limits.conf

# Switch to nfsuser
sudo su - nfsuser

# Check soft limit
ulimit -u

# Check hard limit
ulimit -Hu

# Display all limits (optional)
ulimit -a

# Exit nfsuser session
exit
```

---

## 💡 Common Process Limit Configuration Issues & Fixes

### **Issue 1: Limits Not Applied After Configuration**
**Problem**: Limits don't take effect after editing limits.conf.
**Fix**: User must log out and log back in for limits to apply.
```bash
# Limits are applied at login time
# After editing limits.conf, user must re-login

# Switch to user to test
sudo su - nfsuser

# Check limits
ulimit -u
ulimit -Hu

# Exit and re-login if needed
exit
sudo su - nfsuser
```

### **Issue 2: Soft Limit Higher Than Hard Limit**
**Problem**: Configuration rejected if soft > hard.
**Fix**: Ensure soft limit is less than or equal to hard limit.
```bash
# WRONG - Soft higher than hard
nfsuser    soft    nproc    2025
nfsuser    hard    nproc    1027

# CORRECT - Soft less than or equal to hard
nfsuser    soft    nproc    1027
nfsuser    hard    nproc    2025
```

### **Issue 3: Syntax Errors in limits.conf**
**Problem**: Incorrect syntax prevents limits from being applied.
**Fix**: Use correct format with proper spacing.
```bash
# Correct format:
# <domain> <type> <item> <value>

# Examples:
nfsuser    soft    nproc    1027
nfsuser    hard    nproc    2025

# Use tabs or multiple spaces for separation
```

### **Issue 4: Limits Not Working for Root**
**Problem**: Root user can bypass limits.
**Fix**: Root is not subject to limits.conf restrictions.
```bash
# Root can always exceed limits
# To limit root, use cgroups or other mechanisms

# Verify limits work for regular users
sudo su - nfsuser
ulimit -u
```

### **Issue 5: Wrong Resource Type**
**Problem**: Using wrong item name in limits.conf.
**Fix**: Use correct item names.
```bash
# Common resource types:
nproc    # Number of processes
nofile   # Number of open files
fsize    # Maximum file size
cpu      # CPU time in minutes
as       # Address space (virtual memory)
memlock  # Locked memory

# Example:
nfsuser    soft    nproc    1027
nfsuser    hard    nofile   4096
```

---

## 🔧 Troubleshooting Failures

### **Error: Permission Denied**
**Symptoms**: Cannot edit /etc/security/limits.conf.
**Solution**: Use sudo to edit system configuration files.
```bash
# Always use sudo
sudo vi /etc/security/limits.conf

# Verify file permissions
ls -l /etc/security/limits.conf
```

### **Error: Limits Still Unlimited**
**Symptoms**: ulimit shows unlimited after configuration.
**Solution**: Verify syntax and re-login as user.
```bash
# Check configuration
sudo grep nfsuser /etc/security/limits.conf

# Verify syntax is correct
# Exit and re-login as user
exit
sudo su - nfsuser
ulimit -u
```

### **Error: Cannot Exceed Soft Limit**
**Symptoms**: User cannot increase soft limit.
**Solution**: Soft limit can be increased up to hard limit by user.
```bash
# User can increase soft limit up to hard limit
ulimit -u 2000  # If hard limit is 2025

# Check new soft limit
ulimit -u

# Check hard limit (unchanged)
ulimit -Hu
```

### **Error: Configuration Ignored**
**Symptoms**: Limits.conf changes not taking effect.
**Solution**: Check for PAM configuration and syntax errors.
```bash
# Verify PAM is configured to use limits
grep pam_limits /etc/pam.d/system-auth
grep pam_limits /etc/pam.d/sshd

# Should see:
# session    required     pam_limits.so

# Check for syntax errors
sudo cat /etc/security/limits.conf | grep nfsuser
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The primary challenge was configuring user-specific process limits to prevent resource exhaustion on the storage server. Understanding the difference between soft and hard limits, and the proper syntax for `/etc/security/limits.conf`, is crucial for effective resource management.

**💡 Solution Approach**:
1. **User Verification**: Confirmed nfsuser exists on the system.
2. **Backup Creation**: Created backup of limits.conf before modifications.
3. **Configuration**: Added soft (1027) and hard (2025) nproc limits for nfsuser.
4. **File Verification**: Confirmed entries were added correctly.
5. **Limit Testing**: Switched to nfsuser and verified limits with ulimit commands.

**🎯 Key Success Factors**:
- **Correct Syntax**: Used proper format `<domain> <type> <item> <value>`.
- **Soft vs Hard**: Set soft limit lower than hard limit.
- **Login Required**: Switched to user account for limits to take effect.
- **Verification**: Used `ulimit -u` and `ulimit -Hu` to confirm limits.
- **Backup**: Created backup before making changes.

**⚠️ Critical Fixes**:
- Always use sudo to edit /etc/security/limits.conf.
- Ensure soft limit is less than or equal to hard limit.
- User must log in (or switch to account) for limits to apply.
- Use correct resource type (nproc for process limits).
- Verify configuration with grep and ulimit commands.

**🔒 Linux Resource Limit Best Practices Applied**:
- **Soft Limits**: Set reasonable default to prevent accidental resource exhaustion.
- **Hard Limits**: Set absolute maximum to protect system stability.
- **User-Specific**: Applied limits to specific user, not system-wide.
- **Verification**: Tested limits after configuration.
- **Backup**: Created backup before modifying system files.

**⚠️ Important Resource Limit Concepts**:
- **Soft Limit**: Default limit, can be increased by user up to hard limit.
- **Hard Limit**: Maximum limit, cannot be exceeded (even by user).
- **nproc**: Number of processes a user can create.
- **Login Required**: Limits applied when user logs in.
- **PAM Integration**: limits.conf works through PAM (Pluggable Authentication Modules).

---

## ⚠️ Important Production Notes

🔧 **Soft vs Hard**: Soft limits are defaults, hard limits are absolute maximums.
🔐 **Login Required**: Limits apply at login time, not immediately after configuration.
📊 **Resource Types**: nproc (processes), nofile (files), fsize (file size), etc.
🛡️ **Root Exception**: Root user can bypass limits.conf restrictions.
🗄️ **PAM Dependency**: Requires PAM module pam_limits.so to be enabled.
📝 **Documentation**: Document resource limits and rationale in change logs.
🔍 **Monitoring**: Monitor resource usage to adjust limits appropriately.
⚙️ **Automation**: Use configuration management for consistent limit settings.

**limits.conf Syntax**:
```
Format:
<domain> <type> <item> <value>

Domain:
username    # Specific user
@groupname  # Group (with @ prefix)
*           # All users (wildcard)

Type:
soft        # Soft limit (default, user can increase)
hard        # Hard limit (absolute maximum)
-           # Both soft and hard

Item (common):
nproc       # Max number of processes
nofile      # Max number of open files
fsize       # Max file size
cpu         # Max CPU time (minutes)
as          # Max address space (KB)
memlock     # Max locked memory (KB)
stack       # Max stack size (KB)
core        # Max core file size (KB)

Value:
<number>    # Numeric limit
unlimited   # No limit
```

**Example Configurations**:
```bash
# User-specific limits
nfsuser    soft    nproc    1027
nfsuser    hard    nproc    2025
nfsuser    soft    nofile   4096
nfsuser    hard    nofile   8192

# Group limits
@developers    soft    nproc    500
@developers    hard    nproc    1000

# Wildcard (all users)
*    soft    core    0
*    hard    nofile  65536

# Both soft and hard
apache    -    nproc    2048
```

**Security Recommendations**:
- Set appropriate limits to prevent resource exhaustion attacks
- Monitor resource usage to identify appropriate limit values
- Use soft limits for normal operation, hard limits for safety
- Document business justification for limit values
- Regularly review and adjust limits based on usage patterns
- Implement monitoring alerts for users approaching limits
- Use group-based limits for role-based resource management
- Test limits in non-production before applying to production

**Ulimit Command Reference**:
```bash
# Display limits
ulimit -a          # All limits
ulimit -u          # Max user processes (soft)
ulimit -Hu         # Max user processes (hard)
ulimit -n          # Max open files (soft)
ulimit -Hn         # Max open files (hard)

# Set limits (temporary, current session only)
ulimit -u 1500     # Set soft limit for processes
ulimit -Hu 2000    # Cannot set hard limit higher (need root)

# Common options:
-c  Core file size
-d  Data segment size
-f  File size
-l  Max locked memory
-m  Max memory size
-n  Open files
-s  Stack size
-t  CPU time
-u  User processes
-v  Virtual memory
```

**Monitoring Resource Usage**:
```bash
# Check current process count for user
ps -u nfsuser | wc -l

# Monitor processes in real-time
top -u nfsuser

# Check all users' process counts
for user in $(cut -f1 -d: /etc/passwd); do
  echo "$user: $(ps -u $user 2>/dev/null | wc -l)"
done

# View limits for all users
for user in $(cut -f1 -d: /etc/passwd); do
  sudo -u $user bash -c 'echo "$USER: $(ulimit -u)"' 2>/dev/null
done
```

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Configured process limits for nfsuser on storage server
- Set soft limit to 1027 processes
- Set hard limit to 2025 processes
- Verified limits are applied correctly
- Configuration persists across logins

**🔍 Verification Completed**:
- User exists: nfsuser confirmed
- Configuration file: /etc/security/limits.conf updated
- Soft limit: 1027 (verified with `ulimit -u`)
- Hard limit: 2025 (verified with `ulimit -Hu`)
- Limits active: Applied when user logs in
- Backup created: limits.conf.backup saved

**🚀 Next Steps** (Optional):
- Monitor nfsuser process usage
- Adjust limits based on actual usage patterns
- Implement alerts for users approaching limits
- Document limit configuration in system documentation
- Apply similar limits to other high-usage users
- Review and optimize application to reduce process count

**🔒 Configuration Validation**:
- ✅ Soft limit set to 1027 processes
- ✅ Hard limit set to 2025 processes
- ✅ Limits applied to nfsuser specifically
- ✅ Configuration persists across reboots and logins
- ✅ User cannot exceed hard limit
- ✅ User can increase soft limit up to hard limit

**📊 Configuration Summary**:
- **Server**: Storage Server (ststor01)
- **User**: nfsuser
- **Resource**: nproc (number of processes)
- **Soft Limit**: 1027
- **Hard Limit**: 2025
- **Configuration File**: /etc/security/limits.conf
- **Status**: Active and verified

**🔍 Key Learning Points**:
- Soft limits are default values, can be increased by user up to hard limit
- Hard limits are absolute maximums, cannot be exceeded
- Limits are applied when user logs in (not immediately)
- Configuration stored in /etc/security/limits.conf
- Use `ulimit -u` to check soft limit, `ulimit -Hu` for hard limit
- Format: `<domain> <type> <item> <value>`
