# 🔐 Task 9 - Grant Executable Permissions to Bash Script on App Server 2

**📌 Task Description**

In a bid to automate backup processes, the **xFusionCorp Industries sysadmin team** has developed a new bash script named `xfusioncorp.sh`. While the script has been distributed to all necessary servers, it lacks executable permissions on **App Server 2** within the Stratos Datacenter. Understanding Linux file permissions and the `chmod` command is essential for managing script execution rights and implementing proper access control.

**👉 Task Requirements**:
- **Target Server**: App Server 2 (stapp02) in Stratos Datacenter
- **Script Location**: `/tmp/xfusioncorp.sh`
- **Permission Requirement**: Executable permissions for all users
- **Operation Objectives**:
  - Grant execute permission to owner, group, and others
  - Enable all users to run the backup script
  - Verify script is executable
  - Test script execution
- Modify file permissions using `chmod`
- Ensure script can be executed by any user
- Verify successful permission change
- Test script functionality

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to App Server 2

```bash
ssh steve@stapp02
```

**Purpose**: Establish SSH connection to App Server 2 where the script permissions will be modified.

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

## 🔹 Step 2: Verify Script Exists

```bash
ls -l /tmp/xfusioncorp.sh
```

**Purpose**: Confirm the script file exists and check current permissions.

**Expected Output**:
```
---------- 1 root root 40 Feb  8 09:19 /tmp/xfusioncorp.sh
```

**Permission Breakdown**:
- `----------`: No permissions set (owner, group, others all have no permissions)
- First `-`: Regular file (not directory)
- Next 9 characters: `rwx` permissions for owner, group, others (all `-` means no permissions)
- `1`: Number of hard links
- `root root`: Owner and group
- `40`: File size in bytes
- `Feb  8 09:19`: Last modification date/time
- `/tmp/xfusioncorp.sh`: File path

**Success Indicators**:
- ✅ File exists at `/tmp/xfusioncorp.sh`
- ✅ Currently has no permissions (----------)
- ✅ Owned by root
- ✅ Ready for permission modification

---

## 🔹 Step 3: Attempt to Execute Script (Before Permission Change)

```bash
/tmp/xfusioncorp.sh
```

**Purpose**: Demonstrate that the script cannot be executed without proper permissions.

**Expected Output**:
```
-bash: /tmp/xfusioncorp.sh: Permission denied
```

**Success Indicators**:
- ✅ Permission denied error confirms no execute permission
- ✅ Demonstrates need for permission change
- ✅ Script cannot run in current state

---

## 🔹 Step 4: View Script Contents (Optional)

```bash
sudo cat /tmp/xfusioncorp.sh
```

**Purpose**: View the script contents to understand what it does.

**Expected Output**:
```bash
#!/bin/bash
echo "Welcome To KodeKloud"
```

**Success Indicators**:
- ✅ Script is a simple bash script
- ✅ Contains shebang (`#!/bin/bash`)
- ✅ Outputs welcome message

---

## 🔹 Step 5: Grant Executable Permissions to All Users

```bash
sudo chmod 755 /tmp/xfusioncorp.sh
```

**Purpose**: Set permissions to allow all users to execute the script.

**Command Breakdown**:
- `sudo`: Execute with administrative privileges
- `chmod`: Change file mode (permissions)
- `755`: Permission mode in octal notation
  - `7` (owner): read (4) + write (2) + execute (1) = 7
  - `5` (group): read (4) + execute (1) = 5
  - `5` (others): read (4) + execute (1) = 5
- `/tmp/xfusioncorp.sh`: Target file

**Expected Output**:
```
[no output indicates success]
```

**Success Indicators**:
- ✅ Command completes without errors
- ✅ Permissions modified successfully
- ✅ Script is now executable

**Permission Mode Explanation**:
- **755** = `rwxr-xr-x`
  - Owner: read, write, execute
  - Group: read, execute
  - Others: read, execute

---

## 🔹 Step 6: Verify Permission Change

```bash
ls -l /tmp/xfusioncorp.sh
```

**Purpose**: Confirm the permissions have been changed correctly.

**Expected Output**:
```
-rwxr-xr-x 1 root root 40 Feb  8 09:19 /tmp/xfusioncorp.sh
```

**Permission Breakdown**:
- `-rwxr-xr-x`: File permissions
  - `-`: Regular file
  - `rwx`: Owner has read, write, execute
  - `r-x`: Group has read, execute (no write)
  - `r-x`: Others have read, execute (no write)

**Success Indicators**:
- ✅ Permissions changed from `----------` to `-rwxr-xr-x`
- ✅ Execute bit (`x`) is set for owner, group, and others
- ✅ All users can now execute the script

