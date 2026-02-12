# 🔐 Task 19 - Create Automated Website Backup Script on stapp01

**📌 Task Description**

The production support team at **xFusionCorp Industries** is automating day-to-day operational tasks within the **Stratos Datacenter**. A key requirement is the implementation of an automated backup solution for the static website hosted on **App Server 1 (stapp01)**. This task involves developing a Bash script that archives web content, stores it locally for short-term rotation, and securely transfers it to the central **Backup Server (stbkp01)** without manual intervention or password prompts.

**👉 Task Requirements**:
- **Source Server**: App Server 1 (stapp01)
- **Destination Server**: Backup Server (stbkp01)
- **Source Directory**: `/var/www/html/blog`
- **Script Name**: `blog_backup.sh`
- **Script Location**: `/scripts/`
- **Local Storage**: `/backup/` (Short-term)
- **Remote Storage**: `/backup/` (Long-term)
- **User Permission**: Executable by user `tony` without `sudo` inside the script.
- **Dependency**: `zip` package must be installed on the source server.
- **Operation Objectives**:
  - Install `zip` utility manually
  - Set up password-less SSH between servers
  - Create a Bash script to archive and transfer data
  - Ensure correct filesystem permissions for scripts and backups
- Fulfill security requirements (no plain-text passwords, no sudo escalation in script)
- Validate end-to-end backup flow

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to App Server 1

```bash
ssh tony@stapp01
```

**Purpose**: Establish a session to App Server 1 to prepare the environment.

**Expected Output**:
```
tony@stapp01's password: 
Welcome to App Server 1 - Stratos Datacenter
[tony@stapp01 ~]$
```

---

## 🔹 Step 2: Install Mandatory Dependencies

```bash
sudo yum install -y zip
```

**Purpose**: Provide the archival utility required for the backup script.

**Success Indicators**:
- ✅ `zip` package installed.
- ✅ `zip --version` returns functional output.

---

## 🔹 Step 3: Prepare Directory Structure

```bash
sudo mkdir -p /scripts /backup
sudo chown -R tony:tony /scripts /backup
sudo chmod 755 /scripts /backup
```

**Purpose**: Create and secure the directories for the automation script and local backup storage, ensuring user `tony` has full access.

---

## 🔹 Step 4: Configure Password-less SSH Access

```bash
# Generate keys (if not already present)
ssh-keygen -t rsa -b 2048 -N "" -f ~/.ssh/id_rsa

# Copy key to Backup Server
ssh-copy-id clint@stbkp01
```

**Purpose**: Establish a secure, non-interactive trust relationship between the App Server and the Backup Server to allow automated file transfers.

**Verification**:
```bash
ssh clint@stbkp01 "hostname"
# Should return 'stbkp01' without asking for a password.
```

---

## 🔹 Step 5: Develop the Backup Script

```bash
vi /scripts/blog_backup.sh
```

**Script Content**:
```bash
#!/bin/bash

# Configuration Variables
SOURCE_DIR="/var/www/html/blog"
BACKUP_DIR="/backup"
ARCHIVE_NAME="xfusioncorp_blog.zip"
REMOTE_USER="clint"
REMOTE_SERVER="stbkp01"
REMOTE_DIR="/backup"

# 1. Create the zip archive locally
# -r: recursive
zip -r "$BACKUP_DIR/$ARCHIVE_NAME" "$SOURCE_DIR"

# 2. Transfer the archive to the Backup Server
# scp: secure copy
scp "$BACKUP_DIR/$ARCHIVE_NAME" "$REMOTE_USER@$REMOTE_SERVER:$REMOTE_DIR"

echo "Backup process completed successfully at $(date)"
```

**Success Indicators**:
- ✅ Script logic covers both local archiving and remote transfer.
- ✅ No hardcoded passwords or `sudo` calls.

---

## 🔹 Step 6: Set Script Execution Permissions

```bash
chmod +x /scripts/blog_backup.sh
```

**Purpose**: Make the Bash script executable for user `tony`.

---

## 🔹 Step 7: Execute and Verify Backup

```bash
# Run the script
/scripts/blog_backup.sh

# Verify local file
ls -lh /backup/xfusioncorp_blog.zip

# Verify remote file
ssh clint@stbkp01 "ls -lh /backup/xfusioncorp_blog.zip"
```

