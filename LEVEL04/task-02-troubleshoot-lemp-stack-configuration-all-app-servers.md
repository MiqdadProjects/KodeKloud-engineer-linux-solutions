# 🔐 Task 02 - Troubleshoot LEMP Stack Configuration on all App Servers

**📌 Task Description**

The **Nautilus** production support team has deployed a **LEMP (Linux, Nginx, MySQL/MariaDB, PHP)** stack across all application servers in the **Stratos DC**. The stack uses **Nginx** and **PHP-FPM** to serve a database-connected PHP application. However, due to configuration errors, the PHP page is failing to load on the web servers. This task involves identifying misconfigurations—such as incorrect listening ports, missing PHP processing logic, and service status issues—across all App Servers to ensure the website is accessible via the Load Balancer (LBR) and locally.

**👉 Task Requirements**:
- **App Servers**: `stapp01`, `stapp02`, `stapp03`
- **Web Server**: Nginx (Running on port **80**)
- **Document Root**: `/var/www/html/`
- **PHP Engine**: PHP-FPM (FastCGI)
- **Operation Objectives**:
  - Verify and correct Nginx listening ports.
  - Configure Nginx to properly process `.php` files via `fastcgi_pass`.
  - Ensure correct document root and index file definitions.
  - Validate database connectivity from the PHP application.
  - Enable and restart all required services.
- **Verification**: 
  - `curl http://localhost` must return database connection success.
  - LBR URL must serve the functional PHP application.

**💡 Note**: The specific misconfiguration may vary between servers (e.g., port mismatch vs. missing PHP block). Always verify the current state using `ss` and `systemctl` before applying fixes.

---

## 🔹 Step 1: Discover Current Port Bindings

Begin by checking if Nginx is listening on the correct port (**80**) on each app server.

```bash
# Connect to App Server (Repeat for stapp01, stapp02, stapp03)
ssh [user]@[app-server]
sudo su -

# Check active Nginx listeners
ss -lntp | grep nginx
```

**Observation Analysis**:
If the output shows a different port (e.g., `0.0.0.0:8084`), Nginx is misconfigured and will be unreachable via the standard HTTP port.

---

## 🔹 Step 2: Correct Nginx Configuration (stapp01)

On **stapp01**, the issue is often a simple port mismatch in the configuration file.

```bash
vi /etc/nginx/nginx.conf
```

**Required Corrections**:
Find the `server` block and update the `listen` directive and `root` path:

```nginx
server {
    listen 80; # Change from 8084 or other placeholder
    server_name _;

    root /var/www/html;
    index index.php index.html;
    
    # ... existing location blocks ...
}
```

---

## 🔹 Step 3: Comprehensive LEMP Configuration (stapp02 & stapp03)

If the configuration is significantly broken or missing PHP processing logic, replace/update the `nginx.conf` with a robust LEMP template.

```bash
vi /etc/nginx/nginx.conf
```

**Full LEMP Configuration Template**:

```nginx
user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log;
pid /run/nginx.pid;

events {
    worker_connections 1024;
}

http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;
    sendfile        on;
    keepalive_timeout 65;

    server {
        listen 80;
        server_name _;

        root /var/www/html;
        index index.php index.html;

        location / {
            try_files $uri $uri/ /index.php?$args;
        }

        # PHP-FPM Processing Block
        location ~ \.php$ {
            root /var/www/html;
            # Path to PHP-FPM socket (verify path per version)
            fastcgi_pass unix:/var/opt/remi/php74/run/php-fpm/www.sock;
            fastcgi_index index.php;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
            include fastcgi_params;
        }
    }
}
```

---

## 🔹 Step 4: Validate Services and Connectivity

After modifying the configuration, ensure both the web server and the PHP processor are functional.

```bash
# Check PHP-FPM status
systemctl status php74-php-fpm

# Verify Nginx syntax and restart
nginx -t
systemctl restart nginx
systemctl enable nginx
```

---

## 🔹 Step 5: Final Verification

### **Local Verification (on each App Server)**:
```bash
curl http://localhost
```
**Expected Output**:
`App is able to connect to the database using user kodekloud_aim`

### **Global Verification (via LBR)**:
Click the **LBR** button in the top bar. The page should load successfully and display the database connection message. Refresh several times to ensure all backend nodes are responding correctly.

---

## 📋 Quick Command Reference

```bash
# Verify Port
ss -lntp | grep nginx

# Syntax Check
sudo nginx -t

# Service Control
sudo systemctl restart nginx
sudo systemctl status php74-php-fpm
```

---

## 💡 Common LEMP Issues & Fixes

### **Issue 1: 403 Forbidden**
**Cause**: Missing `index.php` in the `index` directive or incorrect directory permissions.
**Fix**: Ensure `index index.php index.html;` is present and `/var/www/html/` is readable by the `nginx` user.

### **Issue 2: 502 Bad Gateway / File Not Found**
**Cause**: Nginx cannot communicate with the PHP-FPM socket or `SCRIPT_FILENAME` is incorrect.
**Fix**: Verify the `fastcgi_pass` path. Use `ls -l /var/opt/remi/php74/run/php-fpm/www.sock` to confirm the socket exists.

---

## 🔧 Troubleshooting Failures

### **Error: PHP-FPM service not found**
**Symptoms**: `systemctl` returns error.
**Solution**: PHP-FPM may be named differently depending on the version (e.g., `php-fpm`, `php72-php-fpm`). Use `systemctl list-units | grep php` to find the correct service name.

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The PHP application depends on a functional connection to a central database. Misconfiguring the `location ~ \.php$` block often results in Nginx downloading the PHP file as plain text instead of executing it, potentially exposing database credentials.

**💡 Solution Approach**:
1. **Socket Protocol**: Used `unix:` socket communication for `fastcgi_pass` instead of TCP, as it is more efficient for local communication between Nginx and PHP-FPM.
2. **Dynamic Rooting**: Utilized `$document_root` variable in the `fastcgi_param SCRIPT_FILENAME` directive to ensure the PHP interpreter always looks in the correct location, regardless of root changes.
3. **Graceful Failover**: Implemented `try_files` to ensure that requests for non-existent files are handled gracefully or passed to the index controller.

---

## ⚠️ Important Production Notes

🔧 **Socket Permissions**: Ensure the `nginx` user has permissions to read/write to the PHP-FPM socket file.
🔐 **Security**: Disable `cgi.fix_pathinfo` in `php.ini` to prevent certain types of script injection attacks.
📊 **Performance**: Tune `worker_connections` in `nginx.conf` and `pm.max_children` in PHP-FPM pool configs for high-traffic environments.

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Corrected Nginx listening ports to **80** across all application servers.
- Implemented robust **PHP-FPM** processing logic in `nginx.conf`.
- Validated **Database Connectivity** for the LEMP-based application.
- Verified end-to-end functionality via the Load Balancer.

**🔍 Verification Completed**:
- **stapp01/02/03**: Local curl tests successful.
- **LBR**: Application accessible and serving dynamic content.

**🔒 Configuration Summary**:
- **Port**: 80
- **PHP Handler**: PHP 7.4 FPM (Remi)
- **DB User**: kodekloud_aim
- **Status**: Operational
