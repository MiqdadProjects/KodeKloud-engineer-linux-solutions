# 🔐 Task 20 - Hardening Apache Web Server Response Headers on stapp01

**📌 Task Description**

The security team at **xFusionCorp Industries** is undergoing a hardening process for the **Apache (httpd)** web servers across all application tiers. One of the primary security layers being implemented involves the configuration of specific **HTTP response headers**. These headers are designed to protect end-users from various web-based attacks such as Cross-Site Scripting (XSS), Clickjacking, and MIME-type sniffing. This task involves installing Apache on **App Server 1 (stapp01)**, pivoting it to a non-standard port (**5004**), and injecting mandatory security headers into the transport layer.

**👉 Task Requirements**:
- **Target Server**: App Server 1 (stapp01)
- **Service**: Apache (`httpd`)
- **Port**: `5004` (Custom Listen Port)
- **Document Root**: `/var/www/html/`
- **Mandatory Headers**:
  - `X-XSS-Protection`: `1; mode=block`
  - `X-Frame-Options`: `SAMEORIGIN`
  - `X-Content-Type-Options`: `nosniff`
- **Operation Objectives**:
  - Install `httpd` package
  - Modify `httpd.conf` to change the default listening port
  - Create a custom `index.html` file
  - Enable `mod_headers` and configure security policies
  - Verify headers using `curl` locally
- Ensure the service is enabled for boot persistence
- Validate that the LBR remains unaffected for this test case

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to App Server 1

```bash
ssh tony@stapp01
```

**Purpose**: Establish an administrative session to App Server 1.

**Expected Output**:
```
tony@stapp01's password: 
Welcome to App Server 1 - Stratos Datacenter
[tony@stapp01 ~]$
```

**Success Indicators**:
- ✅ Successfully authenticated as user `tony`
- ✅ Switched to root using `sudo -i`

---

## 🔹 Step 2: Install Apache Web Server

```bash
sudo yum install -y httpd
```

**Purpose**: Install the standard Apache HTTP server package.

**Success Indicators**:
- ✅ Package installed without errors.
- ✅ Configuration files available in `/etc/httpd/`.

---

## 🔹 Step 3: Configure Custom Listening Port

```bash
sudo vi /etc/httpd/conf/httpd.conf
```

**Purpose**: Change Apache from the default port 80 to the required testing port 5004.

**Workflow**:
1. Search for the string `Listen 80`.
2. Modify it to:
```apache
Listen 5004
```

**Success Indicators**:
- ✅ `httpd.conf` updated with `Listen 5004`.

---

## 🔹 Step 4: Create Landing Page Content

```bash
echo "Welcome to the xFusionCorp Industries!" | sudo tee /var/www/html/index.html
```

**Purpose**: Provision the default index file with the required branding message.

**Verification**:
```bash
cat /var/www/html/index.html
```

---

## 🔹 Step 5: Configure Security Response Headers

```bash
sudo vi /etc/httpd/conf/httpd.conf
```

**Purpose**: Inject security headers into all HTTP responses.

**Configuration Block**:
Append the following block to the end of `httpd.conf`:

```apache
<IfModule mod_headers.c>
    Header always set X-XSS-Protection "1; mode=block"
    Header always set X-Frame-Options "SAMEORIGIN"
    Header always set X-Content-Type-Options "nosniff"
</IfModule>
```

**Parameter Breakdown**:
- `X-XSS-Protection`: Prevents page loading if an XSS attack is detected.
- `X-Frame-Options`: Prevents the site from being rendered in an iFrame (Ant-Clickjacking).
- `X-Content-Type-Options`: Forces the browser to strictly follow the Content-Type header (Anti-Sniffing).

**Success Indicators**:
- ✅ `mod_headers` logic implemented.
- ✅ No syntax errors in configuration.

---

## 🔹 Step 6: Activate and Persist Service

```bash
sudo systemctl enable httpd
sudo systemctl start httpd
```

**Purpose**: Apply the changes and ensure the hardened server starts on boot.

---

## 🔹 Step 7: Final Verification and Header Audit

```bash
curl -I http://localhost:5004
```

**Purpose**: Use a HEAD request to inspect the response headers and verify the hardening policies are active.

