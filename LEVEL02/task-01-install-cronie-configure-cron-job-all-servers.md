# 🔐 Task 1 - Install Cronie and Configure Cron Job on All App Servers

**📌 Task Description**

The **Nautilus system admins team** has prepared scripts to automate several day-to-day tasks. They want them to be deployed on all app servers in Stratos DC on a set schedule. Before that, they need to test similar functionality with a sample cron job. This task involves installing the `cronie` package, ensuring the `crond` service is running, and configuring a scheduled task for the root user.

**👉 Task Requirements**:
- **Target Servers**: All App Servers (stapp01, stapp02, stapp03) in Stratos Datacenter
- **Package**: `cronie` (Cron daemon)
- **Service**: `crond` (Start and enable)
- **Cron Job**: `*/5 * * * * echo hello > /tmp/cron_text`
- **User**: `root`
- **Operation Objectives**:
  - Install `cronie` package on all servers
  - Start and enable `crond` service
  - Configure cron job to run every 5 minutes
  - Verify cron job creation and execution
- Ensure automation infrastructure is ready
- Verify scheduled task execution

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to App Server 1

```bash
ssh tony@stapp01
```

**Purpose**: Establish SSH connection to App Server 1 for cron configuration.

**Expected Output**:
```
tony@stapp01's password: 
Welcome to App Server 1 - Stratos Datacenter
[tony@stapp01 ~]$
```

**Success Indicators**:
- ✅ Successfully authenticated as user `tony`
- ✅ Command prompt shows `stapp01` hostname
- ✅ Ready to execute administrative commands

---

## 🔹 Step 2: Install Cronie Package on App Server 1

```bash
sudo yum install -y cronie
```

**Purpose**: Install the cron daemon package required for scheduling tasks.

**Command Breakdown**:
- `sudo`: Execute with administrative privileges
- `yum`: Package manager (RHEL/CentOS)
- `install -y`: Install package with automatic yes to prompts
- `cronie`: The standard cron daemon package

**Expected Output** (sample):
```
Installed:
  cronie-1.5.2-4.el8.x86_64
  cronie-anacron-1.5.2-4.el8.x86_64
Complete!
```

**Success Indicators**:
- ✅ Package installed successfully
- ✅ No error messages
- ✅ `cronie` and dependencies installed

---

## 🔹 Step 3: Start and Enable Crond Service on App Server 1

```bash
sudo systemctl start crond
sudo systemctl enable crond
```

**Purpose**: Start the cron service immediately and ensure it starts automatically on boot.

**Expected Output**:
```
Created symlink /etc/systemd/system/multi-user.target.wants/crond.service → /usr/lib/systemd/system/crond.service.
```

**Success Indicators**:
- ✅ Service started
- ✅ Symlink created (enabled)
- ✅ Service ready to process cron jobs

---

## 🔹 Step 4: Verify Crond Status on App Server 1

```bash
sudo systemctl status crond
```

**Purpose**: Confirm the cron service is active and running.

**Expected Output**:
```
● crond.service - Command Scheduler
   Loaded: loaded (/usr/lib/systemd/system/crond.service; enabled; vendor preset: enabled)
   Active: active (running) since Mon 2026-02-09 17:00:00 EST; 1min ago
```

**Success Indicators**:
- ✅ Status is `active (running)`
- ✅ Service is `enabled`

---

## 🔹 Step 5: Add Cron Job for Root User on App Server 1

```bash
sudo crontab -e
```

**Purpose**: Edit the crontab file for the root user to add the scheduled task.

**Add the following line**:
```
*/5 * * * * echo hello > /tmp/cron_text
```

**Cron Expression Breakdown**:
- `*/5`: Every 5 minutes
- `*`: Every hour
- `*`: Every day of month
- `*`: Every month
- `*`: Every day of week
- `echo hello > /tmp/cron_text`: Command to execute

**Vi Editor Commands**:
1. Press `i` to enter insert mode
2. Paste the cron job line
3. Press `Esc` to exit insert mode
4. Type `:wq` and press `Enter` to save and exit

**Alternative (Non-interactive)**:
```bash
(sudo crontab -l 2>/dev/null; echo "*/5 * * * * echo hello > /tmp/cron_text") | sudo crontab -
```

**Success Indicators**:
- ✅ `crontab: installing new crontab` message appears
- ✅ Configuration saved successfully

---

## 🔹 Step 6: Verify Cron Job Creation on App Server 1

