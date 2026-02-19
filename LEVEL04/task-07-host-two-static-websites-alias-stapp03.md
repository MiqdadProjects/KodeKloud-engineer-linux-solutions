# 🔐 Task 07 - Host Two Static Websites on Apache with URL Aliases on stapp03

**📌 Task Description**

**xFusionCorp Industries** is preparing two static websites (**beta** and **demo**) for staging on **App Server 3 (stapp03)**. The websites exist as backup archives on the Jump Host and need to be deployed under Apache using **URL aliases**, making each accessible via a dedicated sub-path on a custom port. This task covers the full workflow from file transfer to service validation.

**👉 Task Requirements**:
- **Target Server**: App Server 3 (`stapp03`)
- **Apache Port**: `8085`
- **Sites**:
  - `beta` → `http://localhost:8085/beta/`
  - `demo` → `http://localhost:8085/demo/`
- **Source Files**: `/home/thor/beta` and `/home/thor/demo` on Jump Host
- **Operation Objectives**:
  - Install Apache.
  - Reconfigure the listening port.
  - Transfer site content from the Jump Host.
  - Deploy content to `/var/www/html/`.
  - Configure `Alias` directives in Apache.
  - Validate with `curl` locally.

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Transfer Files from Jump Host

Run from the **Jump Host**:

```bash
scp -r /home/thor/beta banner@stapp03:/tmp/
scp -r /home/thor/demo banner@stapp03:/tmp/
```

---

## 🔹 Step 2: Connect and Install Apache

```bash
ssh banner@stapp03
sudo su -

yum install -y httpd
```

---

## 🔹 Step 3: Configure Custom Port

```bash
vi /etc/httpd/conf/httpd.conf
```

Change:
```apache
Listen 80
```
To:
```apache
Listen 8085
```

---

## 🔹 Step 4: Deploy Website Content

```bash
mv /tmp/beta /var/www/html/
mv /tmp/demo /var/www/html/

chown -R apache:apache /var/www/html/beta /var/www/html/demo
chmod -R 755 /var/www/html/beta /var/www/html/demo
```

---

## 🔹 Step 5: Configure Apache Alias Directives

```bash
vi /etc/httpd/conf.d/websites.conf
```

**Configuration Content**:
```apache
# Beta website configuration
Alias /beta "/var/www/html/beta"
<Directory "/var/www/html/beta">
    Options Indexes FollowSymLinks
    AllowOverride None
    Require all granted
</Directory>

# Demo website configuration
Alias /demo "/var/www/html/demo"
<Directory "/var/www/html/demo">
    Options Indexes FollowSymLinks
    AllowOverride None
    Require all granted
</Directory>
```

---

## 🔹 Step 6: Validate and Start Service

```bash
# Check configuration syntax
httpd -t

# Start and enable Apache
systemctl start httpd
systemctl enable httpd
```

---

## 🔹 Step 7: Verification

```bash
curl http://localhost:8085/beta/
curl http://localhost:8085/demo/
```

**Expected Output (beta)**:
```html
<h1>KodeKloud</h1>
<p>This is a sample page for our beta website</p>
```

**Expected Output (demo)**:
```html
<h1>KodeKloud</h1>
<p>This is a sample page for our demo website</p>
```

---

## 📋 Quick Command Reference

```bash
# Transfer from Jump Host
scp -r /home/thor/{beta,demo} banner@stapp03:/tmp/

# On stapp03
sudo yum install -y httpd
sudo sed -i 's/Listen 80/Listen 8085/' /etc/httpd/conf/httpd.conf
sudo mv /tmp/beta /tmp/demo /var/www/html/
sudo chown -R apache:apache /var/www/html/beta /var/www/html/demo

# Config
cat <<EOF | sudo tee /etc/httpd/conf.d/websites.conf
Alias /beta "/var/www/html/beta"
<Directory "/var/www/html/beta">
    Options Indexes FollowSymLinks
    AllowOverride None
    Require all granted
</Directory>

Alias /demo "/var/www/html/demo"
<Directory "/var/www/html/demo">
    Options Indexes FollowSymLinks
    AllowOverride None
    Require all granted
</Directory>
EOF

sudo systemctl enable --now httpd
```

---

## 💡 Common Issues & Fixes

| Issue | Cause | Fix |
|-------|-------|-----|
| 403 Forbidden | Directory ownership wrong | `chown -R apache:apache /var/www/html/beta` |
| 404 Not Found | Alias path mismatch | Verify exact path in `Alias` directive |
| Port conflict | Something using 8085 | `ss -tulpn \| grep 8085` |

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Installed **Apache** on `stapp03` and set port to **8085**.
- Deployed `beta` and `demo` sites to `/var/www/html/`.
- Configured `Alias` directives for clean sub-path URLs.
- Verified both sites accessible via `curl`.

**🔒 Configuration Summary**:
- **Port**: 8085
- **beta**: http://localhost:8085/beta/
- **demo**: http://localhost:8085/demo/
- **Config**: /etc/httpd/conf.d/websites.conf