**Expected Output**:
```
adding: var/www/html/blog/ (stored 0%)
...
xfusioncorp_blog.zip      100%  ...KB   ...MB/s   00:00    
Backup process completed successfully at ...
```

**Success Indicators**:
- ✅ Archive exists in `/backup` on `stapp01`.
- ✅ Archive successfully transferred to `/backup` on `stbkp01`.

---

## 📋 Quick Command Reference

Execute these steps as user **tony** on **stapp01**:

```bash
# 1. SSH Prep (One-time)
ssh-keygen -t rsa -N "" -f ~/.ssh/id_rsa
ssh-copy-id clint@stbkp01

# 2. Script Creation
cat << 'EOF' > /scripts/blog_backup.sh
#!/bin/bash
zip -r /backup/xfusioncorp_blog.zip /var/www/html/blog
scp /backup/xfusioncorp_blog.zip clint@stbkp01:/backup/
EOF

# 3. Permissions
chmod +x /scripts/blog_backup.sh

# 4. Trigger
/scripts/blog_backup.sh
```

---

## 💡 Common Scripting Issues & Fixes

### **Issue 1: SSH Host Verification Prompt**
**Problem**: Script hangs on first run because of the "Are you sure you want to continue connecting?" prompt.
**Fix**: Run one manual `ssh clint@stbkp01` first to add the fingerprint, or use `-o StrictHostKeyChecking=no` in the `scp` command.

### **Issue 2: Permission Denied (Remote)**
**Problem**: User `clint` on `stbkp01` does not have write access to `/backup`.
**Fix**: Log into `stbkp01` and ensure the directory has correct ownership.
```bash
sudo chown clint:clint /backup
```

### **Issue 3: Filesystem Path Mismatch**
**Problem**: `SOURCE_DIR` does not exist or has incorrect casing.
**Fix**: Double check path using `ls /var/www/html/blog`.

---

## 🔧 Troubleshooting Failures

### **Error: zip command not found**
**Symptoms**: Script fails with "command not found".
**Solution**: The `zip` package must be installed via `yum` (Step 2).

### **Error: Permission denied (publickey,password)**
**Symptoms**: `scp` prompts for a password.
**Solution**: Ensure the public key of `tony` is correctly placed in `clint@stbkp01:~/.ssh/authorized_keys`. Use `ssh-copy-id` to repair.

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The absolute requirement to avoid `sudo` *inside* the script means that the script's environment must be perfectly pre-configured. If any directory (`/backup` or `/scripts`) belongs to root, the script will fail silently or with permission errors when run by `tony`.

**💡 Solution Approach**:
1. **Pre-emptive Provisioning**: Handled all elevated tasks (installing `zip`, creating root-level folders) manually using `sudo` *before* handing over control to the script.
2. **User Context**: Explicitly assigned `/scripts` and `/backup` to `tony` using `chown`.
3. **Identifier Mapping**: Used `ssh-copy-id` to ensure the security context for file transfer was handled at the system level via keys, rather than with insecure script variables.

---

## ⚠️ Important Production Notes

🔧 **Rotation**: Add a cleanup command to the script to remove archives older than X days.
🔐 **Key Security**: Protect the private key on `stapp01` with `chmod 600`.
📊 **Error Handling**: Add `set -e` to the top of the script to stop execution if any command fails.
🛡️ **Audit**: Regularly check the destination server to ensure backups are actually arriving.

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Developed and deployed the `blog_backup.sh` automation script.
- Configured password-less SSH between `stapp01` and `stbkp01`.
- Verified local and remote archival of the `/var/www/html/blog` directory.
- Ensured script execution is safe and user-appropriate (no sudo required).

**🔍 Verification Completed**:
- **Execution Test**: Script runs cleanly as user `tony`.
- **Remote Audit**: File `xfusioncorp_blog.zip` verified on Backup Server.
- **Dependency Check**: `zip` package utility confirmed.

**🔒 Configuration Summary**:
- **Script Path**: /scripts/blog_backup.sh
- **Local Storage**: /backup/
- **Remote Host**: stbkp01 (User: clint)
- **Method**: ZIP + SCP (SSH Key Auth)
