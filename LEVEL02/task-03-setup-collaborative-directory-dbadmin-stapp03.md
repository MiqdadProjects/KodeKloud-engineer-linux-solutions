# 🔐 Task 3 - Setup Collaborative Directory with SGID on App Server 3

**📌 Task Description**

The **Nautilus team** doesn't want its data to be accessed by any of the other groups/teams due to security reasons and wants their data to be strictly accessed by the **dbadmin group** of the team. This task involves creating a collaborative directory with proper ownership, permissions, and the **SGID (Set Group ID)** bit to ensure all files created within inherit the group ownership.

**👉 Task Requirements**:
- **Target Server**: App Server 3 (stapp03) in Stratos Datacenter
- **Directory Path**: `/dbadmin/data`
- **Group Owner**: `dbadmin`
- **Permissions**: Read/Write/Execute for user and group owners only
- **Special Permission**: SGID bit to ensure group inheritance
- **Operation Objectives**:
  - Create directory structure `/dbadmin/data`
  - Set group ownership to `dbadmin`
  - Configure permissions: `2770` (rwxrws---)
  - Verify SGID bit is set for group inheritance
- Ensure strict access control (no access for others)
- Enable collaborative work within group

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to App Server 3

```bash
ssh banner@stapp03
```

**Purpose**: Establish SSH connection to App Server 3 for directory configuration.

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

## 🔹 Step 2: Switch to Root User

```bash
sudo su -
```

**Purpose**: Switch to root user for administrative operations.

**Expected Output**:
```
[root@stapp03 ~]#
```

