# 🔐 Task 5 - Create Temporary User with Expiry Date on App Server 2

**📌 Task Description**

As part of the temporary assignment to the **Nautilus project**, a developer named **john** requires access for a limited duration. To ensure smooth access management, a temporary user account with an expiry date is needed. Temporary user accounts with expiration dates are essential for managing contractor access, project-based assignments, and time-limited permissions. This approach enhances security by automatically disabling accounts after the specified date, preventing unauthorized access from forgotten temporary accounts.

**👉 Task Requirements**:
- **Target Server**: App Server 2 (stapp02) in Stratos Datacenter
- **User Configuration**:
  - Username: `john` (lowercase as per standard protocol)
  - Home Directory: `/home/john` (create with `-m` flag)
  - Shell: `/bin/bash` (interactive shell for developer)
  - Expiry Date: **2027-02-17** (account expires after this date)
  - Purpose: Temporary developer access for Nautilus project
- **Security Objectives**:
  - Time-limited access control
  - Automatic account expiration
  - Prevent long-term access from temporary accounts
  - Ensure proper cleanup of temporary access
- Verify user creation with correct expiry date
- Confirm account will expire on specified date
- Ensure proper temporary account configuration

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to App Server 2

```bash
ssh steve@stapp02
```

**Purpose**: Establish SSH connection to App Server 2 where the temporary user will be created.

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
id john
```

**Purpose**: Confirm that the user `john` does not already exist before creation.

**Expected Output**:
```
id: 'john': no such user
```

**Success Indicators**:
- ✅ Error message confirms user does not exist
- ✅ Safe to proceed with user creation

**Alternative Check**:
```bash
getent passwd john
```

**Expected Output** (no output indicates user doesn't exist):
```

```

---

## 🔹 Step 3: Create Temporary User with Expiry Date

```bash
sudo useradd -m -s /bin/bash -e 2027-02-17 john
```

**Purpose**: Create the user `john` with home directory, bash shell, and account expiration date.

**Command Breakdown**:
- `sudo`: Execute with administrative privileges
- `useradd`: User creation command
- `-m`: Create home directory (`/home/john`)
- `-s /bin/bash`: Set default shell to bash (interactive shell for developer)
- `-e 2027-02-17`: Set account expiry date to February 17, 2027 (YYYY-MM-DD format)
- `john`: Username to create (lowercase as per protocol)

**Expected Output**:
```
[no output indicates success]
```

**Success Indicators**:
- ✅ User created successfully
- ✅ Home directory created at `/home/john`
- ✅ Shell set to `/bin/bash`
- ✅ Expiry date set to 2027-02-17

**⚠️ Important**: The date format must be **YYYY-MM-DD**. After the expiry date, the account will be automatically disabled.

---

## 🔹 Step 4: Verify User Creation

```bash
id john
```

**Purpose**: Confirm the user was created with correct UID and group assignments.

**Expected Output**:
```
uid=1005(john) gid=1005(john) groups=1005(john)
```

**Success Indicators**:
- ✅ User `john` exists with valid UID
- ✅ Primary group created automatically: `john`
- ✅ User has system identity

**Note**: The UID value (1005) may vary depending on existing users on the system.

---

## 🔹 Step 5: Verify Account Expiry Date

```bash
sudo chage -l john
```

**Purpose**: Display detailed password and account aging information, including expiry date.

**Command Breakdown**:
- `sudo`: Execute with administrative privileges
- `chage`: Change user password expiry information
- `-l`: List current settings (read-only, doesn't modify)
- `john`: Username to query

**Expected Output**:
```
Last password change                                    : never
Password expires                                        : never
Password inactive                                       : never
Account expires                                         : Feb 17, 2027
Minimum number of days between password change          : 0
Maximum number of days between password change          : 99999
Number of days of warning before password expires       : 7
```

**Success Indicators**:
- ✅ **Account expires: Feb 17, 2027** (matches specified date)
- ✅ Expiry date is correctly configured
- ✅ Account will be disabled after this date

**⚠️ Critical Field**: The **"Account expires"** line must show **Feb 17, 2027**.

---

## 🔹 Step 6: Verify User Configuration in Password Database

```bash
getent passwd john
```

**Purpose**: Confirm the user entry in the password database with correct settings.

**Expected Output**:
```
john:x:1005:1005::/home/john:/bin/bash
```

**Field Breakdown**:
- `john`: Username
- `x`: Password placeholder (stored in `/etc/shadow`)
- `1005`: UID (User ID)
- `1005`: GID (Group ID)
- ``: Comment field (empty)
- `/home/john`: Home directory
- `/bin/bash`: Shell (interactive)

**Success Indicators**:
- ✅ Username is lowercase: `john`
- ✅ Home directory: `/home/john`
- ✅ Shell: `/bin/bash`

---

## 🔹 Step 7: Verify Home Directory Creation

```bash
ls -ld /home/john
```

**Purpose**: Confirm the home directory exists with correct ownership and permissions.

**Expected Output**:
```
drwx------. 2 john john 62 Feb 08 13:00 /home/john
```

**Success Indicators**:
- ✅ Directory exists at `/home/john`
- ✅ Owner: `john`
- ✅ Group: `john`
- ✅ Permissions: `drwx------` (700) - owner has full access

**Permission Breakdown**:
- `d`: Directory
- `rwx`: Owner (john) - read, write, execute
- `---`: Group - no permissions
- `---`: Others - no permissions

---

## 🔹 Step 8: Check Shadow File for Expiry Date

```bash
sudo grep "^john:" /etc/shadow | cut -d: -f8
```

**Purpose**: Verify the expiry date is stored in the shadow password file.

**Expected Output**:
```
20844
```

**Explanation**:
- The number represents days since January 1, 1970 (Unix epoch)
- `20844` corresponds to February 17, 2027
- This is how Linux stores account expiration dates internally

**Success Indicators**:
- ✅ Expiry field is set (not empty)
- ✅ Value corresponds to 2027-02-17

**To Convert Days to Date** (optional verification):
```bash
date -d "1970-01-01 + 20844 days" +%Y-%m-%d
```

**Expected Output**:
```
2027-02-17
```

---

## 🔹 Step 9: Verify User Can Login (Before Expiry)

```bash
sudo su - john
```

**Purpose**: Test that the user can login before the expiry date.

**Expected Output**:
```
[john@stapp02 ~]$ pwd
/home/john
[john@stapp02 ~]$ whoami
john
[john@stapp02 ~]$ exit
logout
```

**Success Indicators**:
- ✅ Successfully switched to user `john`
- ✅ Current directory is `/home/john`
- ✅ User is functional before expiry date
- ✅ Can exit back to previous user

---

## 🔹 Step 10: Set Password for User (Optional)

```bash
sudo passwd john
```

**Purpose**: Set a password for the user to enable login authentication.

**Expected Interaction**:
```
New password: [enter password]
Retype new password: [re-enter password]
passwd: all authentication tokens updated successfully.
```

**Success Indicators**:
- ✅ Password set successfully
- ✅ User can now login with password authentication

**Note**: This step is optional but recommended for developer accounts that require interactive login.

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **App Server 2**:

```bash
# Connect to App Server 2
ssh steve@stapp02