```bash
sudo crontab -l
```

**Purpose**: List the current cron jobs for the root user to verify the addition.

**Expected Output**:
```
*/5 * * * * echo hello > /tmp/cron_text
```

**Success Indicators**:
- ✅ Cron job listed correctly
- ✅ Verification complete for App Server 1

---

## 🔹 Step 7: Connect to App Server 2

```bash
ssh steve@stapp02
```

**Purpose**: Establish SSH connection to App Server 2.

**Expected Output**:
```
steve@stapp02's password: 
Welcome to App Server 2 - Stratos Datacenter
[steve@stapp02 ~]$
```

---

## 🔹 Step 8: Install Cronie and Configure Service on App Server 2

```bash
sudo yum install -y cronie
sudo systemctl start crond
sudo systemctl enable crond
sudo systemctl status crond
```

**Purpose**: Install package and start service on App Server 2.

**Success Indicators**:
- ✅ Package installed
- ✅ Service active and running

---

## 🔹 Step 9: Add Cron Job on App Server 2

```bash
sudo crontab -e
# Add: */5 * * * * echo hello > /tmp/cron_text
```

**Or non-interactive**:
```bash
(sudo crontab -l 2>/dev/null; echo "*/5 * * * * echo hello > /tmp/cron_text") | sudo crontab -
```

**Verify**:
```bash
sudo crontab -l
```

**Success Indicators**:
- ✅ Cron job added and listed

---

## 🔹 Step 10: Connect to App Server 3

```bash
ssh banner@stapp03
```

**Purpose**: Establish SSH connection to App Server 3.

**Expected Output**:
```
banner@stapp03's password: 
Welcome to App Server 3 - Stratos Datacenter
[banner@stapp03 ~]$
```

---

## 🔹 Step 11: Install Cronie and Configure Service on App Server 3

```bash
sudo yum install -y cronie
sudo systemctl start crond
sudo systemctl enable crond
sudo systemctl status crond
```

**Purpose**: Install package and start service on App Server 3.

**Success Indicators**:
- ✅ Package installed
- ✅ Service active and running

---

## 🔹 Step 12: Add Cron Job on App Server 3

```bash
sudo crontab -e
# Add: */5 * * * * echo hello > /tmp/cron_text
```

**Or non-interactive**:
```bash
(sudo crontab -l 2>/dev/null; echo "*/5 * * * * echo hello > /tmp/cron_text") | sudo crontab -
```

**Verify**:
```bash
sudo crontab -l
```

**Success Indicators**:
- ✅ Cron job added and listed
- ✅ All servers configured

---

## 🔹 Step 13: Verify Cron Execution (After 5 Minutes)

**On any server**:
```bash
ls -l /tmp/cron_text
cat /tmp/cron_text
```

**Purpose**: Confirm the cron job ran successfully and created the output file.

**Expected Output**:
```
-rw-r--r-- 1 root root 6 Feb 09 17:05 /tmp/cron_text
hello
```

**Success Indicators**:
- ✅ File exists
- ✅ Content is "hello"
- ✅ Timestamp matches execution time
- ✅ Automation verified

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **each App Server**:

```bash
# App Server 1
ssh tony@stapp01

# Install and configure
sudo yum install -y cronie
sudo systemctl start crond
sudo systemctl enable crond
sudo systemctl status crond

# Add cron job
(sudo crontab -l 2>/dev/null; echo "*/5 * * * * echo hello > /tmp/cron_text") | sudo crontab -

# Verify
sudo crontab -l

# Exit
exit

# App Server 2
ssh steve@stapp02

# Install and configure
sudo yum install -y cronie
sudo systemctl start crond
sudo systemctl enable crond
sudo systemctl status crond

# Add cron job
(sudo crontab -l 2>/dev/null; echo "*/5 * * * * echo hello > /tmp/cron_text") | sudo crontab -

# Verify
sudo crontab -l

# Exit
exit

# App Server 3
ssh banner@stapp03

# Install and configure
sudo yum install -y cronie
sudo systemctl start crond
sudo systemctl enable crond
sudo systemctl status crond

# Add cron job
(sudo crontab -l 2>/dev/null; echo "*/5 * * * * echo hello > /tmp/cron_text") | sudo crontab -

# Verify
sudo crontab -l

# Exit
exit
```

---

## 💡 Common Cron Configuration Issues & Fixes