---

## 🔹 Step 7: Execute the Script as Regular User

```bash
/tmp/xfusioncorp.sh
```

**Purpose**: Test that the script can now be executed by a regular user.

**Expected Output**:
```
Welcome To KodeKloud
```

**Success Indicators**:
- ✅ Script executes successfully
- ✅ No permission denied error
- ✅ Output is displayed correctly
- ✅ All users can execute the script

---

## 🔹 Step 8: Verify Permissions Using stat Command

```bash
stat /tmp/xfusioncorp.sh
```

**Purpose**: Display detailed file information including permissions in multiple formats.

**Expected Output**:
```
  File: /tmp/xfusioncorp.sh
  Size: 40              Blocks: 8          IO Block: 4096   regular file
Device: fd00h/64768d    Inode: 12345       Links: 1
Access: (0755/-rwxr-xr-x)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2026-02-08 09:19:00.000000000 +0000
Modify: 2026-02-08 09:19:00.000000000 +0000
Change: 2026-02-08 14:26:00.000000000 +0000
 Birth: -
```

**Success Indicators**:
- ✅ Access shows `(0755/-rwxr-xr-x)`
- ✅ Octal notation: `0755`
- ✅ Symbolic notation: `-rwxr-xr-x`
- ✅ Permissions are correctly set

---

## 🔹 Step 9: Test Execution from Different Directory

```bash
cd /home
/tmp/xfusioncorp.sh
```

**Purpose**: Verify the script can be executed from any directory using absolute path.

**Expected Output**:
```
Welcome To KodeKloud
```

**Success Indicators**:
- ✅ Script executes from different working directory
- ✅ Absolute path works correctly
- ✅ Execute permission is functional

---

## 🔹 Step 10: Verify All Users Can Execute (Optional)

```bash
sudo -u steve /tmp/xfusioncorp.sh
```

**Purpose**: Test that other users can also execute the script.

**Expected Output**:
```
Welcome To KodeKloud
```

**Success Indicators**:
- ✅ Different user can execute script
- ✅ Permissions apply to all users
- ✅ Task requirement fulfilled

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **App Server 2**:

```bash
# Connect to App Server 2
ssh steve@stapp02

# Verify script exists and check current permissions
ls -l /tmp/xfusioncorp.sh

# Attempt to execute (should fail initially)
/tmp/xfusioncorp.sh

# View script contents (optional)
sudo cat /tmp/xfusioncorp.sh

# Grant executable permissions to all users
sudo chmod 755 /tmp/xfusioncorp.sh

# Verify permission change
ls -l /tmp/xfusioncorp.sh

# Execute the script
/tmp/xfusioncorp.sh

# Verify with stat command
stat /tmp/xfusioncorp.sh

# Test from different directory
cd /home
/tmp/xfusioncorp.sh
```

---

## 💡 Common Permission Issues & Fixes

### **Issue 1: Permission Denied After chmod**
**Problem**: Script still shows permission denied after running chmod.
**Fix**: Verify chmod command was run with sudo and correct permissions were set.
```bash
# Verify current permissions
ls -l /tmp/xfusioncorp.sh

# Ensure using sudo
sudo chmod 755 /tmp/xfusioncorp.sh

# Verify change
ls -l /tmp/xfusioncorp.sh
```

### **Issue 2: Wrong Permission Mode**
**Problem**: Set wrong permissions (e.g., 644 instead of 755).
**Fix**: Reapply correct permissions.
```bash
# 644 gives read/write to owner, read to others (no execute)
# WRONG for scripts
sudo chmod 644 /tmp/xfusioncorp.sh

# CORRECT - 755 gives execute to all
sudo chmod 755 /tmp/xfusioncorp.sh

# Verify
ls -l /tmp/xfusioncorp.sh
```

### **Issue 3: Only Owner Can Execute**
**Problem**: Only root can execute, other users get permission denied.
**Fix**: Ensure execute bit is set for group and others.
```bash
# Check current permissions
ls -l /tmp/xfusioncorp.sh

# Set execute for all users
sudo chmod 755 /tmp/xfusioncorp.sh

# Alternative: Add execute bit to existing permissions
sudo chmod +x /tmp/xfusioncorp.sh
```

### **Issue 4: Script Not Found**
**Problem**: Script doesn't exist at specified path.
**Fix**: Verify script location and path.
```bash
# Search for script
sudo find /tmp -name "xfusioncorp.sh"

# Verify exact path
ls -l /tmp/xfusioncorp.sh

# Check if in different location
sudo find / -name "xfusioncorp.sh" 2>/dev/null
```