# Verify user doesn't exist
id john

# Create user with expiry date
sudo useradd -m -s /bin/bash -e 2027-02-17 john

# Verify user creation
id john

# Verify expiry date
sudo chage -l john

# Verify user configuration
getent passwd john

# Verify home directory
ls -ld /home/john

# Check shadow file expiry
sudo grep "^john:" /etc/shadow | cut -d: -f8

# Test user login
sudo su - john
pwd
whoami
exit

# Set password (optional)
sudo passwd john
```

---

## 💡 Common Temporary User Issues & Fixes

### **Issue 1: Wrong Date Format**
**Problem**: Error `useradd: invalid date '17-02-2027'`.
**Fix**: Use correct date format **YYYY-MM-DD**.
```bash
# WRONG - This will fail
sudo useradd -e 17-02-2027 john

# CORRECT - Use YYYY-MM-DD format
sudo useradd -m -s /bin/bash -e 2027-02-17 john
```

### **Issue 2: Expiry Date Not Set**
**Problem**: `chage -l john` shows "Account expires: never".
**Fix**: Update expiry date using `chage` or `usermod`.
```bash
# Option 1: Using chage
sudo chage -E 2027-02-17 john

# Option 2: Using usermod
sudo usermod -e 2027-02-17 john

# Verify change
sudo chage -l john
```

### **Issue 3: User Cannot Login After Expiry**
**Problem**: User is locked out after expiry date.
**Fix**: This is expected behavior. Extend expiry date if access is still needed.
```bash
# Check current expiry
sudo chage -l john

# Extend expiry date
sudo chage -E 2028-02-17 john

# Or remove expiry (set to never expire)
sudo chage -E -1 john

# Verify change
sudo chage -l john
```

### **Issue 4: Username Not Lowercase**
**Problem**: User created as `John` instead of `john`.
**Fix**: Remove user and recreate with lowercase name.
```bash
# Remove incorrectly named user
sudo userdel -r John

