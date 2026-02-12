# 🔐 Task 21 - Troubleshoot Apache Service on All App Servers

**📌 Task Description**

The monitoring systems at **xFusionCorp Industries** have identified that the **Apache (httpd)** service is not operational on several **Nautilus Application Servers** in the **Stratos Datacenter**. This failure is impacting the availability of web applications across the infrastructure. This task involves identifying the specific servers with faulty configurations, resolving syntax and operational errors, and ensuring that Apache is consistently running on the required custom port (**5004**) with the correct document root across the entire application tier.

**👉 Task Requirements**:
- **Target Servers**: All App Servers (stapp01, stapp02, stapp03) in Stratos Datacenter
- **Service**: Apache (`httpd`)
- **Port**: `5004` (Mandatory for all servers)
- **Document Root**: `/var/www/html`
- **Operation Objectives**:
  - Audit the status of Apache across all application servers
  - Identify and fix configuration errors (syntax, ports, paths)
  - Ensure services are enabled and started
  - Verify connectivity from the jump host via `curl`
- Do not disable any running firewalls
- Maintain standard document root layout
- Ensure consistency in configuration across all three nodes

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface. Please note that the root cause of service failures may vary; the following solution addresses specific syntax and operational blocks encountered during this audit.

---

## 🔹 Step 1: Preliminary Audit from Jump Host

```bash
# Test connectivity to all app servers on port 5004
curl http://172.16.238.10:5004/
curl http://172.16.238.11:5004/
curl http://172.16.238.12:5004/
```

**Purpose**: Quickly identify which servers are not responding to web requests.

**Expected Result** (initial):
- ❌ **stapp01/stapp02/stapp03**: `curl: (7) Failed to connect` or `Connection refused`.

---

## 🔹 Step 2: Investigation - stapp01 and stapp03

Connect to each server and check the service status.

```bash
# Execute on stapp01 and stapp03
sudo systemctl status httpd
```

**Findings**:
The service is often found in an **inactive (dead)** state and is **disabled**, meaning it does not start on its own.

**Fix**:
```bash
sudo systemctl enable httpd
sudo systemctl start httpd
```

---

## 🔹 Step 3: Investigation - stapp02 Configuration Failure

Connect to **stapp02** where simple service starts may fail.

```bash
sudo systemctl status httpd
```

**Symptoms**:
The service fails to start with a generic error. Checking logs reveals:
`httpd: Syntax error on line 34 of /etc/httpd/conf/httpd.conf`

**Fix Phase 1 - Port Error**:
1. Edit the configuration: `sudo vi /etc/httpd/conf/httpd.conf`
2. Search for the `Listen` directive.
3. Correct any typographical errors or mismatched port values to ensure it reads:
   ```apache
   Listen 5004
   ```

**Fix Phase 2 - Document Root Path**:
Checking the syntax again (`httpd -t`) reveals a secondary issue:
`DocumentRoot '/var/www/html;' is not a directory`

1. Re-edit: `sudo vi /etc/httpd/conf/httpd.conf`
2. Locate the `DocumentRoot` directive.
3. Remove any trailing semicolons or invalid characters to ensure it reads:
   ```apache
   DocumentRoot "/var/www/html"
   ```

---

## 🔹 Step 4: Verification - stapp02 Syntax

```bash
sudo httpd -t
```

**Expected Output**:
```
Syntax OK
```

**Final Service Activation**:
```bash
sudo systemctl enable httpd
sudo systemctl restart httpd
```

---

## 🔹 Step 5: Final Validation from Jump Host

Perform a final sweep using `curl` to ensure all servers are correctly serving content on port 5004.

```bash
# Execute from Jump Host
curl http://172.16.238.10:5004/
curl http://172.16.238.11:5004/
curl http://172.16.238.12:5004/
```

**Expected Output**:
```
Welcome to xFusionCorp Industries!
Welcome to xFusionCorp Industries!
Welcome to xFusionCorp Industries!
```

**Success Indicators**:
- ✅ All three servers return the branding message.
- ✅ No connection refused errors.

---

## 📋 Quick Command Reference

Audit and Repair sequence for App Servers:

```bash
# 1. Status Check
sudo systemctl status httpd

# 2. Config Validation
sudo httpd -t

# 3. Path/Port correction (if needed)
# sudo vi /etc/httpd/conf/httpd.conf

# 4. Service Rollout
sudo systemctl enable --now httpd

# 5. Local Port Test
sudo ss -tulpn | grep :5004
```

---

## 💡 Common Configuration Issues in Task 21

### **Issue 1: Semicolon in Config**
**Problem**: Using C-style syntax (`;`) at the end of lines in `httpd.conf`.
**Fix**: Apache configuration is directive-based and does not use semicolons for line termination. Remove them.

### **Issue 2: Listen Directive Missing**
**Problem**: Apache is trying to bind to port 80 while the app requires 5004.
**Fix**: Ensure `Listen 5004` is the *only* Listen directive or the primary one.

### **Issue 3: Service Persistence**
**Problem**: Service works after manual start but fails after reboot.
**Fix**: Always run `systemctl enable httpd` during the troubleshooting process.

---

## 🔧 Troubleshooting Failures

### **Error: Address already in use: AH00072: make_sock: could not bind to address 0.0.0.0:5004**
**Symptoms**: Apache fails to start.
**Solution**: Another process is already using port 5004 or there is a duplicate `Listen` entry in the config. Check via `ss -tulpn`.

### **Error: DocumentRoot does not exist**
**Symptoms**: `httpd -t` fails.
**Solution**: Verify the directory exists on the filesystem (`ls -d /var/www/html`) and check for typos in the path string inside `httpd.conf`.

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The challenge in Task 21 is "Configuration Drift" where different servers in the same tier have unique errors. While stapp01 and stapp03 just had service state issues, stapp02 had multi-level syntax errors (wrong port format and invalid path characters).

**💡 Solution Approach**:
1. **Iterative Debugging**: Used `httpd -t` repeatedly. It only reports the *first* error it finds, so multiple runs are needed to clear layered configuration mistakes.
2. **Service Orchestration**: Ensured that the `enable` status was part of the fix to satisfy the monitoring tool's requirement for ongoing service availability.
3. **External Verification**: Used the Jump Host as the definitive source of truth, bypassing local cache or loopback configurations.

---

## ⚠️ Important Production Notes

🔧 **Config Management**: Use tools like Ansible to ensure `httpd.conf` stays synchronized across the application tier.
🔐 **Security**: Ensure that custom ports are registered in `semanage` if SELinux is in Enforcing mode.
📊 **Logging**: Review `/var/log/httpd/error_log` if service starts are failing without clear shell errors.
🛡️ **Firewall**: Port 5004 must be added to the `public` zone in `firewalld`.

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Identified and fixed service state issues on **stapp01** and **stapp03**.
- Resolved syntax errors regarding **Listen** port and **DocumentRoot** path on **stapp02**.
- Standardized Apache to run on port **5004** across all application servers.
- Verified persistent service operation and accessibility from the Jump Host.

**🔍 Verification Completed**:
- **App Tier Audit**: All three servers active and listening on port 5004.
- **Connectivity**: curl tests confirmed 3/3 successful responses.

**🔒 Configuration Summary**:
- **Port Compliance**: 5004
- **Path Compliance**: /var/www/html
- **Service Status**: Active/Enabled on all nodes
