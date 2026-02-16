# 🔐 Task 10 - Setup Nginx with SSL/TLS on stapp02

**📌 Task Description**

The **Nautilus** systems administration team is preparing for a new application deployment on **App Server 2 (stapp02)** in the **Stratos Datacenter**. A key requirement for this deployment is the implementation of transport-layer security (SSL/TLS) to encrypt web traffic. This task involves installing the **Nginx** web server, deploying provided self-signed certificates and keys, and configuring Nginx to serve content over both standard HTTP and secure HTTPS protocols.

**👉 Task Requirements**:
- **Target Server**: App Server 2 (`stapp02`)
- **Web Server**: Nginx
- **SSL Artifacts**:
  - Certificate: `/tmp/nautilus.crt`
  - Private Key: `/tmp/nautilus.key`
- **SSL Storage Path**: `/etc/nginx/ssl/` (recommended)
- **Web Content**: `index.html` with content `Welcome!`
- **Operation Objectives**:
  - Install Nginx from the EPEL repository.
  - Securely migrate SSL certificates to the system configuration tree.
  - Set appropriate file permissions for the private key (**600**).
  - Configure Nginx to listen on ports **80** (HTTP) and **443** (HTTPS/TLS).
  - Enable and start the Nginx service.
- Maintain standardized document root paths.
- Verify TLS handshake and content delivery via `curl`.

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to App Server 2

```bash
ssh steve@stapp02
```

**Purpose**: Establish an administrative session to prepare the web environment.

**Expected Output**:
```
steve@stapp02's password: 
Welcome to App Server 2 - Stratos Datacenter
[steve@stapp02 ~]$
```

**Success Indicators**:
- ✅ Successfully authenticated as user `steve`
- ✅ Switched to root using `sudo su -`

---

## 🔹 Step 2: Install Nginx Web Server

```bash
# Enable EPEL repository (required for Nginx)
yum install -y epel-release

# Install Nginx package
yum install -y nginx
```

**Purpose**: Provision the high-performance web server and its dependencies.

---

## 🔹 Step 3: Secure SSL Certificate Deployment

```bash
# Create a dedicated directory for SSL artifacts
mkdir -p /etc/nginx/ssl

# Move existing certificates to the configuration path
mv /tmp/nautilus.crt /etc/nginx/ssl/nautilus.crt
mv /tmp/nautilus.key /etc/nginx/ssl/nautilus.key

# Implement secure ownership and permissions
chown root:root /etc/nginx/ssl/nautilus.*
chmod 644 /etc/nginx/ssl/nautilus.crt
chmod 600 /etc/nginx/ssl/nautilus.key
```

**Purpose**: Move sensitive cryptographic keys to a protected system location and enforce strict file permissions to prevent unauthorized access.

---

## 🔹 Step 4: Configure HTTPS/TLS Listeners

```bash
vi /etc/nginx/nginx.conf
```

**Purpose**: Update the Nginx configuration to enable the SSL module and define the server blocks.

**Key Configuration Sections**:

```nginx
# Standard Server Block (Port 80)
server {
    listen       80;
    listen       [::]:80;
    server_name  172.16.238.11; # Server IP or Hostname
    root         /usr/share/nginx/html;
}

# TLS Enabled Server Block (Port 443)
server {
    listen       443 ssl http2;
    listen       [::]:443 ssl http2;
    server_name  172.16.238.11;
    root         /usr/share/nginx/html;

    # SSL Certificate Paths
    ssl_certificate "/etc/nginx/ssl/nautilus.crt";
    ssl_certificate_key "/etc/nginx/ssl/nautilus.key";

    # SSL Optimization
    ssl_session_cache shared:SSL:1m;
    ssl_session_timeout  10m;
    ssl_ciphers PROFILE=SYSTEM;
    ssl_prefer_server_ciphers on;
}
```

---

## 🔹 Step 5: Deploy Initial Web Content

```bash
echo "Welcome!" > /usr/share/nginx/html/index.html
```

**Purpose**: Provide a baseline index page to verify that the server is successfully delivering content.

---

## 🔹 Step 6: Validate and Activate Service

```bash
# Verify Nginx configuration syntax
nginx -t

# Apply changes and enable persistence
systemctl restart nginx
systemctl enable nginx
```

**Expected Output for `nginx -t`**:
```text
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

---

## 🔹 Step 7: Final Verification (Jump Host)

```bash
# Test HTTPS connectivity (Ignoring self-signed warnings with -k)
curl -Ik https://stapp02.stratos.xfusioncorp.com

