# 🔐 Task 07 - Configure Apache Basic Authentication on stapp03

**📌 Task Description**

The **Nautilus** security team has identified a requirement to protect confidential sub-directories within the web document root on **App Server 3 (stapp03)**. Specifically, the directory `/var/www/html/itadmin` must be restricted so that only authorized personnel can access its content. To achieve this, we will implement **Apache Basic Authentication** using `.htaccess` files. This involves creating a secure credential store, configuring Apache to permit directory-level overrides, and deploying restricted content for the IT Administration team.

**👉 Task Requirements**:
- **Target Server**: App Server 3 (`stapp03`)
- **Restricted Directory**: `/var/www/html/itadmin`
- **Authentication Method**: Basic Auth via `.htaccess`
- **User Credentials**:
  - Username: `javed`
  - Password: `YchZHRcLkL`
- **Password File**: `/etc/httpd/.htpasswd`
- **Operation Objectives**:
  - Create the target directory structure.
  - Generate a secure password file using `htpasswd`.
  - Configure `.htaccess` with authentication directives.
  - Enable `AllowOverride` in the main `httpd.conf`.
  - Deploy the `index.html` from the Jump Host.
- Ensure the default document root remains globally accessible except for the protected path.
- Verify authentication prompt and successful login via `curl`.

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Transfer Web Content from Jump Host

Perform this step from the **Jump Host**:

```bash
# Copy the IT Admin index page to the app server
scp /tmp/index.html banner@stapp03:/tmp/
```

**Purpose**: Move the protected content to the target server's temporary storage for later deployment.

---

## 🔹 Step 2: Connect to App Server 3 and Prepare Directory

```bash
ssh banner@stapp03
sudo su -

# Create the restricted directory
mkdir -p /var/www/html/itadmin
```

**Purpose**: Establish a workspace for the restricted application.

---

## 🔹 Step 3: Create Authentication Credentials

```bash
# Create the password file and add user 'javed'
# -c: create file, -b: take password from command line
htpasswd -cb /etc/httpd/.htpasswd javed YchZHRcLkL
```

**Purpose**: Stores the hashed password in a location accessible to the Apache service but outside of the document root for security.

**Success Indicators**:
- ✅ Password file `/etc/httpd/.htpasswd` created.
- ✅ User `javed` encrypted in the file.

---

## 🔹 Step 4: Configure .htaccess Directives

```bash
vi /var/www/html/itadmin/.htaccess
```

**Purpose**: Define the specific security policy for the `itadmin` directory.

**Configuration Content**:
```apache
AuthType Basic
AuthName "Restricted Content"
AuthUserFile /etc/httpd/.htpasswd
Require valid-user
```

**Directive Breakdown**:
- `AuthType Basic`: Uses standard HTTP Basic Authentication.
- `AuthUserFile`: Points to the credential file created in Step 3.
- `Require valid-user`: Permits any user present in the `.htpasswd` file.

---

## 🔹 Step 5: Enable Apache Overrides

```bash
vi /etc/httpd/conf/httpd.conf
```

**Purpose**: By default, Apache ignores `.htaccess` files. We must explicitly allow the `/var/www/html` directory to override global settings.

**Changes Required**:
Find the directory block for `/var/www/html` and update the `AllowOverride` directive:

```apache
<Directory "/var/www/html">
    # ... other settings ...
    AllowOverride All
</Directory>
```

**Success Indicators**:
- ✅ `AllowOverride All` set for the primary document root.

---

## 🔹 Step 6: Deploy Content and Restart Service

```bash
# Move the index file to the protected path
mv /tmp/index.html /var/www/html/itadmin/

# Restart Apache to apply configuration changes
systemctl restart httpd
```

---

## 🔹 Step 7: Final Verification

```bash
# Test the URL locally (should return 401 Unauthorized without credentials)
curl -I http://localhost:8080/itadmin/

# Test with credentials
curl -u javed:YchZHRcLkL http://localhost:8080/itadmin/
```

**Expected Results**:
- **Unauthenticated**: `HTTP/1.1 401 Unauthorized`
- **Authenticated**: `HTTP/1.1 200 OK` | Content of `index.html`

