# 🔐 Task 09 - Configure Nginx with PHP-FPM 8.1 on stapp01

**📌 Task Description**

The **Nautilus** application development team is deploying a new **PHP-based application** on **App Server 1 (stapp01)**. This task mirrors the standard LEMP stack integration pattern: **Nginx** as the web server and **PHP-FPM 8.1** as the FastCGI processor. Unlike Task 08, pre-existing PHP files (`index.php` and `info.php`) are already placed under the document root — they must **not** be modified.

**👉 Task Requirements**:
- **Target Server**: App Server 1 (`stapp01`)
- **Web Server**: Nginx on port `8098`
- **Document Root**: `/var/www/html`
- **PHP Version**: PHP-FPM **8.1** (via Remi repository)
- **PHP-FPM Socket**: `/var/run/php-fpm/default.sock`
- **Pre-existing files**: `/var/www/html/index.php`, `/var/www/html/info.php` — do **NOT** modify.
- **Operation Objectives**:
  - Install Nginx and configure for port 8098.
  - Install PHP-FPM 8.1 via EPEL/Remi repo.
  - Configure PHP-FPM Unix socket with `nginx` user ownership.
  - Link Nginx to PHP-FPM via `fastcgi_pass`.
  - Validate via `curl` from Jump Host.

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to App Server 1

```bash
ssh tony@stapp01
sudo su -
```

---

## 🔹 Step 2: Install and Configure Nginx

```bash
yum install -y nginx
```

Edit `/etc/nginx/nginx.conf` — update or replace the `server {}` block:

```nginx
server {
    listen       8098;
    listen       [::]:8098;
    server_name  _;
    root         /var/www/html;
    index        index.php index.html;

    location ~ \.php$ {
        fastcgi_pass unix:/var/run/php-fpm/default.sock;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
}
```

```bash
nginx -t
systemctl enable --now nginx
```

---

## 🔹 Step 3: Install PHP-FPM 8.1 via Remi Repository

```bash
dnf update -y
dnf install -y epel-release
dnf install -y https://rpms.remirepo.net/enterprise/remi-release-9.rpm

dnf module reset php -y
dnf module enable php:remi-8.1 -y

dnf install -y php-fpm php-cli php-mysqlnd php-zip php-devel \
               php-gd php-mbstring php-curl php-xml php-bcmath php-json

# Verify version
php -v
```

---

## 🔹 Step 4: Configure PHP-FPM Socket for Nginx

Edit `/etc/php-fpm.d/www.conf`:

```ini
user = nginx
group = nginx

listen = /var/run/php-fpm/default.sock

listen.owner = nginx
listen.group = nginx
listen.mode = 0660
```

```bash
# Ensure socket directory exists
mkdir -p /var/run/php-fpm/

systemctl enable --now php-fpm
systemctl status php-fpm
```

---

## 🔹 Step 5: Verification from Jump Host

```bash
curl http://stapp01:8098/index.php
```

**Expected Output**:
```
Welcome to xFusionCorp Industries!
```

> ⚠️ **Do NOT modify** `/var/www/html/index.php` or `/var/www/html/info.php` — they were pre-placed as part of the application setup.

---

## 📋 Quick Command Reference

```bash
# Nginx install + config
sudo yum install -y nginx
# Edit /etc/nginx/nginx.conf: listen 8098 + fastcgi block
sudo nginx -t && sudo systemctl enable --now nginx

# PHP-FPM 8.1 install
sudo dnf install -y epel-release
sudo dnf install -y https://rpms.remirepo.net/enterprise/remi-release-9.rpm
sudo dnf module reset php -y && sudo dnf module enable php:remi-8.1 -y
sudo dnf install -y php-fpm php-cli
# Edit /etc/php-fpm.d/www.conf: user/group/socket = nginx
sudo mkdir -p /var/run/php-fpm
sudo systemctl enable --now php-fpm

# Validate
curl http://stapp01:8098/index.php
```

---

## 💡 Common Issues & Fixes

| Issue | Cause | Fix |
|-------|-------|-----|
| PHP shown as plain text | Missing `fastcgi_pass` config | Verify `location ~ \.php$` block |
| 502 Bad Gateway | PHP-FPM socket missing | Check `ls /var/run/php-fpm/default.sock` |
| Module enable fails | Conflicting PHP stream | Run `dnf module reset php -y` first |
| 403 on `/var/www/html` | Wrong directory ownership | `chown -R nginx:nginx /var/www/html` |

---

## 🔧 Troubleshooting

### **PHP output raw (not executed)**
- Confirm `location ~ \.php$` block exists in the active Nginx server block.
- Confirm `fastcgi_param SCRIPT_FILENAME` uses `$document_root$fastcgi_script_name`.
- Restart both services: `systemctl restart nginx php-fpm`.

### **502 Bad Gateway**
- Verify socket file: `ls -la /var/run/php-fpm/default.sock`.
- Ensure `listen.owner = nginx` and `listen.group = nginx` in `www.conf`.
- Check PHP-FPM logs: `journalctl -u php-fpm`.

### **PHP module enable fails**
```bash
# If dnf module reset doesn't clear the conflict
dnf remove php* -y
dnf module reset php -y
dnf module enable php:remi-8.1 -y
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The Unix socket path must match exactly between Nginx's `fastcgi_pass` and PHP-FPM's `listen`. A path mismatch causes a **502 Bad Gateway** even when both services are running. Additionally, the parent directory `/var/run/php-fpm/` may not exist on a fresh install and must be created explicitly before starting PHP-FPM.

**💡 Solution Approach**:
1. **Explicit directory creation**: Used `mkdir -p /var/run/php-fpm/` to ensure the socket parent path exists before starting PHP-FPM.
2. **Consistent socket path**: Defined `/var/run/php-fpm/default.sock` identically in both `nginx.conf` and `www.conf`.
3. **Ownership alignment**: Set `user`, `group`, `listen.owner`, and `listen.group` all to `nginx` to grant Nginx access to the socket.
4. **Pre-existing files respected**: `index.php` and `info.php` were left untouched; only service configuration was modified.

---

## ⚠️ Important Production Notes

🔧 **Socket vs TCP**: Unix sockets are faster than TCP (127.0.0.1:9000) for local communication. Always prefer sockets for single-server setups.
🔐 **Security**: Never expose PHP-FPM port 9000 externally. It should be reachable only by the local Nginx process.
📊 **Logs**: Monitor `/var/log/php-fpm/www-error.log` and `/var/log/nginx/error.log` for FastCGI failures.
🛡️ **OPcache**: Add `php-opcache` for production to cache compiled PHP bytecode and boost performance.
📁 **Files**: Do not overwrite pre-existing PHP application files during service configuration steps.

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Deployed **Nginx** on port **8098** on `stapp01`.
- Installed **PHP-FPM 8.1** via Remi repository.
- Created socket directory `/var/run/php-fpm/` and configured Unix socket with `nginx` ownership.
- Linked Nginx ↔ PHP-FPM via `fastcgi_pass`.
- Validated PHP execution via `curl` — pre-existing files untouched.

**🔍 Verification Completed**:
- **stapp01**: Nginx active | PHP-FPM active | FastCGI socket confirmed.
- **Output**: `Welcome to xFusionCorp Industries!` returned by `curl http://stapp01:8098/index.php`.

**🔒 Configuration Summary**:
- **Server**: stapp01
- **Nginx Port**: 8098
- **PHP Version**: 8.1 (Remi)
- **Socket**: /var/run/php-fpm/default.sock
- **Document Root**: /var/www/html
