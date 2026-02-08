# 🔐 Task 1 - Create Custom Apache User on App Server 2

**📌 Task Description**

In response to heightened security concerns, the **xFusionCorp Industries security team** has opted for custom Apache users for their web applications. Each user is tailored specifically for an application, enhancing security measures through role-based access control and application isolation. The task involves creating a custom Apache user with specific configurations on App Server 2 within the Stratos Datacenter.

**👉 Task Requirements**:
- **Target Server**: App Server 2 (Stratos Datacenter)
- **User Configuration**:
  - Username: `mark`
  - UID: `1872` (custom unique identifier)
  - Home Directory: `/var/www/mark`
  - Purpose: Dedicated Apache web application user
- **Security Objectives**:
  - Application-specific user isolation
  - Custom UID for enhanced tracking and access control
  - Dedicated home directory within Apache web root
- Verify user creation with correct UID and home directory assignment
- Ensure proper directory ownership and permissions

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to App Server 2

```bash
ssh steve@stapp02
```

**Purpose**: Establish SSH connection to App Server 2 where the custom Apache user will be created.

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
id mark
```

**Purpose**: Confirm that the user `mark` does not already exist before creation.

**Expected Output**:
```
id: 'mark': no such user
```

**Success Indicators**:
- ✅ Error message confirms user does not exist
- ✅ Safe to proceed with user creation

**Alternative Check**:
```bash
grep "^mark:" /etc/passwd
```

**Expected Output** (no output indicates user doesn't exist):
```

```

---

## 🔹 Step 3: Create the Custom Apache User

```bash
sudo useradd -u 1872 -d /var/www/mark -m -s /bin/bash mark
```

**Purpose**: Create the user `mark` with custom UID, specified home directory, and bash shell.

**Command Breakdown**:
- `sudo`: Execute with administrative privileges
- `useradd`: User creation command
- `-u 1872`: Assign custom UID (User ID)
- `-d /var/www/mark`: Set home directory path
- `-m`: Create home directory if it doesn't exist
- `-s /bin/bash`: Set default shell to bash
- `mark`: Username to create

**Expected Output**:
```
[no output indicates success]
```

**Alternative Command** (if directory needs to be created separately):
```bash
sudo mkdir -p /var/www/mark
sudo useradd -u 1872 -d /var/www/mark -s /bin/bash mark
```

---

## 🔹 Step 4: Verify User Creation

```bash
id mark
```

**Purpose**: Confirm the user was created with the correct UID and group assignments.

**Expected Output**:
```
uid=1872(mark) gid=1872(mark) groups=1872(mark)
```

**Success Indicators**:
- ✅ UID matches specified value: `1872`
- ✅ Username is `mark`
- ✅ Primary group created automatically: `mark (1872)`

**Additional Verification**:
```bash
grep "^mark:" /etc/passwd
```

**Expected Output**:
```
mark:x:1872:1872::/var/www/mark:/bin/bash
```

**Field Breakdown**:
- `mark`: Username
- `x`: Password placeholder (stored in `/etc/shadow`)
- `1872`: UID
- `1872`: GID (Group ID)
- ``: Comment field (empty)
- `/var/www/mark`: Home directory
- `/bin/bash`: Default shell

---

## 🔹 Step 5: Verify Home Directory Creation

```bash
ls -ld /var/www/mark
```

**Purpose**: Confirm the home directory exists with correct ownership and permissions.

**Expected Output**:
```
drwx------. 2 mark mark 62 Oct 25 16:00 /var/www/mark
```

**Success Indicators**:
- ✅ Directory exists at `/var/www/mark`
- ✅ Owner: `mark`
- ✅ Group: `mark`
- ✅ Permissions: `drwx------` (700) - owner has full access, others have none
- ✅ Directory type indicated by `d` prefix

**Permission Breakdown**:
- `d`: Directory
- `rwx`: Owner (mark) - read, write, execute
- `---`: Group - no permissions
- `---`: Others - no permissions

---

## 🔹 Step 6: Set Proper Directory Ownership (If Needed)

```bash
sudo chown -R mark:mark /var/www/mark
```

**Purpose**: Ensure recursive ownership of the home directory and all contents.

**Command Breakdown**:
- `sudo`: Execute with administrative privileges
- `chown`: Change ownership command
- `-R`: Recursive (apply to directory and all contents)
- `mark:mark`: Set owner to `mark`, group to `mark`
- `/var/www/mark`: Target directory

**Expected Output**:
```
[no output indicates success]
```

**Verification**:
```bash
ls -la /var/www/mark
```

**Expected Output**:
```
total 12
drwx------. 2 mark mark   62 Oct 25 16:00 .
drwxr-xr-x. 5 root root   52 Oct 25 16:00 ..
-rw-r--r--. 1 mark mark   18 Oct 25 16:00 .bash_logout
-rw-r--r--. 1 mark mark  193 Oct 25 16:00 .bash_profile
-rw-r--r--. 1 mark mark  231 Oct 25 16:00 .bashrc
```

**Success Indicators**:
- ✅ All files owned by `mark:mark`
- ✅ Default shell configuration files created
- ✅ Proper permissions on configuration files

---

## 🔹 Step 7: Verify User in Password File

```bash
sudo cat /etc/passwd | grep mark
```

**Purpose**: Confirm user entry exists in the system password database.

**Expected Output**:
```
mark:x:1872:1872::/var/www/mark:/bin/bash
```

---

## 🔹 Step 8: Check User's Group Membership

```bash
groups mark
```

**Purpose**: Display all groups the user belongs to.

**Expected Output**:
```
mark : mark
```

**Success Indicators**:
- ✅ User is member of primary group `mark`
- ✅ Group GID matches UID: `1872`

---

## 🔹 Step 9: Test User Login (Optional)

```bash
sudo su - mark
```

**Purpose**: Verify the user account is functional and can be accessed.

**Expected Output**:
```
[mark@stapp02 ~]$ pwd
/var/www/mark
[mark@stapp02 ~]$ whoami
mark
[mark@stapp02 ~]$ exit
logout
```

**Success Indicators**:
- ✅ Successfully switched to user `mark`
- ✅ Current directory is `/var/www/mark`
- ✅ `whoami` confirms current user is `mark`
- ✅ `exit` returns to previous user

---

## 🔹 Step 10: Set Password (Optional - If Required)

```bash
sudo passwd mark
```

**Purpose**: Set a password for the user if interactive login is required.

**Expected Interaction**:
```
New password: [enter password]
Retype new password: [re-enter password]
passwd: all authentication tokens updated successfully.
```

**Note**: Password may not be required if user is only used for application processes.

---

## 🔹 Step 11: Verify Apache Compatibility (Optional)

```bash
sudo -u mark touch /var/www/mark/test.txt
ls -l /var/www/mark/test.txt
```

**Purpose**: Confirm the user can create files in their home directory.

**Expected Output**:
```
-rw-r--r--. 1 mark mark 0 Oct 25 16:00 /var/www/mark/test.txt
```

**Cleanup**:
```bash
sudo -u mark rm /var/www/mark/test.txt
```

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **App Server 2**:

```bash
# Connect to App Server 2
ssh steve@stapp02