**Expected Output**:
```http
HTTP/1.1 200 OK
Date: ...
Server: Apache/2.4.37 (centos)
X-XSS-Protection: 1; mode=block
X-Frame-Options: SAMEORIGIN
X-Content-Type-Options: nosniff
Last-Modified: ...
ETag: ...
Accept-Ranges: bytes
Content-Length: 39
Content-Type: text/html; charset=UTF-8
```

**Success Indicators**:
- ✅ HTTP Status 200 OK.
- ✅ All three hardening headers present in the response.
- ✅ Resource reachable on port 5004.

---

## 📋 Quick Command Reference

Use these commands for rapid Apache hardening on **stapp01**:

```bash
# Prep
sudo yum install -y httpd

# Config Overrides
sudo sed -i 's/Listen 80/Listen 5004/g' /etc/httpd/conf/httpd.conf

# Header Injection
cat <<EOF | sudo tee -a /etc/httpd/conf/httpd.conf
<IfModule mod_headers.c>
    Header always set X-XSS-Protection "1; mode=block"
    Header always set X-Frame-Options "SAMEORIGIN"
    Header always set X-Content-Type-Options "nosniff"
</IfModule>
EOF

# Rollout
sudo systemctl enable --now httpd
curl -I localhost:5004
```

---

## 💡 Common Hardening Issues & Fixes

### **Issue 1: mod_headers Not Loaded**
**Problem**: The custom headers don't appear in the `curl` output.
**Fix**: Ensure the module is loaded. It is usually enabled by default, but verify:
```bash
httpd -M | grep headers
```

### **Issue 2: SELinux Blocking Port 5004**
**Problem**: Apache fails to start with "Permission Denied" for the custom port.
**Fix**: Add the port to the allowed HTTP service ports.
```bash
sudo semanage port -a -t http_port_t -p tcp 5004
```

### **Issue 3: Firewall Blocking Inbound**
**Problem**: Port 5004 is unreachable from external testing pods.
**Fix**: Open the port in `firewalld`.
```bash
sudo firewall-cmd --permanent --add-port=5004/tcp
sudo firewall-cmd --reload
```

---

## 🔧 Troubleshooting Failures

### **Error: Job for httpd.service failed...**
**Symptoms**: Apache won't restart after config changes.
**Solution**: Check for syntax errors.
```bash
sudo apachectl configtest
```

### **Error: 403 Forbidden**
**Symptoms**: `curl` returns 403.
**Solution**: Check permissions on `/var/www/html/index.html`. It should be readable by the `apache` user.

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The use of a non-standard port (**5004**) is the most likely source of friction, particularly with SELinux (if enforced) or existing firewall rules that only anticipate port 80 or 443.

**💡 Solution Approach**:
1. **Dynamic Patching**: Used `sed` to replace the port string specifically to avoid corrupting other parts of the main config.
2. **Conditional Header Injection**: Wrapped the headers in an `<IfModule>` block to ensure that if the module is ever disabled, the server doesn't crash on startup.
3. **Local Audit**: Used `curl -I` specifically on `localhost` to isolate server-side configuration from network/LBR issues.

---

## ⚠️ Important Production Notes

🔧 **Persistence**: Ensure `Header always set` is used to ensure headers are sent even on error pages (404, 500).
🔐 **Security**: Consider adding `Content-Security-Policy` (CSP) as a logical next step in hardening.
📊 **Version Masking**: Add `ServerTokens Prod` and `ServerSignature Off` to further harden the server.
🛡️ **Audit**: Regularly check the site with security scanners like `OWASP ZAP` to verify headers.

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Successfully installed **Apache** on `stapp01`.
- Configured service to listen on custom port **5004**.
- Deployed a custom landing page in the document root.
- Implemented **X-XSS-Protection**, **X-Frame-Options**, and **X-Content-Type-Options** headers.
- Verified hardening via iterative `curl` auditing.

**🔍 Verification Completed**:
- **stapp01**: HTTP status **200 OK** | Headers confirmed | Port 5004 active.
- **Persistence**: Service enabled for automatic start.

**🔒 Configuration Summary**:
- **Port**: 5004
- **Headers**: 3 Security Hardening Headers
- **Validation**: curl -I localhost:5004
- **Scope**: stapp01 ONLY