### **Issue 1: Service Not Running**
**Problem**: Cron jobs not executing.
**Fix**: Ensure crond service is active.
```bash
# Check status
sudo systemctl status crond

# Start if needed
sudo systemctl start crond
```

### **Issue 2: Invalid Syntax**
**Problem**: Crontab rejects the entry.
**Fix**: Verify cron expression format (5 fields + command).
```bash
# Correct format: min hour dom mon dow command
*/5 * * * * echo hello > /tmp/cron_text
```

### **Issue 3: Permissions**
**Problem**: Cron job cannot write to file.
**Fix**: Ensure destination directory is writable (tmp is usually fine).
```bash
# Check permissions
ls -ld /tmp

# Check cron logs for errors
sudo grep CRON /var/log/cron
```

### **Issue 4: PATH Issues**
**Problem**: Command not found in cron.
**Fix**: Use full path for commands or set PATH in crontab.
```bash
# Use full path
*/5 * * * * /bin/echo hello > /tmp/cron_text
```

---

## 🔧 Troubleshooting Failures

### **Error: crontab command not found**
**Symptoms**: `bash: crontab: command not found`.
**Solution**: Install cronie package.
```bash
sudo yum install -y cronie
```

### **Error: Output file not created**
**Symptoms**: `/tmp/cron_text` not found after 5 minutes.
**Solution**: Check syslog/cron logs and service status.
```bash
# Check logs
sudo tail -f /var/log/cron

# Verify job exists
sudo crontab -l
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The challenge involves ensuring consistent setup across multiple servers and correctly configuring a cron job to run at a specific interval. Installing the package first is a prerequisite often missed.

**💡 Solution Approach**:
1. **Prerequisite Check**: Installed `cronie` package first on each server.
2. **Service Management**: Started and enabled `crond` to ensure it runs.
3. **Cron Configuration**: Added the exact cron expression `*/5 * * * *`.
4. **Verification**: Checked service status and crontab listing.
5. **Multi-Server**: Repeated steps consistently across stapp01, stapp02, and stapp03.

**🎯 Key Success Factors**:
- **Package Installation**: Essential first step.
- **Service Activation**: Required for cron to actually run.
- **Expression Accuracy**: `*/5` correctly specifies "every 5 minutes".
- **Privilege Usage**: Used `sudo` for root crontab modification.

**⚠️ Critical Fixes**:
- Ensure `cronie` is installed before trying to start `crond`.
- Use `sudo crontab -e` to edit root's crontab (not user's).
- Verify the file creation path `/tmp/cron_text`.

---

## ⚠️ Important Production Notes

🔧 **Cron Logs**: Monitor `/var/log/cron` for execution history.
🔐 **Security**: Be cautious with root cron jobs; use least privilege where possible.
📊 **Scheduling**: Avoid piling up too many jobs at the same minute; stagger them if needed.
🛡️ **Environment**: Cron run environment differs from interactive shell (PATH, env variables).

**Cron Expression Quick Reference**:
```
* * * * * command to execute
| | | | |
| | | | ----- Day of week (0 - 7) (Sunday=0 or 7)
| | | ------- Month (1 - 12)
| | --------- Day of month (1 - 31)
| ----------- Hour (0 - 23)
------------- Minute (0 - 59)
```

**Common Examples**:
```bash
0 0 * * *     # Daily at midnight
*/15 * * * *  # Every 15 minutes
0 9-17 * * *  # Hourly between 9am and 5pm
```

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Installed `cronie` package on all three app servers
- Started and enabled `crond` service
- Configured cron job `*/5 * * * * echo hello > /tmp/cron_text` for root
- Verified configuration on all servers

**🔍 Verification Completed**:
- **App Server 1**: Package installed, Service running, Cron job added
- **App Server 2**: Package installed, Service running, Cron job added
- **App Server 3**: Package installed, Service running, Cron job added

**🚀 Next Steps** (Optional):
- Use `watch` command to monitor multiple servers if needed
- Set up monitoring for cron job failures
- Clean up test file `/tmp/cron_text` after validation

**📊 Configuration Summary**:
- **Servers**: stapp01, stapp02, stapp03
- **Package**: cronie
- **Service**: crond (enabled)
- **Job**: `*/5 * * * * echo hello > /tmp/cron_text`
- **User**: root

**🔍 Key Learning Points**:
- `cronie` package provides the `crond` service in RHEL/CentOS
- `systemctl` manages the service state
- `crontab -e` is the standard way to edit cron jobs
- `*/n` syntax dictates interval execution