# Verify user doesn't exist
id mark

# Create user with custom UID and home directory
sudo useradd -u 1872 -d /var/www/mark -m -s /bin/bash mark

# Verify user creation
id mark

# Verify home directory
ls -ld /var/www/mark

# Set ownership (if needed)
sudo chown -R mark:mark /var/www/mark

# Verify ownership
ls -la /var/www/mark

# Verify user in system files
grep "^mark:" /etc/passwd

# Test user switch
sudo su - mark
pwd
whoami
exit
```

---

## 💡 Common User Creation Issues & Fixes

### **Issue 1: UID Already in Use**
**Problem**: Error `useradd: UID 1872 is not unique`.
**Fix**: Check existing users and choose a different UID.
```bash
# Find users with UID 1872
grep ":1872:" /etc/passwd

# If UID is in use, choose alternative or remove conflicting user
sudo userdel conflicting_user
```

### **Issue 2: Home Directory Already Exists**
**Problem**: Directory `/var/www/mark` exists with wrong ownership.
**Fix**: Remove directory or change ownership before user creation.
```bash
# Option 1: Remove existing directory
sudo rm -rf /var/www/mark

# Option 2: Keep directory and fix ownership after user creation
sudo useradd -u 1872 -d /var/www/mark -s /bin/bash mark
sudo chown -R mark:mark /var/www/mark
```

### **Issue 3: Permission Denied**
**Problem**: Cannot create user or directory without sudo.
**Fix**: Ensure using sudo for administrative commands.
```bash
# Always use sudo for user management
sudo useradd -u 1872 -d /var/www/mark -m -s /bin/bash mark
```

### **Issue 4: User Cannot Login**
**Problem**: `su - mark` fails or user cannot access home directory.
**Fix**: Verify home directory permissions and shell assignment.
```bash
# Check home directory permissions
ls -ld /var/www/mark

# Fix permissions if needed
sudo chmod 700 /var/www/mark
sudo chown mark:mark /var/www/mark

