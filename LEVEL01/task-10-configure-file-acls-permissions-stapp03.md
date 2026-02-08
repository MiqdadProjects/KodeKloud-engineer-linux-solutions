# 🔐 Task 10 - Configure File ACLs and Permissions on App Server 3

**📌 Task Description**

After conducting a security audit within the **Stratos DC**, the Nautilus security team discovered misconfigured permissions on critical files. To address this, corrective actions are being taken by the production support team. The `/etc/hosts` file on **Nautilus App Server 3** requires adjustments to its Access Control Lists (ACLs) to implement granular user-specific permissions beyond standard Unix permissions.

**👉 Task Requirements**:
- **Target Server**: App Server 3 (stapp03) in Stratos Datacenter
- **Target File**: `/etc/hosts`
- **Permission Requirements**:
  1. File owner: `root`
  2. Group owner: `root`
  3. Others: Read-only permissions
  4. User `mark`: No permissions (explicitly denied)
  5. User `ryan`: Read-only permission (via ACL)
- **Operation Objectives**:
  - Set correct ownership using `chown`
  - Configure base permissions using `chmod`
  - Apply user-specific ACLs using `setfacl`
  - Verify all permission changes
- Implement granular access control beyond standard permissions
- Use ACLs for user-specific permission management
- Verify configuration with `getfacl`

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to App Server 3

```bash
ssh banner@stapp03
```

**Purpose**: Establish SSH connection to App Server 3 where ACL configuration will be performed.

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

## 🔹 Step 2: Verify File Exists and Check Current Permissions

```bash
ls -l /etc/hosts
```

**Purpose**: Confirm the file exists and check current ownership and permissions.

**Expected Output** (sample):
```
-rw-r--r-- 1 root root 158 Feb 08 14:00 /etc/hosts
```

**Success Indicators**:
- ✅ File exists at `/etc/hosts`
- ✅ Current ownership visible
- ✅ Current permissions visible

---

## 🔹 Step 3: Check Current ACLs (If Any)

```bash
getfacl /etc/hosts
```

**Purpose**: Display current ACL configuration before making changes.

**Expected Output** (sample):
```
# file: etc/hosts
# owner: root
# group: root
user::rw-
group::r--
other::r--
```

**Success Indicators**:
- ✅ Current ACLs displayed
- ✅ Baseline configuration visible
- ✅ Ready to apply changes

---

## 🔹 Step 4: Set File Owner and Group to Root

```bash
sudo chown root:root /etc/hosts
```

**Purpose**: Ensure the file is owned by root user and root group.

**Command Breakdown**:
- `sudo`: Execute with administrative privileges
- `chown`: Change ownership command
- `root:root`: Set owner to `root`, group to `root`
- `/etc/hosts`: Target file

**Expected Output**:
```
[no output indicates success]
```

**Success Indicators**:
- ✅ Ownership changed to root:root
- ✅ Command completes without errors

---

## 🔹 Step 5: Verify Ownership Change

```bash
ls -l /etc/hosts
```

**Purpose**: Confirm ownership is now set to root:root.

**Expected Output**:
```
-rw-r--r-- 1 root root 158 Feb 08 14:00 /etc/hosts
```

**Success Indicators**:
- ✅ Owner is `root`
- ✅ Group is `root`
- ✅ Ownership requirement met

---

## 🔹 Step 6: Set Others' Permissions to Read-Only

```bash
sudo chmod o=r /etc/hosts
```

**Purpose**: Configure "others" (world) to have only read permission.

**Command Breakdown**:
- `sudo`: Execute with administrative privileges
- `chmod`: Change file mode (permissions)
- `o=r`: Set others' permissions to read-only
  - `o`: Others (world)
  - `=`: Set exactly (replace existing)
  - `r`: Read permission only

**Expected Output**:
```
[no output indicates success]
```

**Success Indicators**:
- ✅ Others have read-only permission
- ✅ Write and execute removed from others

---

## 🔹 Step 7: Verify Base Permissions

```bash
ls -l /etc/hosts
```