**Success Indicators**:
- ✅ Prompt shows root user (#)
- ✅ Full administrative privileges available

**Alternative**: Use `sudo` prefix for each command instead of switching to root.

---

## 🔹 Step 3: Verify dbadmin Group Exists (Optional)

```bash
getent group dbadmin
```

**Purpose**: Confirm the dbadmin group exists on the system.

**Expected Output**:
```
dbadmin:x:1005:
```

**Success Indicators**:
- ✅ Group exists
- ✅ Group ID displayed
- ✅ Ready to use for ownership

**Note**: If group doesn't exist, create it with `groupadd dbadmin`.

---

## 🔹 Step 4: Create Directory Structure

```bash
mkdir -p /dbadmin/data
```

**Purpose**: Create the collaborative directory with parent directories if needed.

**Command Breakdown**:
- `mkdir`: Make directory command
- `-p`: Create parent directories as needed
- `/dbadmin/data`: Full path to create

**Expected Output**:
```
[no output indicates success]
```

**Success Indicators**:
- ✅ Directory created
- ✅ Parent directory `/dbadmin` also created
- ✅ No error messages

---

## 🔹 Step 5: Set Ownership to root:dbadmin

```bash
chown root:dbadmin /dbadmin/data
```

**Purpose**: Set the directory owner to root and group owner to dbadmin.

**Command Breakdown**:
- `chown`: Change ownership command
- `root`: User owner
- `dbadmin`: Group owner
- `/dbadmin/data`: Target directory

**Expected Output**:
```
[no output indicates success]
```

**Success Indicators**:
- ✅ Ownership changed
- ✅ User owner: root
- ✅ Group owner: dbadmin

---

## 🔹 Step 6: Set Permissions with SGID Bit

```bash
chmod 2770 /dbadmin/data
```

**Purpose**: Set permissions to rwxrws--- with SGID bit for group inheritance.

**Command Breakdown**:
- `chmod`: Change mode (permissions) command
- `2770`: Octal permission notation
  - `2`: SGID (Set Group ID) bit
  - `7`: rwx (read/write/execute) for user owner
  - `7`: rwx (read/write/execute) for group owner
  - `0`: --- (no permissions) for others

**Expected Output**:
```
[no output indicates success]
```

**Success Indicators**:
- ✅ Permissions set
- ✅ SGID bit enabled
- ✅ Group members can read/write/execute
- ✅ Others have no access

**⚠️ Important**: The `2` in `2770` sets the SGID bit, ensuring files created inside inherit the `dbadmin` group.

---

## 🔹 Step 7: Verify Directory Configuration

```bash
ls -ld /dbadmin/data
```

**Purpose**: Confirm ownership and permissions are set correctly.

**Command Breakdown**:
- `ls`: List command
- `-l`: Long format (shows permissions and ownership)
- `-d`: Show directory itself, not contents
- `/dbadmin/data`: Target directory

**Expected Output**:
```
drwxrws--- 2 root dbadmin 4096 Feb 09 18:30 /dbadmin/data
```

**Success Indicators**:
- ✅ First character is `d` (directory)
- ✅ Permissions show `rwxrws---`
- ✅ `s` in group execute position indicates SGID bit
- ✅ Owner is `root`
- ✅ Group is `dbadmin`

**Permission Breakdown**:
- `d`: Directory
- `rwx`: User (root) has read, write, execute
- `rws`: Group (dbadmin) has read, write, execute + SGID
- `---`: Others have no permissions

---

## 🔹 Step 8: Test SGID Functionality (Optional)

```bash
touch /dbadmin/data/test_file
ls -l /dbadmin/data/test_file
```

**Purpose**: Verify that files created inside inherit the dbadmin group.

**Expected Output**:
```
-rw-r--r-- 1 root dbadmin 0 Feb 09 18:35 /dbadmin/data/test_file
```

**Success Indicators**:
- ✅ File created successfully
- ✅ Group owner is `dbadmin` (inherited from directory)
- ✅ SGID working correctly

**Cleanup**:
```bash
rm /dbadmin/data/test_file
```

---

## 🔹 Step 9: Verify Parent Directory Permissions (Optional)

```bash
ls -ld /dbadmin
```

**Purpose**: Check parent directory permissions for security.

**Expected Output**:
```
drwxr-xr-x 3 root root 4096 Feb 09 18:30 /dbadmin
```

**Success Indicators**:
- ✅ Parent directory exists
- ✅ Permissions appropriate for parent

**Note**: Parent directory typically has different permissions than the collaborative subdirectory.

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **App Server 3**:

```bash
# Connect to App Server 3
ssh banner@stapp03

# Switch to root
sudo su -

# Verify group exists (optional)
getent group dbadmin

# Create directory
mkdir -p /dbadmin/data

# Set ownership
chown root:dbadmin /dbadmin/data

# Set permissions with SGID
chmod 2770 /dbadmin/data

# Verify configuration
ls -ld /dbadmin/data

# Test SGID (optional)
touch /dbadmin/data/test_file
ls -l /dbadmin/data/test_file
rm /dbadmin/data/test_file

# Exit root
exit
```

---

## 💡 Common Directory Permission Issues & Fixes

### **Issue 1: SGID Bit Not Set**
**Problem**: Files don't inherit group ownership.
**Fix**: Ensure SGID bit is set with leading `2` in chmod.
```bash
# Check if SGID is set (look for 's' in group permissions)
ls -ld /dbadmin/data

# Set SGID bit
chmod 2770 /dbadmin/data

# Or use symbolic notation
chmod g+s /dbadmin/data
```

### **Issue 2: Wrong Group Ownership**
**Problem**: Directory owned by wrong group.
**Fix**: Use chown to set correct group.
```bash
# Check current ownership
ls -ld /dbadmin/data

# Set correct group
chown root:dbadmin /dbadmin/data

# Or change only group
chgrp dbadmin /dbadmin/data
```

### **Issue 3: Others Have Access**
**Problem**: Security requirement violated.
**Fix**: Ensure last digit in permissions is 0.
```bash
# Remove all permissions for others
chmod 2770 /dbadmin/data

# Verify
ls -ld /dbadmin/data
# Should show: drwxrws---
```

### **Issue 4: Group Members Can't Write**
**Problem**: Group members get permission denied.
**Fix**: Ensure group has write permission.
```bash
# Set full permissions for group
chmod 2770 /dbadmin/data

# Verify group has 'w' permission
ls -ld /dbadmin/data
# Should show: drwxrws---
```

### **Issue 5: Directory Doesn't Exist**
**Problem**: Parent directory not created.
**Fix**: Use -p flag with mkdir.
```bash
# Create with parent directories
mkdir -p /dbadmin/data

# Verify
ls -ld /dbadmin
ls -ld /dbadmin/data
```

---

## 🔧 Troubleshooting Failures

### **Error: Permission Denied**
**Symptoms**: Cannot create directory or modify permissions.
**Solution**: Use sudo or switch to root user.
```bash
# Use sudo for each command
sudo mkdir -p /dbadmin/data
sudo chown root:dbadmin /dbadmin/data
sudo chmod 2770 /dbadmin/data

# Or switch to root
sudo su -
```

### **Error: Group Does Not Exist**
**Symptoms**: `chown: invalid group: 'dbadmin'`.
**Solution**: Create the group first.
```bash
# Check if group exists
getent group dbadmin

# Create group if needed
sudo groupadd dbadmin

# Then set ownership
sudo chown root:dbadmin /dbadmin/data
```

### **Error: SGID Not Working**
**Symptoms**: New files don't inherit group ownership.
**Solution**: Verify SGID bit is set and filesystem supports it.
```bash
# Check current permissions
ls -ld /dbadmin/data

# Should show 's' in group execute position: drwxrws---

# Re-apply SGID if needed
sudo chmod 2770 /dbadmin/data

# Test
sudo touch /dbadmin/data/test
ls -l /dbadmin/data/test
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The primary challenge was creating a collaborative directory with strict access control using the SGID (Set Group ID) bit to ensure all files created within automatically inherit the group ownership. Understanding octal permissions and special bits is crucial for proper collaborative directory setup.

**💡 Solution Approach**:
1. **Directory Creation**: Used `mkdir -p` to create directory structure.
2. **Ownership Configuration**: Set owner to root and group to dbadmin.
3. **SGID Permission**: Applied `2770` to enable SGID and restrict access.
4. **Verification**: Checked with `ls -ld` to confirm configuration.
5. **Testing**: Created test file to verify SGID inheritance.

**🎯 Key Success Factors**:
- **SGID Bit**: The `2` in `2770` ensures group inheritance.
- **Octal Notation**: `2770` = SGID + rwxrwx--- permissions.
- **Group Ownership**: Critical for collaborative access.
- **No Others Access**: `0` in last position ensures security.
- **Verification**: `ls -ld` shows `s` in group execute position.

**⚠️ Critical Fixes**:
- Always use leading `2` in chmod for SGID bit.
- Ensure group exists before setting ownership.
- Verify `s` appears in group permissions (drwxrws---).
- Test file creation to confirm SGID works.
- Use `chmod 2770` not `chmod 770` (missing SGID).

**🔒 Linux Permission Best Practices Applied**:
- **Least Privilege**: Others have no access (security requirement).
- **SGID for Collaboration**: Ensures consistent group ownership.
- **Clear Ownership**: root:dbadmin ownership structure.
- **Verification**: Always verify with ls -ld after changes.
- **Testing**: Test functionality with actual file creation.

**⚠️ Important Permission Concepts**:
- **SGID Bit**: Files created inside inherit directory's group.
- **Octal Notation**: 4-digit format includes special bits.
- **Special Bits**: SUID(4), SGID(2), Sticky(1).
- **Symbolic Display**: `s` in execute position shows SGID.
- **Inheritance**: Only works for files created after SGID is set.

---

## ⚠️ Important Production Notes

🔧 **SGID Requirement**: Essential for collaborative directories to maintain group ownership.
🔐 **Security**: No access for others ensures data protection.
📊 **Octal Permissions**: `2770` = SGID + rwxrwx---.
🛡️ **Group Management**: Ensure all team members are in dbadmin group.
🗄️ **Parent Permissions**: Parent directory permissions affect access.
📝 **Documentation**: Document group membership and access policies.
🔍 **Monitoring**: Audit directory access regularly.
⚙️ **Automation**: Use configuration management for consistent setup.

**Permission Octal Reference**:
```
Special Bits (first digit):
4 = SUID (Set User ID)
2 = SGID (Set Group ID)
1 = Sticky Bit

Standard Permissions (next three digits):
4 = Read (r)
2 = Write (w)
1 = Execute (x)

Examples:
2770 = SGID + rwxrwx---
1777 = Sticky + rwxrwxrwx
4755 = SUID + rwxr-xr-x
```

**SGID Behavior**:
```bash
# Without SGID (normal directory)
mkdir /normal
chmod 770 /normal
touch /normal/file
ls -l /normal/file
# Group = creator's primary group

# With SGID (collaborative directory)
mkdir /collab
chmod 2770 /collab
chown root:dbadmin /collab
touch /collab/file
ls -l /collab/file
# Group = dbadmin (inherited from directory)
```

**Security Recommendations**:
- Use SGID for all collaborative directories
- Restrict others access (last digit = 0)
- Regular group membership audits
- Monitor directory access logs
- Implement file retention policies
- Use ACLs for more granular control if needed
- Document access requirements
- Regular permission audits

**Collaborative Directory Patterns**:
```bash
# Team collaboration (SGID)
mkdir -p /team/shared
chown root:teamgroup /team/shared
chmod 2770 /team/shared

# Project directory (SGID + Sticky)
mkdir -p /projects/alpha
chown root:projectgroup /projects/alpha
chmod 3770 /projects/alpha  # SGID + Sticky

# Drop box (write-only for others)
mkdir -p /dropbox
chown root:staff /dropbox
chmod 2733 /dropbox  # SGID + rwx-wx-wx
```

**Group Management**:
```bash
# Create group
sudo groupadd dbadmin

# Add users to group
sudo usermod -aG dbadmin username

# Verify group membership
getent group dbadmin
groups username

# Remove user from group
sudo gpasswd -d username dbadmin
```

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Created collaborative directory `/dbadmin/data` on App Server 3
- Set group ownership to `dbadmin`
- Configured permissions to `2770` (rwxrws---)
- Enabled SGID bit for group inheritance
- Verified configuration with ls -ld

**🔍 Verification Completed**:
- Directory exists: `/dbadmin/data`
- Ownership: `root:dbadmin`
- Permissions: `drwxrws---` (2770)
- SGID bit: Set (visible as `s` in group permissions)
- Access control: User and group have full access, others have none

**🚀 Next Steps** (Optional):
- Add users to dbadmin group as needed
- Test collaborative access with group members
- Implement backup strategy for directory
- Set up monitoring for directory access
- Document group membership procedures
- Create subdirectories with inherited permissions

**🔒 Configuration Validation**:
- ✅ Directory created at correct path
- ✅ Group ownership set to dbadmin
- ✅ SGID bit enabled for inheritance
- ✅ User and group have rwx permissions
- ✅ Others have no access (security requirement met)
- ✅ Files created inside will inherit dbadmin group

**📊 Configuration Summary**:
- **Server**: App Server 3 (stapp03)
- **Directory**: /dbadmin/data
- **Owner**: root
- **Group**: dbadmin
- **Permissions**: 2770 (drwxrws---)
- **Special Bit**: SGID (Set Group ID)
- **Access**: User and group only, no others

**🔍 Key Learning Points**:
- SGID bit (2 in chmod) ensures files inherit directory's group
- Octal notation: 2770 = SGID + rwxrwx---
- `s` in group permissions indicates SGID is set
- Collaborative directories require SGID for consistent ownership
- Security achieved by setting others permissions to 0
- `ls -ld` verifies directory permissions and ownership