# Verify shell is valid
grep "^mark:" /etc/passwd | cut -d: -f7
```

---

## 🔧 Troubleshooting Failures

### **Error: User Already Exists**
**Symptoms**: `useradd: user 'mark' already exists`.
**Solution**: Check if user exists and remove if necessary.
```bash
# Verify user exists
id mark

# Remove existing user
sudo userdel -r mark

# Recreate user
sudo useradd -u 1872 -d /var/www/mark -m -s /bin/bash mark
```

### **Error: Cannot Create Directory**
**Symptoms**: Permission denied when creating `/var/www/mark`.
**Solution**: Ensure parent directory exists and has correct permissions.
```bash
# Create parent directory if needed
sudo mkdir -p /var/www

# Set permissions on parent directory
sudo chmod 755 /var/www

# Retry user creation
sudo useradd -u 1872 -d /var/www/mark -m -s /bin/bash mark
```

### **Error: Invalid Shell**
**Symptoms**: User cannot login or shell is invalid.
**Solution**: Verify shell path and ensure it's listed in `/etc/shells`.
```bash
# Check valid shells
cat /etc/shells

# Update user's shell if needed
sudo usermod -s /bin/bash mark
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The primary challenge was creating a custom Apache user with specific UID and home directory requirements while ensuring proper security isolation for web application purposes.

**💡 Solution Approach**:
1. **Pre-verification**: Confirmed user `mark` did not exist to avoid conflicts.
2. **Custom UID Assignment**: Used `-u 1872` flag to assign specific UID for tracking.
3. **Home Directory Configuration**: Set home directory to `/var/www/mark` using `-d` flag with `-m` to auto-create.
4. **Ownership Assignment**: Applied recursive ownership using `chown -R` to ensure all files belong to the user.
5. **Verification**: Confirmed user creation, UID, home directory, and ownership through multiple validation checks.

**🎯 Key Success Factors**:
- **Correct UID Assignment**: Used exact UID `1872` as specified.
- **Proper Home Directory**: Created directory within Apache web root (`/var/www`).
- **Ownership Verification**: Ensured user owns all files in home directory.
- **Shell Assignment**: Set `/bin/bash` for potential interactive use.
- **Security Isolation**: User has dedicated directory with restrictive permissions.

**⚠️ Critical Fixes**:
- Always verify user doesn't exist before creation to avoid conflicts.
- Use `-m` flag to automatically create home directory with proper skeleton files.
- Apply `chown -R` to ensure recursive ownership of all directory contents.
- Verify UID is unique to prevent authentication issues.

**🔒 Linux User Management Best Practices Applied**:
- **Custom UID**: Assigned specific UID outside standard user range for easier identification.
- **Dedicated Home Directory**: Isolated user's files within application-specific path.
- **Principle of Least Privilege**: User has access only to their own directory.
- **Shell Access**: Configured bash shell for potential administrative needs.
- **Ownership Consistency**: Applied recursive ownership to prevent permission issues.

**⚠️ Important Security Concepts**:
- **UID Uniqueness**: Each user must have a unique UID for proper system identification.
- **Home Directory Isolation**: Application users should have dedicated directories.
- **Permission Hardening**: Default 700 permissions prevent unauthorized access.
- **Group Management**: Auto-created group matches username for simplified administration.

---

## ⚠️ Important Production Notes

🔧 **User Management**: Always verify user doesn't exist before creation using `id` command.
🔐 **Password Security**: Set strong passwords using `passwd` if interactive login is required.
📊 **UID Planning**: Document custom UIDs to prevent conflicts in multi-server environments.
🛡️ **Permission Hardening**: Apply restrictive permissions (700) to sensitive directories.
🗄️ **Backup Considerations**: Include `/etc/passwd`, `/etc/shadow`, and `/etc/group` in backup strategies.
📝 **Audit Trail**: Log user creation activities for compliance and security tracking.
🔍 **Regular Audits**: Periodically review user accounts to identify unused or orphaned accounts.
⚙️ **Automation**: Consider using configuration management tools (Ansible, Puppet) for consistent user creation across multiple servers.

**Apache Integration Notes**:
- Configure Apache to run processes under the `mark` user for application isolation
- Update Apache configuration (`httpd.conf`) to specify `User mark` and `Group mark`
- Ensure web content is owned by `mark:mark` for proper access control
- Use `sudo -u mark` to perform operations as the application user

**Security Recommendations**:
- Disable password login if user is only for service accounts
- Implement SSH key-based authentication if remote access is needed
- Use `nologin` shell if interactive login is not required: `sudo usermod -s /sbin/nologin mark`
- Apply SELinux contexts if running on SELinux-enabled systems
- Monitor user activity through system logs (`/var/log/secure`)