**Purpose**: Confirm base permissions are correctly set.

**Expected Output**:
```
-rw-r--r-- 1 root root 158 Feb 08 14:00 /etc/hosts
```

**Permission Breakdown**:
- `-rw-r--r--`: File permissions
  - Owner (root): read, write
  - Group (root): read
  - Others: read

**Success Indicators**:
- ✅ Owner has read/write
- ✅ Group has read
- ✅ Others have read-only

---

## 🔹 Step 8: Remove All Permissions for User mark Using ACL

```bash
sudo setfacl -m u:mark:0 /etc/hosts
```

**Purpose**: Explicitly deny all permissions to user `mark` using ACL.

**Command Breakdown**:
- `sudo`: Execute with administrative privileges
- `setfacl`: Set file access control lists
- `-m`: Modify ACL
- `u:mark:0`: User `mark` with no permissions (0 = ---)
- `/etc/hosts`: Target file

**Expected Output**:
```
[no output indicates success]
```

**Success Indicators**:
- ✅ ACL entry created for user `mark`
- ✅ All permissions denied (---)
- ✅ User `mark` cannot access file

**⚠️ Important**: `0` means no permissions (equivalent to `---`).

---

## 🔹 Step 9: Verify ACL for User mark

```bash
getfacl /etc/hosts
```

**Purpose**: Confirm ACL entry for user `mark` shows no permissions.

**Expected Output** (partial):
```
# file: etc/hosts
# owner: root
# group: root
user::rw-
user:mark:---
group::r--
mask::r--
other::r--
```

**Success Indicators**:
- ✅ Entry `user:mark:---` is present
- ✅ User `mark` has no permissions
- ✅ ACL mask is set appropriately

---

## 🔹 Step 10: Grant Read-Only Permission to User ryan Using ACL

```bash
sudo setfacl -m u:ryan:r /etc/hosts
```

**Purpose**: Grant read-only permission to user `ryan` using ACL.

**Command Breakdown**:
- `sudo`: Execute with administrative privileges
- `setfacl`: Set file access control lists
- `-m`: Modify ACL
- `u:ryan:r`: User `ryan` with read permission
- `/etc/hosts`: Target file

**Expected Output**:
```
[no output indicates success]
```

**Success Indicators**:
- ✅ ACL entry created for user `ryan`
- ✅ Read permission granted
- ✅ User `ryan` can read file

---

## 🔹 Step 11: Verify ACL for User ryan

```bash
getfacl /etc/hosts
```

**Purpose**: Confirm ACL entry for user `ryan` shows read permission.

**Expected Output**:
```
# file: etc/hosts
# owner: root
# group: root
user::rw-
user:mark:---
user:ryan:r--
group::r--
mask::r--
other::r--
```

**Success Indicators**:
- ✅ Entry `user:ryan:r--` is present
- ✅ User `ryan` has read permission
- ✅ All ACL requirements met

---

## 🔹 Step 12: Final Verification - Check File Permissions

```bash
ls -l /etc/hosts
```

**Purpose**: Verify base file permissions with ACL indicator.

**Expected Output**:
```
-rw-r--r--+ 1 root root 158 Feb 08 14:00 /etc/hosts
```

**Success Indicators**:
- ✅ Owner: `root`
- ✅ Group: `root`
- ✅ **`+` symbol indicates ACLs are set**
- ✅ Base permissions: `-rw-r--r--`

**⚠️ Important**: The `+` at the end of permissions indicates ACLs are configured.

---

## 🔹 Step 13: Final Verification - Complete ACL Display

```bash
getfacl /etc/hosts
```

**Purpose**: Display complete ACL configuration for final verification.

**Expected Output**:
```
# file: etc/hosts
# owner: root
# group: root
user::rw-
user:mark:---
user:ryan:r--
group::r--
mask::r--
other::r--
```

**Success Indicators**:
- ✅ Owner: root
- ✅ Group: root
- ✅ User mark: no permissions (---)
- ✅ User ryan: read permission (r--)
- ✅ Others: read permission (r--)
- ✅ All requirements met

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **App Server 3**:

```bash
# Connect to App Server 3
ssh banner@stapp03

# Check current permissions
ls -l /etc/hosts

# Check current ACLs
getfacl /etc/hosts

# Set owner and group to root
sudo chown root:root /etc/hosts

# Verify ownership
ls -l /etc/hosts

# Set others to read-only
sudo chmod o=r /etc/hosts

# Verify base permissions
ls -l /etc/hosts

# Remove all permissions for user mark
sudo setfacl -m u:mark:0 /etc/hosts

# Verify mark's ACL
getfacl /etc/hosts | grep mark

# Grant read permission to user ryan
sudo setfacl -m u:ryan:r /etc/hosts

# Verify ryan's ACL
getfacl /etc/hosts | grep ryan

# Final verification - permissions
ls -l /etc/hosts

# Final verification - complete ACLs
getfacl /etc/hosts
```

---

## 💡 Common ACL Configuration Issues & Fixes

### **Issue 1: ACL Not Supported on Filesystem**
**Problem**: `setfacl: Operation not supported`.
**Fix**: Verify filesystem supports ACLs and remount if needed.
```bash
# Check filesystem type
df -T /etc

# Check if ACL is enabled
tune2fs -l /dev/sda1 | grep "Default mount options"

# Remount with ACL support (if needed)
sudo mount -o remount,acl /
```

### **Issue 2: User Does Not Exist**
**Problem**: `setfacl: Option -m: Invalid argument near character 3`.
**Fix**: Verify users exist before setting ACLs.
```bash
# Check if user mark exists
id mark

# Check if user ryan exists
id ryan

# If users don't exist, create them first
sudo useradd mark
sudo useradd ryan
```

### **Issue 3: ACL Mask Limiting Permissions**
**Problem**: User permissions don't work as expected due to mask.
**Fix**: Adjust ACL mask if needed.
```bash
# Check current mask
getfacl /etc/hosts | grep mask

# Set mask to allow read
sudo setfacl -m m::r /etc/hosts

# Verify
getfacl /etc/hosts
```

### **Issue 4: Cannot Remove ACLs**
**Problem**: Need to remove all ACLs and start over.
**Fix**: Use `-b` flag to remove all ACLs.
```bash
# Remove all ACLs
sudo setfacl -b /etc/hosts

# Verify ACLs removed
getfacl /etc/hosts

# Reapply ACLs as needed
sudo setfacl -m u:mark:0 /etc/hosts
sudo setfacl -m u:ryan:r /etc/hosts
```

### **Issue 5: Wrong Permission Notation**
**Problem**: Used wrong notation for permissions.
**Fix**: Use correct ACL permission notation.
```bash
# WRONG - Using octal notation
sudo setfacl -m u:ryan:4 /etc/hosts

# CORRECT - Using symbolic notation
sudo setfacl -m u:ryan:r /etc/hosts

# Permission notation:
# r = read
# w = write
# x = execute
# - = no permission
# 0 = no permissions (---)
```

---

## 🔧 Troubleshooting Failures

### **Error: Operation Not Permitted**
**Symptoms**: `setfacl: /etc/hosts: Operation not permitted`.
**Solution**: Use sudo for ACL operations.
```bash
# Always use sudo for system files
sudo setfacl -m u:mark:0 /etc/hosts

# Verify you have sudo access
sudo -l
```

### **Error: Invalid Argument**
**Symptoms**: `setfacl: Option -m: Invalid argument`.
**Solution**: Verify ACL syntax and user existence.
```bash
# Correct syntax: u:username:permissions
sudo setfacl -m u:mark:0 /etc/hosts
sudo setfacl -m u:ryan:r /etc/hosts

# Verify users exist
id mark
id ryan
```

### **Error: No Such File or Directory**
**Symptoms**: `setfacl: /etc/hosts: No such file or directory`.
**Solution**: Verify file path is correct.
```bash
# Check if file exists
ls -l /etc/hosts

# Verify exact path
sudo find /etc -name "hosts"
```