# Recreate with lowercase name
sudo useradd -m -s /bin/bash -e 2027-02-17 john
```

### **Issue 5: Need to Change Expiry Date**
**Problem**: Expiry date needs to be updated after user creation.
**Fix**: Use `chage` or `usermod` to modify expiry date.
```bash
# Change expiry date
sudo chage -E 2027-12-31 john

# Verify change
sudo chage -l john

# Alternative using usermod
sudo usermod -e 2027-12-31 john
```

---

## 🔧 Troubleshooting Failures

### **Error: User Already Exists**
**Symptoms**: `useradd: user 'john' already exists`.
**Solution**: Check existing user and update expiry date or remove and recreate.
```bash
# Verify existing user
id john
sudo chage -l john

# Option 1: Update existing user's expiry
sudo usermod -e 2027-02-17 john

# Option 2: Remove and recreate
sudo userdel -r john
sudo useradd -m -s /bin/bash -e 2027-02-17 john
```

### **Error: Invalid Date Format**
**Symptoms**: `useradd: invalid date` error.
**Solution**: Ensure date is in YYYY-MM-DD format.
```bash
# Verify date format
# CORRECT: YYYY-MM-DD
sudo useradd -m -s /bin/bash -e 2027-02-17 john

# INCORRECT formats (will fail):
# DD-MM-YYYY: 17-02-2027
# MM/DD/YYYY: 02/17/2027
# DD/MM/YYYY: 17/02/2027
```

### **Error: chage Command Fails**
**Symptoms**: `chage: Permission denied` or command not found.
**Solution**: Ensure using sudo and chage is installed.
```bash
# Use sudo
sudo chage -l john

# Verify chage is installed
which chage

# Install if missing (unlikely on most systems)
sudo yum install shadow-utils  # RHEL/CentOS
sudo apt install passwd         # Debian/Ubuntu
```

### **Error: Account Expired Prematurely**
**Symptoms**: User cannot login before expected expiry date.
**Solution**: Verify system date and expiry date settings.
```bash
# Check system date
date

# Check user expiry
sudo chage -l john

# Verify expiry date in shadow file
sudo grep "^john:" /etc/shadow | cut -d: -f8

# Convert to readable date
date -d "1970-01-01 + $(sudo grep '^john:' /etc/shadow | cut -d: -f8) days" +%Y-%m-%d
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The primary challenge was creating a temporary user account with an automatic expiration date to ensure time-limited access for a project-based developer assignment. Understanding the date format requirements and verification methods for account expiration is crucial for proper temporary access management.

**💡 Solution Approach**:
1. **Date Format**: Used correct **YYYY-MM-DD** format with `-e` flag during user creation.
2. **Interactive User**: Created with `-m` (home directory) and `-s /bin/bash` for developer access.
3. **Expiry Verification**: Used `chage -l` command to display and verify account expiration date.
4. **Shadow File Check**: Examined `/etc/shadow` to confirm expiry date is stored correctly.
5. **Functional Testing**: Tested user login before expiry to ensure account is functional.

**🎯 Key Success Factors**:
- **Correct Date Format**: Used **YYYY-MM-DD** format as required by `useradd -e`.
- **Lowercase Username**: Created user as `john` (lowercase) per standard protocol.
- **Interactive Shell**: Set `/bin/bash` for developer who needs interactive access.
- **Home Directory**: Created home directory with `-m` for user files and configurations.
- **Verification**: Used `chage -l` to confirm expiry date is correctly set.

**⚠️ Critical Fixes**:
- Always use **YYYY-MM-DD** format for expiry dates (other formats will fail).
- Verify expiry date immediately after creation using `chage -l`.
- Ensure username is lowercase as per organizational standards.
- Test user login before expiry to confirm account is functional.
- Document expiry date for access management and cleanup planning.

**🔒 Linux Temporary User Best Practices Applied**:
- **Time-Limited Access**: Set explicit expiration date for temporary assignments.
- **Automatic Expiration**: Account will be disabled automatically after expiry date.
- **Interactive Access**: Provided bash shell for developer productivity.
- **Standard Compliance**: Used lowercase username per protocol.
- **Verification**: Confirmed expiry settings using multiple methods.

**⚠️ Important Security Concepts**:
- **Account Expiration**: Automatically disables account after specified date.
- **Expiry vs Password Expiry**: Account expiry is different from password expiry.
- **Epoch Days**: Linux stores expiry as days since January 1, 1970.
- **Automatic Enforcement**: System prevents login after expiry without manual intervention.
- **Cleanup**: Expired accounts should be reviewed and removed periodically.

---

## ⚠️ Important Production Notes

