# 🔐 Task 08 - Configure Nginx with PHP-FPM 8.1 on stapp03

**📌 Task Description**

The **Nautilus** application development team is deploying a new **PHP-based application** on **App Server 3 (stapp03)**. The infrastructure requirement calls for **Nginx** as the web server integrated with **PHP-FPM 8.1** as the FastCGI process manager for handling PHP execution. This setup separates the web serving and PHP processing concerns for improved performance and security.

**👉 Task Requirements**:
- **Target Server**: App Server 3 (`stapp03`)
- **Web Server**: Nginx on port `8092`
- **Document Root**: `/var/www/html`
- **PHP Version**: PHP-FPM **8.1** (via Remi repository)
- **PHP-FPM Port**: `9000` (via Unix socket: `/var/run/php-fpm/default.sock`)
- **Operation Objectives**:
  - Install Nginx and configure for port 8092.
  - Install PHP-FPM 8.1 via EPEL/Remi repo.
  - Configure PHP-FPM socket with nginx ownership.
  - Link Nginx to PHP-FPM via FastCGI directives.
  - Create an `index.php` and validate with `curl`.

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to App Server 3

```bash
ssh banner@stapp03
sudo su -
```

---

## 🔹 Step 2: Install and Configure Nginx

```bash
yum install -y nginx
```

Edit the server block in `/etc/nginx/nginx.conf`:

```nginx
server {
    listen       8092;
    listen       [::]:8092;
    server_name  _;
    root         /var/www/html;
    index        index.php index.html;

    # PHP-FPM via Unix socket
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

php -v  # Verify: PHP 8.1.x
```

---

## 🔹 Step 4: Configure PHP-FPM for Nginx

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
systemctl enable --now php-fpm
systemctl status php-fpm
```

---

## 🔹 Step 5: Create Test PHP File

```bash
echo "<?php echo 'Welcome to xFusionCorp Industries!'; ?>" > /var/www/html/index.php
chown nginx:nginx /var/www/html/index.php
```

---

## 🔹 Step 6: Verification from Jump Host

```bash
curl http://stapp03:8092/index.php
```

**Expected Output**:
```
Welcome to xFusionCorp Industries!
```

> ⚠️ If you see raw `<?php` text in the output, Nginx is not properly connected to PHP-FPM. Re-check the `fastcgi_pass` socket path and PHP-FPM socket configuration.

---

## 📋 Quick Command Reference

```bash
# Nginx
sudo yum install -y nginx
# Edit /etc/nginx/nginx.conf (port 8092 + fastcgi_pass)
sudo nginx -t && sudo systemctl enable --now nginx

# PHP-FPM 8.1
sudo dnf install -y epel-release
sudo dnf install -y https://rpms.remirepo.net/enterprise/remi-release-9.rpm
sudo dnf module enable php:remi-8.1 -y
sudo dnf install -y php-fpm php-cli
# Edit /etc/php-fpm.d/www.conf (user/group/socket = nginx)
sudo systemctl enable --now php-fpm

# Test
curl http://stapp03:8092/index.php
```

---

## 💡 Common Issues & Fixes

| Issue | Cause | Fix |
|-------|-------|-----|
| PHP shown as plain text | Nginx not passing to PHP-FPM | Verify `fastcgi_pass` socket path |
| 502 Bad Gateway | PHP-FPM not running or wrong socket | Check `systemctl status php-fpm` |
| 403 Forbidden | Wrong document root ownership | `chown -R nginx:nginx /var/www/html` |
| PHP version wrong | Wrong module enabled | `dnf module reset php -y` before enable |

---

## 🔧 Troubleshooting

### **PHP output raw (not executed)**
- Confirm `location ~ \.php$` block exists in the Nginx server block.
- Confirm `fastcgi_param SCRIPT_FILENAME` uses `$document_root$fastcgi_script_name`.
- Restart both services: `systemctl restart nginx php-fpm`.

### **502 Bad Gateway**
- Run `ls -la /var/run/php-fpm/` to confirm the socket file exists.
- Ensure `listen.owner = nginx` and `listen.group = nginx` are set in `www.conf`.
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
The primary challenge is getting the **Unix socket path** to match between Nginx (`fastcgi_pass`) and PHP-FPM (`listen`). A mismatch causes a 502 Bad Gateway even when both services are running.

**💡 Solution Approach**:
1. **Consistent socket path**: Used `/var/run/php-fpm/default.sock` in both `nginx.conf` and `www.conf`.
2. **Ownership alignment**: Set `user/group/listen.owner/listen.group` all to `nginx` so the socket file is accessible.
3. **Module pin**: Used `dnf module reset` before `enable` to avoid conflicts with the default PHP stream.

---

## ⚠️ Important Production Notes

🔧 **Socket vs TCP**: Unix sockets are faster than TCP (127.0.0.1:9000) for local PHP-FPM. Use sockets for single-server setups.
🔐 **Security**: Never expose PHP-FPM port 9000 to the internet. It should only accept connections from the local Nginx process.
📊 **Logs**: Monitor `/var/log/php-fpm/www-error.log` and `/var/log/nginx/error.log` for FastCGI failures.
🛡️ **OPcache**: Enable `php-opcache` for production to cache compiled PHP bytecode and significantly improve performance.

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Deployed **Nginx** on port **8092** with `/var/www/html` as document root.
- Installed **PHP-FPM 8.1** via Remi repository.
- Configured PHP-FPM Unix socket with **nginx** user ownership.
- Linked Nginx ↔ PHP-FPM via `fastcgi_pass`.
- Validated PHP execution via `curl` from the Jump Host.

**🔍 Verification Completed**:
- **stapp03**: Nginx active | PHP-FPM active | FastCGI socket confirmed.
- **Output**: `Welcome to xFusionCorp Industries!` returned by `curl`.

**🔒 Configuration Summary**:
- **Server**: stapp03
- **Nginx Port**: 8092
- **PHP Version**: 8.1 (Remi)
- **Socket**: /var/run/php-fpm/default.sock
- **Document Root**: /var/www/html