### **Error: Permissions Still Not Working**
**Symptoms**: User can still access file despite ACL.
**Solution**: Check ACL mask and effective permissions.
```bash
# View effective permissions
getfacl /etc/hosts

# Check mask value
getfacl /etc/hosts | grep mask

# Adjust mask if needed
sudo setfacl -m m::r /etc/hosts
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The primary challenge was implementing granular user-specific permissions beyond standard Unix permissions using Access Control Lists (ACLs). Understanding how ACLs extend traditional permissions and how the ACL mask affects effective permissions is crucial for proper access control.

**💡 Solution Approach**:
1. **Ownership Configuration**: Set file owner and group to root using `chown`.
2. **Base Permissions**: Configured others to read-only using `chmod o=r`.
3. **Deny Access**: Used `setfacl -m u:mark:0` to explicitly deny all permissions to user mark.
4. **Grant Access**: Used `setfacl -m u:ryan:r` to grant read permission to user ryan.
5. **Verification**: Confirmed all settings with `getfacl` and `ls -l`.

**🎯 Key Success Factors**:
- **ACL Understanding**: Grasped how ACLs extend standard Unix permissions.
- **Correct Syntax**: Used proper `setfacl` syntax for user-specific permissions.
- **Permission Notation**: Applied symbolic notation (r, w, x, 0) correctly.
- **Mask Awareness**: Understood ACL mask's role in effective permissions.
- **Verification**: Used `getfacl` to confirm all ACL entries.

**⚠️ Critical Fixes**:
- Always use `sudo` for modifying system file ACLs.
- Verify users exist before setting ACLs for them.
- Use symbolic notation (r, w, x) not octal for ACL permissions.
- Check for `+` symbol in `ls -l` output to confirm ACLs are set.
- Use `getfacl` to verify ACL configuration, not just `ls -l`.

**🔒 Linux ACL Best Practices Applied**:
- **Granular Control**: Used ACLs for user-specific permissions beyond owner/group/others.
- **Explicit Deny**: Set user mark to `---` to explicitly deny access.
- **Least Privilege**: Granted only read permission to user ryan.
- **Verification**: Confirmed configuration with `getfacl`.
- **Documentation**: ACLs are self-documenting via `getfacl` output.

**⚠️ Important ACL Concepts**:
- **ACL Extension**: ACLs extend standard Unix permissions (owner, group, others).
- **ACL Mask**: Controls maximum effective permissions for named users and groups.
- **Effective Permissions**: Actual permissions = ACL entry AND mask.
- **ACL Indicator**: `+` in `ls -l` output indicates ACLs are present.
- **Precedence**: ACLs are checked before standard permissions.

---

## ⚠️ Important Production Notes

🔧 **ACL Support**: Verify filesystem supports ACLs before implementation (ext4, XFS support ACLs).
🔐 **Backup ACLs**: Use `getfacl -R` to backup ACLs before modifications.
📊 **ACL Mask**: Understand mask limits maximum permissions for named users/groups.
🛡️ **Default ACLs**: Set default ACLs on directories to inherit to new files.
🗄️ **ACL Portability**: ACLs may not be preserved when copying files without special flags.
📝 **Documentation**: Document ACL configurations for system administrators.
🔍 **Regular Audits**: Review ACLs regularly to ensure compliance with security policies.
⚙️ **Automation**: Use scripts to apply consistent ACL configurations across systems.

**ACL Command Reference**:
```bash
# Set ACL for user
setfacl -m u:username:rwx file

# Set ACL for group
setfacl -m g:groupname:rx file

# Remove specific ACL entry
setfacl -x u:username file

# Remove all ACLs
setfacl -b file

# Set default ACL (for directories)
setfacl -d -m u:username:rwx directory

# Copy ACLs from one file to another
getfacl file1 | setfacl --set-file=- file2

# Backup ACLs
getfacl -R /path > acl_backup.txt

# Restore ACLs
setfacl --restore=acl_backup.txt
```

**ACL Permission Notation**:
```bash
# Symbolic notation (used with setfacl)
r   = read
w   = write
x   = execute
-   = no permission
0   = no permissions (equivalent to ---)

