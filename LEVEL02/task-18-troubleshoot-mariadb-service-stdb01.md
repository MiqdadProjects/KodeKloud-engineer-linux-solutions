# 🔐 Task 18 - Troubleshoot MariaDB Service Failure on stdb01

**📌 Task Description**

The production support team at **xFusionCorp Industries** has reported a critical connectivity issue between the application tier and the database tier in the **Stratos Datacenter**. Initial investigations have confirmed that the **MariaDB** service is down on the **database server (stdb01)**, preventing any data persistence for the Nautilus application. This task requires a comprehensive forensic analysis of the service failure, recreation of missing data structures, and restoration of the database service to functional status.

**👉 Task Requirements**:
- **Target Server**: Database Server (`stdb01`)
- **Service**: `mariadb`
- **Data Directory**: `/var/lib/mysql`
- **Operation Objectives**:
  - Investigate MariaDB service status and health
  - Identify the primary cause of startup failure via logs
  - Reconstruct the missing MySQL data directory structure
  - Initialize the database system tables
  - Restore and verify database service availability
- Ensure proper ownership and permissions for the data directory
- Enable service persistence across reboots
- Validate the fix via systemctl

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface. Please note that the root cause of the service failure may vary depending on your specific task; the following solution addresses a common configuration typo encountered during this troubleshooting session.
---

## 🔹 Step 1: Connect to Database Server

```bash
ssh peter@stdb01
```

**Purpose**: Establish an administrative SSH session to the affected database server.

**Expected Output**:
```
peter@stdb01's password: 
Welcome to Database Server - Stratos Datacenter
[peter@stdb01 ~]$
```

**Success Indicators**:
- ✅ Successfully authenticated as user `peter`
- ✅ Switched to root using `sudo su -`

---

## 🔹 Step 2: Investigation - Check MariaDB Status

```bash
systemctl status mariadb
```

**Purpose**: Confirm the current state of the database engine.

**Expected Output** (initial):
```
● mariadb.service - MariaDB 10.3 database server
   Loaded: loaded (/usr/lib/systemd/system/mariadb.service; enabled; vendor preset: disabled)
   Active: failed (Result: exit-code) since Wed 2026-02-11 19:35:00 EST; 3min ago
```

**Success Indicators**:
- ✅ Service failure confirmed (status `failed`)

---

## 🔹 Step 3: Investigation - Analyze Crash Logs

```bash
journalctl -u mariadb -n 50 --no-pager
```

**Purpose**: Review the most recent logs to understand why MariaDB is refusing to start.

**Critical Findings**:
The logs indicate that the service cannot find its data directory or initialize its PID file because the base path is missing.

**Success Indicators**:
- ✅ Error message `Can't create test file /var/lib/mysql/stdb01.lower-test` identified
- ✅ Root cause: Path `/var/lib/mysql` does not exist

---

## 🔹 Step 4: Investigation - Verify Directory Existence

```bash
ls -ld /var/lib/mysql
```

**Purpose**: Physically verify the directory structure on the filesystem.

**Expected Output**:
```
ls: cannot access '/var/lib/mysql': No such file or directory
```

**Success Indicators**:
- ✅ Confirmed that the critical data directory `/var/lib/mysql` is missing

---

## 🔹 Step 5: Fix - Reconstruct MariaDB Data Directory

```bash
mkdir -p /var/lib/mysql
chown -R mysql:mysql /var/lib/mysql
chmod 755 /var/lib/mysql
```

**Purpose**: Create the necessary directory structure and assign the correct ownership and permissions for the `mysql` system user.

**Command Breakdown**:
- `mkdir -p`: Creates the directory path.
- `chown mysql:mysql`: Sets the service account as the owner.
- `chmod 755`: Grants read/execute access to others while allowing the owner full write access.

**Success Indicators**:
- ✅ Directory created
- ✅ Ownership verified via `ls -ld`

---

## 🔹 Step 6: Initialize MariaDB Database

```bash
mariadb-install-db --user=mysql --datadir=/var/lib/mysql
```

**Purpose**: Initialize the MariaDB data directory and create the system tables (the `mysql` database) required for the engine to start correctly.

**Success Indicators**:
- ✅ Initialization process completed successfully
- ✅ Internal database files (ibdata, etc.) generated in the directory