**Success Indicators**:
- ✅ Authentication prompt triggered correctly.
- ✅ Valid credentials grant access to the protected directory.

---

## 📋 Quick Command Reference

Setup sequence for **stapp03**:

```bash
# Directory & Content
sudo mkdir -p /var/www/html/itadmin
sudo mv /tmp/index.html /var/www/html/itadmin/

# Auth File
sudo htpasswd -cb /etc/httpd/.htpasswd javed YchZHRcLkL

# .htaccess Creation
cat <<EOF | sudo tee /var/www/html/itadmin/.htaccess
AuthType Basic
AuthName "Restricted Content"
AuthUserFile /etc/httpd/.htpasswd
Require valid-user
EOF

# Global Config Patch
sudo sed -i '/<Directory "\/var\/www\/html">/,/<\/Directory>/ s/AllowOverride None/AllowOverride All/' /etc/httpd/conf/httpd.conf

# Rollout
sudo systemctl restart httpd
```

---

## 💡 Common Auth Issues & Fixes

### **Issue 1: Internal Server Error (500)**
**Problem**: There is a syntax error in the `.htaccess` file or a module (like `mod_auth_basic`) is missing.
**Fix**: Check `/var/log/httpd/error_log`. Ensure `AllowOverride` is not restricting the specific directive used.

### **Issue 2: Password Prompt Not Appearing**
**Problem**: Apache is ignoring the `.htaccess` file.
**Fix**: Ensure `AllowOverride All` (or at least `AllowOverride AuthConfig`) is set in the main `httpd.conf` for the parent directory.

### **Issue 3: Permission Denied for .htpasswd**
**Problem**: The Apache user cannot read the password file.
**Fix**: Check permissions. The file should be readable by the `apache` group.
```bash
sudo chown root:apache /etc/httpd/.htpasswd
sudo chmod 640 /etc/httpd/.htpasswd
```

---

## 🔧 Troubleshooting Failures

### **Error: configuration error:  couldn't check user: /itadmin/**
**Symptoms**: HTTP 500 error in browser.
**Solution**: Verify the path in `AuthUserFile` is absolute and correct.

### **Error: .htaccess: AllowOverride not allowed here**
**Symptoms**: Apache fails to process the override.
**Solution**: Ensure the `AllowOverride` directive is placed inside the correct `<Directory>` block in `httpd.conf`.

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The challenge is ensuring that directory-level security doesn't break the global accessibility of the website. Additionally, properly pointing to the `.htpasswd` file outside of the web-accessible tree is a critical security best practice.

**💡 Solution Approach**:
1. **Isolated Auth store**: Placed the `.htpasswd` file in `/etc/httpd/` instead of `/var/www/html/` to prevent users from downloading the hashed password file directly.
2. **Selective Overrides**: While `AllowOverride All` was used for simplicity in lab, in production, we advocate for `AllowOverride AuthConfig` to minimize performance impact and security risk.
3. **Double-Pass Audit**: Used both `-I` (Head) and `-u` (User) flags in `curl` to verify both the rejection of anonymous users and the acceptance of authorized ones.

---

## ⚠️ Important Production Notes

🔧 **Encryption**: Use `bcrypt` or `SHA-512` if supported for stronger password hashing.
🔐 **TLS Requirement**: Basic Auth sends credentials in plain-text (Base64). NEVER use this without HTTPS (SSL/TLS) in production.
📊 **Logs**: Monitor `/var/log/httpd/access_log` for repeated 401 errors, which may indicate a brute-force attack.
🛡️ **Audit**: Use `htpasswd -v` to verify credentials manually if users report login failures.

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Created the restricted IT Admin directory on **stapp03**.
- Configured a secure **Basic Authentication** credential system.
- Enabled **.htaccess overrides** in the global Apache configuration.
- Successfully restricted access to user `javed` with verified credentials.
- Confirmed functional URL access at `http://stapp03:8080/itadmin/`.

**🔍 Verification Completed**:
- **stapp03**: Apache reloaded | Security logic active.
- **Connectivity**: Local `curl` verification successful for user `javed`.

**🔒 Configuration Summary**:
- **User**: javed
- **Auth File**: /etc/httpd/.htpasswd
- **Access Level**: Directory-restricted
- **Override Status**: Enabled (All)