# Examples:
u:mark:---    # User mark: no permissions
u:ryan:r--    # User ryan: read only
u:admin:rwx   # User admin: read, write, execute
u:dev:rw-     # User dev: read, write
```

**ACL Mask Explanation**:
```bash
# The mask defines maximum effective permissions
# Effective permission = ACL entry AND mask

# Example:
user:ryan:rw-    # Ryan's ACL entry
mask::r--        # Mask limits to read
# Effective: r-- (read only, write is masked out)

# Set mask
sudo setfacl -m m::rwx /etc/hosts

# View mask
getfacl /etc/hosts | grep mask
```

**Default ACLs for Directories**:
```bash
# Set default ACL (inherited by new files)
sudo setfacl -d -m u:ryan:r /data/shared

# View default ACLs
getfacl /data/shared

# New files in /data/shared will inherit ryan's read permission
```

**Security Recommendations**:
- Use ACLs for granular access control beyond standard permissions
- Regularly audit ACL configurations with `getfacl`
- Document ACL policies and user access requirements
- Test ACL configurations before applying to production files
- Use default ACLs on directories for consistent inheritance
- Backup ACLs before making changes
- Monitor ACL changes through system logs
- Implement least privilege principle with ACLs
- Combine ACLs with standard permissions for defense in depth

**ACL vs Standard Permissions**:
```
Standard Permissions:
- Owner, Group, Others (3 categories)
- One permission set per category
- Simple but limited

ACLs:
- Named users and groups (unlimited)
- Multiple permission sets possible
- More complex but flexible
- Extends standard permissions
```

**Copying Files with ACLs**:
```bash
# cp doesn't preserve ACLs by default
cp file1 file2  # ACLs NOT preserved

# Use -a or --preserve=all to preserve ACLs
cp -a file1 file2
cp --preserve=all file1 file2

# rsync preserves ACLs with -A
rsync -aA file1 file2

# tar preserves ACLs with --acls
tar --acls -czf backup.tar.gz /path
```

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Set file owner and group to `root:root`
- Configured others to have read-only permission
- Denied all permissions to user `mark` using ACL
- Granted read-only permission to user `ryan` using ACL
- Verified all configurations with `getfacl` and `ls -l`

**🔍 Verification Completed**:
- File ownership: `root:root` (confirmed with `ls -l`)
- Others permissions: read-only (confirmed with `ls -l`)
- User mark ACL: `---` no permissions (confirmed with `getfacl`)
- User ryan ACL: `r--` read permission (confirmed with `getfacl`)
- ACL indicator: `+` symbol present in `ls -l` output
- Complete ACL configuration verified with `getfacl`

**🚀 Next Steps** (Optional):
- Document ACL configuration in system documentation
- Implement monitoring for ACL changes
- Apply similar ACL configurations to other critical files
- Create backup of ACL configurations
- Train team on ACL management
- Implement automated ACL auditing

**🔒 Security Validation**:
- ✅ File owned by root (prevents unauthorized modification)
- ✅ Others have read-only access (appropriate for /etc/hosts)
- ✅ User mark explicitly denied (security requirement met)
- ✅ User ryan granted minimal required access (least privilege)
- ✅ ACLs properly configured and verified
- ✅ Security audit findings addressed

**📊 Configuration Summary**:
- **File**: `/etc/hosts`
- **Owner**: root
- **Group**: root
- **Base Permissions**: `-rw-r--r--`
- **ACL Entries**:
  - `user:mark:---` (no permissions)
  - `user:ryan:r--` (read only)
- **ACL Indicator**: `+` present in `ls -l`

**🔍 Key Learning Points**:
- ACLs extend standard Unix permissions for granular control
- `setfacl -m u:username:permissions` sets user-specific ACLs
- `getfacl` displays complete ACL configuration
- `+` in `ls -l` indicates ACLs are configured
- ACL mask can limit effective permissions
- Permission notation: `r` (read), `w` (write), `x` (execute), `0` (none)
