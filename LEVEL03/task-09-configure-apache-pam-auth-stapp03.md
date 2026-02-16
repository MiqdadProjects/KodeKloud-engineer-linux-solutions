# 🔐 Task 09 - Configure Apache Basic Authentication with PAM on stapp03

**📌 Task Description**

The **Nautilus** security team requires a more integrated authentication mechanism for specific web directories on **App Server 3 (stapp03)**. Instead of using standard `htpasswd` files, the requirement is to integrate **Apache Basic Authentication** with **PAM (Pluggable Authentication Modules)**. This allow users to authenticate against local Linux system accounts. This task involves installing external authentication modules, configuring the PAM pipe, and securing a specific directory using a pre-existing Linux user account.

**👉 Task Requirements**:
- **Target Server**: App Server 3 (`stapp03`)
- **Protected URL Path**: `/protected/`
- **Authentication Strategy**: Basic Auth + PAM (via External Provider)
- **Target User**: `kareem` (Password: `GyQkFRVNr3`)
- **Software Requirements**:
  - `EPEL` repository
  - `mod_authnz_external` (Apache Module)
  - `pwauth` (External Authenticator)
- **Operation Objectives**:
  - Install and enable required software components.
  - Define the external authentication provider in Apache.
  - Apply security directives to the `/var/www/html/protected` directory.
  - Verify that system-level user credentials grant web access.
- Ensure the authentication bridge works seamlessly without traditional credential files.
- Validate through remote audits via `curl`.

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to App Server 3 and Install Pre-requisites

```bash
ssh banner@stapp03
sudo su -

# Enable EPEL and install authentication modules
yum install -y epel-release
yum --enablerepo=epel -y install mod_authnz_external pwauth
```

**Purpose**: Provision the necessary modules and the `pwauth` binary, which acts as the intermediary between Apache and the system's PAM stack.

---

## 🔹 Step 2: Enable the External Auth Module

```bash
echo 'LoadModule authnz_external_module modules/mod_authnz_external.so' \
  > /etc/httpd/conf.modules.d/10-authnz_external.conf
```

**Purpose**: Explicitly load the extension module into the Apache runtime environment.

---

## 🔹 Step 3: Configure Authentication Provider and Directory Security

```bash
vi /etc/httpd/conf.d/authnz_external.conf
```

**Purpose**: Define the `pwauth` pipe and apply it to the target directory.

**Configuration Content**:
```apache
<IfModule mod_authnz_external.c>
    # Define the external provider as a pipe to the pwauth binary
    DefineExternalAuth pwauth pipe /usr/bin/pwauth
</IfModule>

<Directory /var/www/html/protected>
    AuthType Basic
    AuthName "PAM Authentication"
    
    # Use the external provider instead of a file
    AuthBasicProvider external
    AuthExternal pwauth
    
    require valid-user
</Directory>
```

**Success Indicators**:
- ✅ `pwauth` defined as an external authentication provider.
- ✅ `/var/www/html/protected` directory locked down.

---

## 🔹 Step 4: Prepare Content and Apply Changes

```bash
# Create the directory
mkdir -p /var/www/html/protected

# Restart the service
systemctl restart httpd
systemctl enable --now httpd
```

---

## 🔹 Step 5: Final Verification

```bash
# Test with user 'kareem' from Jump Host
curl -u kareem:GyQkFRVNr3 http://stapp03.stratos.xfusioncorp.com:8080/protected/
```

**Expected Output**:
```text
This is KodeKloud Protected Directory
```

**Success Indicators**:
- ✅ System user `kareem` successfully authenticates against the web directory.
- ✅ Correct content served upon successful login.
- ✅ No `htpasswd` file required for access.

---

## 📋 Quick Command Reference

Setup for **stapp03**:

```bash
# Dependencies
sudo yum install -y epel-release 
sudo yum install -y mod_authnz_external pwauth

# Load Module
echo 'LoadModule authnz_external_module modules/mod_authnz_external.so' | sudo tee /etc/httpd/conf.modules.d/10-authnz_external.conf

# Global Configuration
cat <<EOF | sudo tee /etc/httpd/conf.d/authnz_external.conf
DefineExternalAuth pwauth pipe /usr/bin/pwauth
<Directory /var/www/html/protected>
    AuthType Basic
    AuthName "PAM Authentication"
    AuthBasicProvider external
    AuthExternal pwauth
    require valid-user
</Directory>
EOF

# Rollout
sudo systemctl restart httpd
```

---

## 💡 Common PAM Auth Issues & Fixes

### **Issue 1: Internal Server Error (500)**
**Problem**: The `pwauth` binary might have incorrect permissions or cannot access the PAM shadow files.
**Fix**: `pwauth` typically requires SUID or specific permissions to read authentication data. Check `/var/log/httpd/error_log` for "exec failed" or permission errors.

### **Issue 2: Module Not Loading**
**Problem**: Apache fails to start because it can't find `mod_authnz_external`.
**Fix**: Verify the module exists in `/usr/lib64/httpd/modules/`.

### **Issue 3: Permission Denied for 'kareem'**
**Problem**: The user exists on the system but cannot authenticate via web.
**Fix**: Ensure the user has a valid shell and is not locked at the PAM level. Verify system logs at `/var/log/secure`.

---

## 🔧 Troubleshooting Failures

### **Error: configuration error: could not check user: /protected/**
**Symptoms**: HTTP 500 error.
**Solution**: Usually indicates that `pwauth` is failing to execute. Check if the path defined in `DefineExternalAuth` is correct.

### **Error: mod_authnz_external.c:980: External Authenticator 'pwauth' failed**
**Symptoms**: Credential rejection.
**Solution**: The user's password might be incorrect, or the system's PAM configuration (e.g., `/etc/pam.d/pwauth`) is missing or misconfigured.

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
Bridging the gap between a web server process (usually running as `apache`) and the system's secure authentication store (usually restricted to `root`) requires a trusted third-party binary like `pwauth`.

**💡 Solution Approach**:
1. **Repository Management**: Enabled the **EPEL** repository first, as the required modules are not present in standard RHEL/CentOS base repos.
2. **Pipe abstraction**: Used the `DefineExternalAuth` directive to create a clean abstraction of the system-level check within the Apache configuration.
3. **Standardized Identity**: Leveraged the existing system user `kareem`, ensuring that web access follows the server's local identity and access management (IAM) policies.

---

## ⚠️ Important Production Notes

🔧 **Encryption**: Always wrap Basic Authentication in **SSL/TLS (HTTPS)** to prevent credential sniffing on the wire.
🔐 **Security**: Be aware that `pwauth` is a security-sensitive binary. In high-security environments, ensure it is audited and patched.
📊 **Logs**: Monitor `/var/log/secure` to see web authentication attempts reflected in the system security logs.
🛡️ **Cleanup**: Disable `mod_authnz_external` if it's no longer required to minimize the server's footprint.

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Successfully installed and configured **mod_authnz_external** on stapp03.
- Integrated **Apache Basic Authentication** with the system **PAM** stack.
- Restricted the `/protected/` directory to authorized system users.
- Validated that user **kareem** can access web content using system credentials.

**🔍 Verification Completed**:
- **stapp03**: Apache functional | PAM bridge verified.
- **Connectivity**: curl check successful for verified system user.

**🔒 Configuration Summary**:
- **Mechanism**: mod_authnz_external + pwauth
- **Provider**: PAM Pipe
- **Target**: /var/www/html/protected
- **Platform**: CentOS/RHEL via EPEL