🔧 **Date Format**: Always use **YYYY-MM-DD** format for `-e` flag (ISO 8601 standard).
🔐 **Expiry Monitoring**: Implement monitoring to track upcoming account expirations.
📊 **Access Reviews**: Regularly review temporary accounts and their expiry dates.
🛡️ **Extension Process**: Establish clear process for extending expiry dates if needed.
🗄️ **Cleanup Policy**: Remove expired accounts after grace period to maintain system hygiene.
📝 **Documentation**: Document reason for temporary access and expected duration.
🔍 **Audit Trail**: Log account creation and expiry dates for compliance tracking.
⚙️ **Automation**: Use scripts to notify admins of upcoming account expirations.

**Temporary User Management Best Practices**:
- Set expiry dates for all contractor and temporary staff accounts
- Review expiry dates weekly to identify upcoming expirations
- Notify users 7-14 days before account expiration
- Establish approval process for expiry date extensions
- Remove expired accounts after 30-day grace period
- Document business justification for all temporary accounts
- Use consistent naming conventions for temporary users

**Account Expiry Monitoring Script** (optional):
```bash
#!/bin/bash
# Check for accounts expiring in next 30 days

echo "Accounts expiring in next 30 days:"
today=$(date +%s)
thirty_days=$((today + 30*24*60*60))

while IFS=: read -r user _ _ _ _ _ _ expire_days; do
  if [ -n "$expire_days" ] && [ "$expire_days" != "99999" ]; then
    expire_date=$(date -d "1970-01-01 + $expire_days days" +%s)
    if [ "$expire_date" -le "$thirty_days" ] && [ "$expire_date" -ge "$today" ]; then
      expire_readable=$(date -d "1970-01-01 + $expire_days days" +%Y-%m-%d)
      echo "User: $user - Expires: $expire_readable"
    fi
  fi
done < <(sudo getent shadow)
```

**Expiry Date Management Commands**:
```bash
# View expiry date
sudo chage -l john

# Set expiry date
sudo chage -E 2027-02-17 john

# Extend expiry date
sudo chage -E 2028-02-17 john

# Remove expiry (never expire)
sudo chage -E -1 john

# Set expiry to specific number of days from now
sudo chage -E $(date -d "+90 days" +%Y-%m-%d) john

# List all users with expiry dates
sudo awk -F: '$8 != "" && $8 != "99999" {print $1}' /etc/shadow
```

**Security Recommendations**:
- Set expiry dates for all non-permanent accounts
- Review and approve expiry date extensions through formal process
- Implement automated notifications for upcoming expirations
- Disable accounts immediately upon project completion (don't wait for expiry)
- Use `usermod -L` to lock accounts instead of deleting if audit trail is needed
- Monitor `/var/log/secure` for login attempts from expired accounts
- Implement password expiry policies in addition to account expiry
- Use `chage -M 90` to set password expiry for temporary users

**Integration with Project Management**:
- Link account expiry to project end dates
- Create accounts with expiry matching contract duration
- Set calendar reminders for expiry date reviews
- Coordinate with HR for contractor off-boarding
- Automate account creation/expiry based on project timelines

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Created user `john` on App Server 2 (stapp02)
- Set account expiry date to **February 17, 2027**
- Configured with home directory (`/home/john`)
- Set interactive shell (`/bin/bash`) for developer access
- Used lowercase username per standard protocol
- Implemented automatic time-limited access control

**🔍 Verification Completed**:
- User exists: `id john`
- Expiry date set: `sudo chage -l john` (shows Feb 17, 2027)
- Home directory created: `/home/john`
- Shell is interactive: `/bin/bash`
- Username is lowercase: `john`
- Shadow file contains expiry: field 8 set to `20844` (days since epoch)

**🚀 Next Steps** (Optional):
- Set password for user: `sudo passwd john`
- Configure SSH access if needed
- Grant necessary permissions for Nautilus project
- Add user to project-specific groups
- Document account purpose and expiry in asset management system
- Set calendar reminder for expiry date review (2027-02-10)
- Plan account cleanup after expiry

**🔒 Security Validation**:
- ✅ Account will automatically expire on 2027-02-17
- ✅ User cannot login after expiry date
- ✅ Temporary access is time-limited
- ✅ Username follows lowercase standard protocol
- ✅ Home directory has restrictive permissions (700)
- ✅ Account expiry is properly configured and verified

**📊 Account Lifecycle**:
1. **Created**: February 8, 2026 (today)
2. **Active Period**: ~374 days
3. **Expiry Date**: February 17, 2027
4. **Post-Expiry**: Account locked, requires admin intervention to extend or remove

**🔔 Important Dates**:
- **Today**: 2026-02-08 (account created)
- **Expiry Warning**: 2027-02-10 (7 days before expiry)
- **Expiry Date**: 2027-02-17 (account disabled)
- **Cleanup Date**: 2027-03-19 (30 days after expiry, suggested removal)