### **Issue 5: Cannot Modify Permissions**
**Problem**: Permission denied when running chmod.
**Fix**: Ensure using sudo for administrative operations.
```bash
# Use sudo
sudo chmod 755 /tmp/xfusioncorp.sh

# Verify you have sudo access
sudo -l
```

---

## 🔧 Troubleshooting Failures

### **Error: Operation Not Permitted**
**Symptoms**: `chmod: changing permissions of '/tmp/xfusioncorp.sh': Operation not permitted`.
**Solution**: Use sudo to modify file owned by root.
```bash
# Always use sudo for files owned by root
sudo chmod 755 /tmp/xfusioncorp.sh

# Verify file ownership
ls -l /tmp/xfusioncorp.sh
```

### **Error: No Such File or Directory**
**Symptoms**: `chmod: cannot access '/tmp/xfusioncorp.sh': No such file or directory`.
**Solution**: Verify file exists and path is correct.
```bash
# Check if file exists
ls -l /tmp/xfusioncorp.sh

# List /tmp directory
ls -la /tmp/

# Search for file
sudo find /tmp -name "*xfusion*"
```

### **Error: Bad Interpreter**
**Symptoms**: `/tmp/xfusioncorp.sh: /bin/bash: bad interpreter: No such file or directory`.
**Solution**: Verify bash is installed and shebang is correct.
```bash
# Check bash location
which bash

# Verify shebang in script
head -1 /tmp/xfusioncorp.sh

# Should show: #!/bin/bash
```

### **Error: Script Runs But No Output**
**Symptoms**: Script executes but doesn't show expected output.
**Solution**: Verify script contents and syntax.
```bash
# View script contents
sudo cat /tmp/xfusioncorp.sh

# Run with bash explicitly
bash /tmp/xfusioncorp.sh

# Check for syntax errors
bash -n /tmp/xfusioncorp.sh
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The primary challenge was understanding Linux file permissions and applying the correct permission mode to allow all users to execute a script. Understanding octal notation (755) and symbolic notation (rwxr-xr-x) is crucial for proper permission management.

**💡 Solution Approach**:
1. **Permission Verification**: Checked current permissions showing no access (----------).
2. **Permission Mode Selection**: Used 755 to grant execute to all while maintaining security.
3. **Permission Application**: Applied `chmod 755` with sudo for root-owned file.
4. **Verification**: Confirmed permissions changed to `-rwxr-xr-x`.
5. **Functional Testing**: Executed script to verify it works for all users.

**🎯 Key Success Factors**:
- **Correct Permission Mode**: Used 755 (rwxr-xr-x) for executable scripts.
- **Sudo Usage**: Applied sudo to modify root-owned file.
- **Verification**: Confirmed permissions with `ls -l` before and after.
- **Testing**: Executed script to verify functionality.
- **Understanding**: Grasped octal vs symbolic permission notation.

**⚠️ Critical Fixes**:
- Always use sudo to modify files owned by root.
- Use 755 (not 777) for scripts to maintain security.
- Verify permissions with `ls -l` after chmod.
- Test script execution to confirm permissions work.
- Understand that execute bit must be set for scripts to run.

**🔒 Linux Permission Best Practices Applied**:
- **Least Privilege**: Used 755 (not 777) to prevent write access by others.
- **Execute Bit**: Set execute permission for script execution.
- **Owner Permissions**: Maintained write access for owner only.
- **Group/Others**: Granted read and execute, but not write.
- **Security**: Avoided overly permissive modes like 777.

**⚠️ Important Permission Concepts**:
- **Octal Notation**: 755 = rwxr-xr-x (numeric representation).
- **Symbolic Notation**: rwxr-xr-x (human-readable representation).
- **Execute Bit**: Required for running scripts and programs.
- **Permission Hierarchy**: Owner, Group, Others (in that order).
- **Security**: More restrictive permissions are more secure.

---

## ⚠️ Important Production Notes

🔧 **Permission Modes**: Use 755 for scripts (not 777), 644 for data files, 600 for sensitive files.
🔐 **Security**: Avoid 777 permissions - they allow anyone to modify files.
📊 **Ownership**: Scripts in /tmp should have appropriate ownership and permissions.
🛡️ **Execute Bit**: Required for scripts - without it, scripts cannot run.
🗄️ **Backup Scripts**: Store production scripts in /usr/local/bin or /opt, not /tmp.
📝 **Documentation**: Document permission requirements for scripts.
🔍 **Regular Audits**: Review file permissions regularly for security compliance.
⚙️ **Automation**: Use configuration management tools for consistent permissions.

**Permission Mode Reference**:
```bash
# Common permission modes
755 (rwxr-xr-x)  # Executable scripts, directories
644 (rw-r--r--)  # Regular files, configuration files
600 (rw-------)  # Sensitive files (passwords, keys)
700 (rwx------)  # Private directories, sensitive scripts
775 (rwxrwxr-x)  # Shared directories (group writable)
664 (rw-rw-r--)  # Shared files (group writable)