---

## 🔹 Step 7: Service Restoration

```bash
systemctl start mariadb
systemctl enable mariadb
```

**Purpose**: Start the database engine and configure it to initiate automatically on future system boots.

**Success Indicators**:
- ✅ Command executed without errors
- ✅ Service enters the starting phase

---

## 🔹 Step 8: Final Verification

```bash
systemctl status mariadb
```

**Expected Output**:
```
● mariadb.service - MariaDB 10.3 database server
   Loaded: loaded (/usr/lib/systemd/system/mariadb.service; enabled; vendor preset: disabled)
   Active: active (running) since Wed 2026-02-11 19:40:00 EST; 5s ago
```

**Success Indicators**:
- ✅ Service status is `active (running)`
- ✅ Database is ready to receive connections from the application tier

---

## 📋 Quick Command Reference

Use these commands to restore a broken MariaDB data directory:

```bash
# Verify directory
ls -d /var/lib/mysql || echo "Directory Missing"

# Restoration Sequence
sudo mkdir -p /var/lib/mysql
sudo chown mysql:mysql /var/lib/mysql
sudo chmod 755 /var/lib/mysql
sudo mariadb-install-db --user=mysql --datadir=/var/lib/mysql

# Service Control
sudo systemctl enable --now mariadb
systemctl is-active mariadb
```

---

## 💡 Common MariaDB Startup Issues & Fixes

### **Issue 1: Port 3306 Conflict**
**Problem**: Another instance or service is using the MySQL port.
**Fix**: Stop the competing process.
```bash
sudo ss -tulpn | grep :3306
```

### **Issue 2: SELinux Contexts**
**Problem**: SELinux prevents MariaDB from writing to a newly created directory.
**Fix**: Restore the default security context.
```bash
sudo restorecon -Rv /var/lib/mysql
```

### **Issue 3: Disk Space Exhaustion**
**Problem**: MariaDB cannot write logs or data due to a full partition.
**Fix**: Clear space in `/var`.
```bash
df -h /var
```

---

## 🔧 Troubleshooting Failures

### **Error: [ERROR] Default storage engine (InnoDB) is not available**
**Symptoms**: Service crashes immediately after start.
**Solution**: Check for corrupted `ib_logfile` entries in `/var/lib/mysql`. If initialization was clean, this shouldn't occur.

### **Error: systemctl start mariadb: Job for mariadb.service failed**
**Symptoms**: Generic systemd failure.
**Solution**: Check `journalctl -u mariadb --no-pager` for specific MySQL error codes (ER_).

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The challenge was a total loss of the data directory, which is a rare but critical failure. Simply restarting the service is impossible without the underlying system tables and directory structure. 

**💡 Solution Approach**:
1. **Structural Repair**: Manually mapped the missing path and enforced the standard permissions required by the MariaDB daemon.
2. **Re-initialization**: Used `mariadb-install-db` to rebuild the metadata layer, which is safer than attempting to copy files from another server.
3. **Privilege Alignment**: Ensured the `mysql` user had full control over the new directory, as permission denied errors are the #1 cause of DB startup failures.

---

## ⚠️ Important Production Notes

🔧 **Data Persistence**: In a real production scenario, always attempt to recover existing data from backups before initializing a fresh database.
🔐 **Security**: Run `mysql_secure_installation` after restoring the service to set root passwords and remove test users.
📊 **Monitoring**: Ensure file-integrity monitoring is in place to alert if critical system directories are deleted.
🛡️ **Backup**: Implement a regular `mysqldump` or `mariabackup` schedule.

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Diagnosed MariaDB failure on **stdb01** due to missing `/var/lib/mysql`.
- Reconstructed the data directory with correct ownership and permissions.
- Successfully initialized the database system tables.
- Restored the **MariaDB** service to an **ACTIVE** state.
- Verified service stability and accessibility.

**🔍 Verification Completed**:
- **stdb01**: `systemctl status` confirmed as **ACTIVE**.
- **Filesystem**: Data directory healthy and populated.

**🔒 Configuration Summary**:
- **Data Dir**: /var/lib/mysql
- **Owner**: mysql:mysql
- **Status**: Active/Enabled
- **Initialization Tool**: mariadb-install-db
