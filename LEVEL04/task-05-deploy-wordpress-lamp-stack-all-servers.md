# 🔐 Task 05 - Deploy WordPress LAMP Stack on All App Servers and stdb01

**📌 Task Description**

**xFusionCorp Industries** is preparing to host a **WordPress** website across the **Stratos Datacenter** infrastructure. The storage server already provides a shared `/var/www/html` directory mounted on all application hosts. This task requires setting up the full **LAMP stack** (Apache + PHP + MariaDB) in a distributed fashion—PHP and Apache on all app servers, MariaDB on the dedicated Database Server—and verifying database connectivity through the Load Balancer.

**👉 Task Requirements**:
- **App Servers**: stapp01, stapp02, stapp03
  - Install: `httpd`, `php`, `php-mysqlnd`, `php-fpm`
  - Apache listening port: `8089`
- **Database Server**: stdb01
  - Install and configure: `mariadb-server`
  - Database: `kodekloud_db9`
  - User: `kodekloud_joy` (Password: `BruCStnMT5`, Host: `%`)
  - Grant: `ALL PRIVILEGES ON kodekloud_db9.*`
- **Validation**: Access the App URL through the LBR and confirm the message: `App is able to connect to the database using user kodekloud_joy`

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Part A: App Servers — Install LAMP Dependencies

Repeat on **stapp01**, **stapp02**, and **stapp03**:

```bash
ssh tony@stapp01   # (steve@stapp02 | banner@stapp03)
sudo su -

# Install Apache and PHP ecosystem
yum install -y httpd php php-mysqlnd php-fpm
```

**Success Indicators**:
- ✅ All packages installed without dependency errors.

---

## 🔹 Part B: Reconfigure Apache Port

```bash
vi /etc/httpd/conf/httpd.conf
```

**Change**:
```apache
# From:
Listen 80
# To:
Listen 8089
```

---

## 🔹 Part C: Deploy PHP Database Test File

```bash
cat > /var/www/html/dbtest.php << 'EOF'
<?php
$servername = "stdb01";
$username   = "kodekloud_joy";
$password   = "BruCStnMT5";
$dbname     = "kodekloud_db9";

try {
    $conn = new PDO("mysql:host=$servername;dbname=$dbname", $username, $password);
    $conn->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
    echo "App is able to connect to the database using user kodekloud_joy";
} catch(PDOException $e) {
    echo "Connection failed: " . $e->getMessage();
}
?>
EOF
```

---

## 🔹 Part D: Start and Enable Apache

```bash
systemctl start httpd
systemctl enable httpd
systemctl status httpd
```

---

## 🔹 Part E: Database Server — Install MariaDB

On **stdb01**:

```bash
ssh peter@stdb01
sudo su -

yum install -y mariadb-server
systemctl start mariadb
systemctl enable mariadb
```

---

## 🔹 Part F: Provision Database and User

```bash
mysql -u root
```

```sql
CREATE DATABASE kodekloud_db9;
CREATE USER 'kodekloud_joy'@'%' IDENTIFIED BY 'BruCStnMT5';
GRANT ALL PRIVILEGES ON kodekloud_db9.* TO 'kodekloud_joy'@'%';
FLUSH PRIVILEGES;
EXIT;
```

**Success Indicators**:
- ✅ `SHOW DATABASES;` includes `kodekloud_db9`.
- ✅ User `kodekloud_joy` visible in `mysql.user`.

---

## 🔹 Final Verification

Click the **App** button in the KodeKloud UI (via LBR link) and confirm:

```text
App is able to connect to the database using user kodekloud_joy
```

Or validate directly:
```bash
curl http://stlb01/dbtest.php
```

---

## 📋 Quick Command Reference

**App Servers (Repeat on all 3)**:
```bash
sudo yum install -y httpd php php-mysqlnd php-fpm
sudo sed -i 's/Listen 80/Listen 8089/' /etc/httpd/conf/httpd.conf
sudo systemctl enable --now httpd
```

**Database Server**:
```bash
sudo yum install -y mariadb-server
sudo systemctl enable --now mariadb
mysql -u root -e "CREATE DATABASE kodekloud_db9;"
mysql -u root -e "CREATE USER 'kodekloud_joy'@'%' IDENTIFIED BY 'BruCStnMT5';"
mysql -u root -e "GRANT ALL PRIVILEGES ON kodekloud_db9.* TO 'kodekloud_joy'@'%'; FLUSH PRIVILEGES;"
```

---

## 💡 Common LAMP Issues & Fixes

| Issue | Cause | Fix |
|-------|-------|-----|
| PHP can't connect to DB | Firewall on stdb01 blocking 3306 | Allow port 3306 from app server range |
| Apache fails on port 8089 | Port conflict or SELinux | `semanage port -a -t http_port_t -p tcp 8089` |
| DB user auth fails | Password or host mismatch | Ensure `'%'` wildcard host is used |

---

## 🔧 Troubleshooting

### **Error: php-mysqlnd not found**
```bash
yum install -y php-pdo php-pdo_mysql
```

### **Error: SQLSTATE[HY000] [2002] Connection refused**
- Ensure `mariadb` is running on `stdb01`.
- Check firewall is not blocking port 3306.

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Deployed the full **LAMP stack** across the Nautilus application tier.
- Configured Apache to listen on port **8089** on all app servers.
- Provisioned database `kodekloud_db9` and user `kodekloud_joy` with full privileges.
- Validated database connectivity from the app tier through the LBR.

**🔒 Configuration Summary**:
- **Web Port**: 8089
- **DB**: kodekloud_db9
- **DB User**: kodekloud_joy@%
- **Stack**: Apache + PHP + MariaDB