# Verify content
curl -k https://stapp02.stratos.xfusioncorp.com
```

**Expected Results**:
- **Headers Check**: `HTTP/1.1 200 OK` | `Server: nginx`
- **Content Check**: `Welcome!`

**Success Indicators**:
- ✅ Nginx successfully performing TLS handshake with provided certs.
- ✅ Content accessible over port 443.
- ✅ Service survives reboot.

---

## 📋 Quick Command Reference

Setup for **stapp02**:

```bash
# Packages
sudo yum install -y epel-release nginx

# SSL Setup
sudo mkdir -p /etc/nginx/ssl
sudo mv /tmp/nautilus.* /etc/nginx/ssl/
sudo chmod 600 /etc/nginx/ssl/nautilus.key

# Web Content
echo "Welcome!" | sudo tee /usr/share/nginx/html/index.html

# Service Control 
sudo nginx -t && sudo systemctl enable --now nginx
```

---

## 💡 Common SSL Issues & Fixes

### **Issue 1: SSL Certificate Key Mismatch**
**Problem**: Nginx fails to start with "SSL_CTX_use_PrivateKey_file failed" error.
**Fix**: Ensure the certificate (`.crt`) was actually generated from the private key (`.key`) provided. Verification: `openssl x509 -noout -modulus -in cert.crt | openssl md5` and `openssl rsa -noout -modulus -in key.key | openssl md5` must match.

### **Issue 2: Browser/Curl Untrusted Warning**
**Problem**: Accessing via HTTPS returns "Certificate signed by unknown authority".
**Fix**: Since this is a self-signed certificate, use the `-k` or `--insecure` flag in `curl` for testing. In production, use certificates from a trusted CA.

### **Issue 3: Port 443 Blocked**
**Problem**: Service is running locally, but unreachable from Jump Host.
**Fix**: Check `firewalld` or `iptables` and allow port 443 traffic:
```bash
sudo firewall-cmd --permanent --add-service=https && sudo firewall-cmd --reload
```

---

## 🔧 Troubleshooting Failures

### **Error: nginx: [emerg] open() "/etc/nginx/ssl/nautilus.key" failed (Permission denied)**
**Symptoms**: Service fails to start.
**Solution**: Ensure the Nginx master process (running as root) can read the key, or check for SELinux violations related to custom file paths.

### **Error: curl: (35) Peer reports incompatible TLS version**
**Symptoms**: TLS handshake fails.
**Solution**: Check `ssl_protocols` and `ssl_ciphers` in `nginx.conf`. Ensure they are compatible with the client version.

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
Working with self-signed certificates requires careful path management and permission handling. Placing these files in `/tmp` is insecure for production; migrating them to `/etc/nginx/ssl/` and setting `chmod 600` on the private key is crucial for a passing security audit.

**💡 Solution Approach**:
1. **Repository Logic**: Leveraged the EPEL repository to ensure the latest stable version of Nginx for CentOS/RHEL was utilized.
2. **Path Hardening**: Established a non-standard but clean directory structure (`/etc/nginx/ssl`) to separate cryptographic assets from application configuration.
3. **Dual Protocol Support**: Configured both HTTP (80) and HTTPS (443) listeners to ensure the server is accessible regardless of the initial request protocol.

---

## ⚠️ Important Production Notes

🔧 **Modern TLS**: For production, use `ssl_protocols TLSv1.2 TLSv1.3;` and disable older versions like SSLv3 and TLSv1.0/1.1.
🔐 **HSTS**: Consider adding the `Strict-Transport-Security` header to force HTTPS usage.
📊 **Logs**: Monitor `/var/log/nginx/error.log` for "handshake failure" logs which might indicate client-side compatibility issues.
🛡️ **Audit**: Use tools like `SSLLabs` or `testssl.sh` to evaluate the strength of your TLS configuration.

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Installed **Nginx** via EPEL on App Server 2.
- Secured and deployed **SSL/TLS certificates** in a dedicated system path.
- Configured a dual-protocol web stack (HTTP/HTTPS).
- Deployed standardized web index content.
- Verified transport security via authenticated `curl` requests.

**🔍 Verification Completed**:
- **stapp02**: Service active | Config valid | SSL handshake functional.
- **Connectivity**: curl validation successful from Jump Host.

**🔒 Configuration Summary**:
- **Webserver**: Nginx
- **SSL Path**: /etc/nginx/ssl/
- **Port**: 443 (HTTPS)
- **Status**: Operational
