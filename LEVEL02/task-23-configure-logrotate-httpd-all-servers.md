# 🔐 Task 23 - Configure Apache Logrotate on All App Servers

**📌 Task Description**

The **Nautilus DevOps** team is preparing to launch a new high-traffic application across the **Stratos Datacenter** application tier. This application is expected to generate a significant volume of access and error logs within the **Apache (httpd)** service. To maintain storage efficiency and prevent disk space exhaustion, a robust log management strategy is required. This task involves installing Apache on all application servers and configuring the **logrotate** utility to compress and rotate HTTP logs on a monthly schedule, retaining a specific history of previous logs.

**👉 Task Requirements**:
- **Target Servers**: All App Servers (stapp01, stapp02, stapp03)
- **Service**: Apache (`httpd`)
- **Rotation Tool**: `logrotate`
- **Rotation Interval**: `monthly`
- **Retention Policy**: Keep `3` rotated logs
- **Log Location**: `/var/log/httpd/`
- **Operation Objectives**:
  - Install and enable Apache service on all nodes
  - Configure or update the logrotate policy for httpd
  - Enable log compression to save space
  - Validate the configuration using a dry-run
  - Force a manual rotation to confirm operational status
- Ensure that the Apache service is reloaded after rotation to prevent file handle locking
- Maintain a clean and consistent log structure across the server fleet

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Install and Activate Apache

Repeat these steps on **stapp01**, **stapp02**, and **stapp03**:

```bash
sudo yum install -y httpd
sudo systemctl enable httpd
sudo systemctl start httpd
```

**Purpose**: Ensure the web server is running and generating the log files targeted for rotation.

**Success Indicators**:
- ✅ Apache service is `active (running)`.
- ✅ Log files `access_log` and `error_log` created in `/var/log/httpd/`.

---

## 🔹 Step 2: Configure Logrotate Policy

```bash
sudo vi /etc/logrotate.d/httpd
```

**Purpose**: Define the specific rules for how the system handles Apache logs.

**Configuration Content**:
Ensure the file contains the following block. If it already exists, update the `monthly` and `rotate` parameters.

```bash
/var/log/httpd/*log {
    monthly
    rotate 3
    compress
    delaycompress
    missingok
    notifempty
    sharedscripts
    postrotate
        /bin/systemctl reload httpd.service > /dev/null 2>/dev/null || true
    endscript
}
```

**Parameter Breakdown**:
- `monthly`: Rotates logs once a month.
- `rotate 3`: Keeps only the 3 most recent rotated log files.
- `compress`: Compresses the older log files (typically to `.gz`).
- `delaycompress`: Postpones compression until the next rotation cycle (keeps the most recent rotated log uncompressed).
- `missingok`: Prevents errors if a log file is missing.
- `postrotate`: Script block to reload Apache so it starts writing to the fresh log file.

**Success Indicators**:
- ✅ Configuration file saved with correct parameters.

---

## 🔹 Step 3: Validate and Test Rotation

### **A. Dry Run (Debug Mode)**
```bash
sudo logrotate -d /etc/logrotate.d/httpd
```
*This command simulates the rotation without making actual changes to the filesystem.*

### **B. Force Manual Rotation**
```bash
sudo logrotate -f /etc/logrotate.d/httpd
```
*This forces an immediate rotation regardless of the scheduled interval.*

---

## 🔹 Step 4: Final Verification

```bash
ls -lh /var/log/httpd/
```

**Expected Output**:
```text
-rw-r--r--. 1 root root    0 Feb 12 12:00 access_log
-rw-r--r--. 1 root root  12K Feb 12 11:59 access_log.1
-rw-r--r--. 1 root root    0 Feb 12 12:00 error_log
-rw-r--r--. 1 root root  800 Feb 12 11:59 error_log.1
```

**Success Indicators**:
- ✅ New empty log files created.
- ✅ Old logs renamed with a `.1` extension.
- ✅ Apache continues to log entries to the new files.

