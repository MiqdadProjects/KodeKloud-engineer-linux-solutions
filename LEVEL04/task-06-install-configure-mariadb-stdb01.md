# 🔐 Task 06 - Install and Configure MariaDB on stdb01

**📌 Task Description**

The **Nautilus** infrastructure team requires a new database environment on the **Database Server (stdb01)** in the **Stratos Datacenter**. This task involves the installation and configuration of a **MariaDB** server, followed by the provisioning of a dedicated database and a restricted user with full access grants. This setup forms the backend data layer for future application deployments.

**👉 Task Requirements**:
- **Target Server**: Database Server (`stdb01`)
- **Database Engine**: MariaDB
- **Database**: `kodekloud_db6`
- **User**: `kodekloud_rin` (Password: `dCV3szSGNA`, Host: `%`)
- **Privileges**: `ALL PRIVILEGES ON kodekloud_db6.*`
- **Operation Objectives**:
  - Install the `mariadb-server` package.
  - Start and enable MariaDB for boot persistence.
  - Create the required database using `IF NOT EXISTS` for idempotency.
  - Create the user with a wildcard host for maximum connectivity.
  - Grant all privileges and flush the grant tables.

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to Database Server

```bash
ssh peter@stdb01
sudo su -
```

---

## 🔹 Step 2: Install MariaDB Server

```bash
yum install -y mariadb-server
```

---

## 🔹 Step 3: Start and Enable Service

```bash
systemctl start mariadb
systemctl enable mariadb
systemctl status mariadb
```

**Success Indicators**:
- ✅ Service status: `active (running)`.

---

## 🔹 Step 4: Provision Database and User

```bash
mysql -u root <<EOF
-- Create database (idempotent)
CREATE DATABASE IF NOT EXISTS kodekloud_db6;

-- Create user with wildcard host (idempotent)
CREATE USER IF NOT EXISTS 'kodekloud_rin'@'%' IDENTIFIED BY 'dCV3szSGNA';

-- Grant all privileges
GRANT ALL PRIVILEGES ON kodekloud_db6.* TO 'kodekloud_rin'@'%';

-- Apply changes
FLUSH PRIVILEGES;
EOF
```

**Purpose**: Run all provisioning steps in a single heredoc to minimize errors and ensure atomicity.

---

## 🔹 Step 5: Verification

```bash
# Verify database
mysql -u root -e "SHOW DATABASES;" | grep kodekloud_db6

# Verify user
mysql -u root -e "SELECT user, host FROM mysql.user WHERE user='kodekloud_rin';"

# Test user login
mysql -u kodekloud_rin -pdCV3szSGNA -h 127.0.0.1 -e "SHOW DATABASES;"
```

**Success Indicators**:
- ✅ `kodekloud_db6` listed in databases.
- ✅ User `kodekloud_rin` exists with host `%`.
- ✅ User can authenticate and see the granted database.

---

## 📋 Quick Command Reference

```bash
# Full deployment one-liner
sudo yum install -y mariadb-server && sudo systemctl enable --now mariadb

# Database + User provisioning
mysql -u root -e "
  CREATE DATABASE IF NOT EXISTS kodekloud_db6;
  CREATE USER IF NOT EXISTS 'kodekloud_rin'@'%' IDENTIFIED BY 'dCV3szSGNA';
  GRANT ALL PRIVILEGES ON kodekloud_db6.* TO 'kodekloud_rin'@'%';
  FLUSH PRIVILEGES;"
```

---

## 💡 Common MariaDB Issues & Fixes

### **Issue 1: Access Denied for Root**
**Problem**: `mysql -u root` fails.
**Fix**: Run as system root (`sudo mysql`) or use `sudo mysql_secure_installation` to set a root password first.

### **Issue 2: User Can't Connect Remotely**
**Problem**: The firewall blocks port 3306.
**Fix**: `firewall-cmd --permanent --add-port=3306/tcp && firewall-cmd --reload`

### **Issue 3: Privileges Not Applied**
**Problem**: User lacks access despite grants.
**Fix**: Always run `FLUSH PRIVILEGES;` after any grant changes.

---

## 🔧 Troubleshooting Failures

### **Error: Can't connect to local MySQL server through socket '/var/lib/mysql/mysql.sock'**
**Symptoms**: `mysql` command fails immediately.
**Solution**: MariaDB is not running. Execute `systemctl start mariadb`.

### **Error: ERROR 1396: Operation CREATE USER failed**
**Symptoms**: User creation fails.
**Solution**: User might already exist. Use `CREATE USER IF NOT EXISTS` or drop and recreate.

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Installed and activated **MariaDB** on `stdb01`.
- Created the **kodekloud_db6** database with idempotent SQL.
- Provisioned user **kodekloud_rin** with wildcard host `%`.
- Granted **ALL PRIVILEGES** on the target database.

**🔒 Configuration Summary**:
- **Engine**: MariaDB
- **Database**: kodekloud_db6
- **User**: kodekloud_rin@%
- **Access**: Full