# Avoid these in production
777 (rwxrwxrwx)  # Too permissive - security risk
666 (rw-rw-rw-)  # Too permissive - security risk
```

**chmod Command Syntax**:
```bash
# Octal notation (absolute)
chmod 755 file.sh           # Set exact permissions

# Symbolic notation (relative)
chmod +x file.sh            # Add execute for all
chmod u+x file.sh           # Add execute for owner
chmod g+x file.sh           # Add execute for group
chmod o+x file.sh           # Add execute for others
chmod a+x file.sh           # Add execute for all (same as +x)

chmod -x file.sh            # Remove execute for all
chmod u-w file.sh           # Remove write for owner

chmod u=rwx,g=rx,o=rx file.sh  # Set specific permissions

# Recursive (for directories)
chmod -R 755 /path/to/dir   # Apply to directory and contents
```

**Permission Calculation**:
```
Octal values:
r (read)    = 4
w (write)   = 2
x (execute) = 1

Examples:
rwx = 4+2+1 = 7
rw- = 4+2+0 = 6
r-x = 4+0+1 = 5
r-- = 4+0+0 = 4
--- = 0+0+0 = 0

755 breakdown:
7 (owner)  = rwx (4+2+1)
5 (group)  = r-x (4+0+1)
5 (others) = r-x (4+0+1)
Result: rwxr-xr-x
```

**Security Recommendations**:
- Never use 777 permissions unless absolutely necessary
- Scripts should be 755 (owner can write, others can only read/execute)
- Configuration files should be 644 (owner can write, others read-only)
- Sensitive files (keys, passwords) should be 600 (owner only)
- Use `chmod +x` to add execute without changing other permissions
- Regularly audit file permissions with `find` command
- Implement least privilege principle
- Use ACLs (Access Control Lists) for complex permission requirements

**Script Location Best Practices**:
```bash
# Temporary scripts (testing)
/tmp/script.sh              # Cleaned on reboot

# User scripts
~/bin/script.sh             # User's personal scripts
~/.local/bin/script.sh      # XDG-compliant location

# System-wide scripts
/usr/local/bin/script.sh    # Custom system scripts
/opt/company/bin/script.sh  # Application-specific scripts

# System scripts (package-managed)
/usr/bin/script.sh          # Managed by package manager
/usr/sbin/script.sh         # System administration scripts
```

**Permission Verification Commands**:
```bash
# List permissions
ls -l file.sh

# Detailed file information
stat file.sh

# Find files with specific permissions
find /path -perm 755

# Find files with execute bit set
find /path -perm /111

# Find files without execute bit
find /path ! -perm /111

# Find world-writable files (security risk)
find /path -perm -002
```

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Granted executable permissions to `/tmp/xfusioncorp.sh`
- Enabled all users to execute the script
- Verified permission change from `----------` to `-rwxr-xr-x`
- Tested script execution successfully
- Confirmed script outputs "Welcome To KodeKloud"

**🔍 Verification Completed**:
- Script exists: `/tmp/xfusioncorp.sh`
- Initial permissions: `----------` (no permissions)
- Permission changed: `sudo chmod 755`
- Final permissions: `-rwxr-xr-x` (755)
- Script executable: All users can run it
- Output verified: "Welcome To KodeKloud"

**🚀 Next Steps** (Optional):
- Move script to permanent location (e.g., `/usr/local/bin`)
- Set up automated backup schedule using the script
- Document script purpose and usage
- Implement logging for script execution
- Create cron job for automated execution
- Add error handling to script

**🔒 Security Validation**:
- ✅ Permissions set to 755 (not overly permissive 777)
- ✅ Owner can read, write, execute
- ✅ Group can read and execute (no write)
- ✅ Others can read and execute (no write)
- ✅ Script is executable by all users as required
- ✅ Security maintained while meeting requirements

**📊 Permission Summary**:
- **Before**: `----------` (no permissions)
- **After**: `-rwxr-xr-x` (755)
- **Owner**: read, write, execute
- **Group**: read, execute
- **Others**: read, execute
- **Result**: All users can execute, only owner can modify

**🔍 Key Learning Points**:
- `chmod 755` sets permissions to rwxr-xr-x
- Execute bit (x) is required for running scripts
- Octal 755 = Owner(7=rwx), Group(5=r-x), Others(5=r-x)
- `sudo` required to modify root-owned files
- `ls -l` shows permissions in symbolic notation
- `stat` shows permissions in both octal and symbolic notation