---

## 📋 Quick Command Reference

Execute on **all app servers**:

```bash
# Force installation and service start
sudo yum install -y httpd && sudo systemctl enable --now httpd

# Apply Logrotate configuration
cat <<EOF | sudo tee /etc/logrotate.d/httpd
/var/log/httpd/*log {
    monthly
    rotate 3
    compress
    delaycompress
    missingok
    notifempty
    sharedscripts
    postrotate
        /bin/systemctl reload httpd.service > /dev/null 2>/dev/null || true
    endscript
}
EOF

# Verify
sudo logrotate -f /etc/logrotate.d/httpd
ls /var/log/httpd/
```

---

## 💡 Common Logrotate Issues & Fixes

### **Issue 1: Permission Denied**
**Problem**: The logrotate cron job fails to rotate the logs.
**Fix**: Ensure the directories and files have the correct ownership (`root:root` for the config, and `apache` or `root` for the logs depending on the setup).

### **Issue 2: Logs Not Shrinking**
**Problem**: Logs are rotated but the disk space remains full.
**Fix**: Ensure `compress` is enabled and that Apache is definitely reloaded (`postrotate`) so it releases the file handle on the old, deleted log files.

### **Issue 3: Duplicate Rotation**
**Problem**: Logs are rotating weekly *and* monthly.
**Fix**: Check if a global configuration in `/etc/logrotate.conf` is overriding the specialized service config.

---

## 🔧 Troubleshooting Failures

### **Error: error: [...] parent directory has insecure permissions**
**Symptoms**: Logrotate refuses to run.
**Solution**: The parent directory (e.g., `/etc/logrotate.d/`) must not be world-writable. Check permissions with `ls -ld`.

### **Error: Service reload failed**
**Symptoms**: Errors in the logrotate output during the `postrotate` phase.
**Solution**: Verify the path to systemctl (`/bin/systemctl`) or ensure the httpd service is named correctly (`httpd.service`).

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The default Apache installation often includes a basic logrotate configuration that might be set to `weekly`. Overwriting or modifying this existing file is critical to meeting the technical requirement of `monthly` rotation with a `3` log retention limit.

**💡 Solution Approach**:
1. **Explicit Override**: Re-creating the `/etc/logrotate.d/httpd` file with the specific `monthly` and `rotate 3` directives ensured the team's requirements were met regardless of system defaults.
2. **Reload Logic**: Included a robust `postrotate` script that reloads the service. Without this, Apache would continue writing to the *inode* of the old log file, effectively "filling" a file that had been renamed or moved, leading to phantom disk usage.
3. **Validation Suite**: Used both debug (`-d`) and force (`-f`) flags to verify the configuration before concluding the task.

---

## ⚠️ Important Production Notes

🔧 **Monitoring**: Add logrotate failures to your system monitoring (look for errors in `/var/log/messages`).
🔐 **Security**: In production, consider using the `create` directive with specific permissions (e.g., `create 0640 root adm`).
📊 **Growth Forecast**: If logs grow too large within a single month, consider adding a `size` directive (e.g., `size 100M`) to trigger an earlier rotation.
🛡️ **Shared Scripts**: Always use `sharedscripts` when rotating wildcard paths (like `*log`) to ensure the `postrotate` reload only happens once per cycle.

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Deployed **Apache (httpd)** across all application servers.
- Custom-configured **Logrotate** for HTTP logs to run **monthly**.
- Enforced a retention policy of **3** rotated logs.
- Enabled **compression** to optimize storage utility.
- Verified service continuity and rotation operation across the fleet.

**🔍 Verification Completed**:
- **stapp01/02/03**: Logrotate logic verified via debug output.
- **FS Check**: Manual rotation successful; compressed logs verified.

**🔒 Configuration Summary**:
- **Schedule**: Monthly
- **Retention**: 3
- **Compression**: Enabled
- **Reload**: systemctl reload httpd
