# 🔐 Task 04 - Automate MariaDB Operations with Bash Script on stdb01

**📌 Task Description**

The **Nautilus DevOps** team requires a robust automation solution for managing **MariaDB** database lifecycle operations on the **Database Server (stdb01)** in the **Stratos Datacenter**. Rather than manually executing database commands, the team needs a unified, idempotent **Bash script** that handles database creation, user provisioning, data import (if needed), and final backup—all in a single execution. This approach reduces human error and enables consistent, repeatable deployments.

**👉 Task Requirements**:
- **Target Server**: Database Server (`stdb01`)
- **Script Path**: `/opt/scripts/database.sh`
- **Database Name**: `kodekloud_db01`
- **User**: `kodekloud_roy` (Password: `asdfgdsd`, Host: `%`)
- **Backup Directory**: `/opt/db_backups/`
- **Import Source**: `/opt/db_backups/db.sql`
- **Dump Output**: `/opt/db_backups/kodekloud_db01.sql`
- **Operation Objectives**:
  - Check for database existence; create if absent.
  - Provision user with wildcard host and full privileges.
  - Inspect database for existing tables; import dump if empty.
  - Always produce a fresh MySQL dump at the end.
- Script must be executable and idempotent.
- Output messages must match exact required strings.

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to Database Server

```bash
ssh peter@stdb01
sudo su -
```

**Purpose**: Establish an administrative session on the database server.

---

## 🔹 Step 2: Create the Automation Script

```bash
mkdir -p /opt/scripts
vi /opt/scripts/database.sh
```

**Purpose**: Create the script file in the designated operations directory.

**Script Content**:

```bash
#!/bin/bash

DB_NAME="kodekloud_db01"
DB_USER="kodekloud_roy"
DB_PASS="asdfgdsd"
BACKUP_DIR="/opt/db_backups"
IMPORT_FILE="$BACKUP_DIR/db.sql"
DUMP_FILE="$BACKUP_DIR/${DB_NAME}.sql"

# ─── 1. CREATE DATABASE ──────────────────────────────────────────────────────
DB_EXISTS=$(mysql -u root -sse \
  "SELECT SCHEMA_NAME FROM INFORMATION_SCHEMA.SCHEMATA \
   WHERE SCHEMA_NAME='$DB_NAME';")

if [ "$DB_EXISTS" == "$DB_NAME" ]; then
    echo "Database already exists"
else
    mysql -u root -e "CREATE DATABASE $DB_NAME;"
    echo "Database $DB_NAME has been created"
fi

# ─── 2. CREATE USER & GRANT PRIVILEGES ────────────────────────────────────────
USER_EXISTS=$(mysql -u root -sse \
  "SELECT EXISTS(SELECT 1 FROM mysql.user \
   WHERE user='$DB_USER' AND host='%');")

if [ "$USER_EXISTS" -eq 0 ]; then
    mysql -u root -e \
      "CREATE USER '$DB_USER'@'%' IDENTIFIED BY '$DB_PASS';"
    echo "User $DB_USER has been created"
fi

mysql -u root -e "GRANT ALL PRIVILEGES ON $DB_NAME.* TO '$DB_USER'@'%';"
mysql -u root -e "FLUSH PRIVILEGES;"

# ─── 3. IMPORT DUMP IF DATABASE IS EMPTY ────────────────────────────────────
TABLE_COUNT=$(mysql -u root -D "$DB_NAME" -sN -e "SHOW TABLES;" | wc -l)

if [ "$TABLE_COUNT" -gt 0 ]; then
    echo "database is not empty"
else
    if [ -f "$IMPORT_FILE" ]; then
        mysql -u root "$DB_NAME" < "$IMPORT_FILE"
        echo "imported database dump into $DB_NAME database"
    fi
fi

# ─── 4. TAKE MYSQL DUMP ──────────────────────────────────────────────────────
mkdir -p "$BACKUP_DIR"
mysqldump -u root "$DB_NAME" > "$DUMP_FILE"
```

---

## 🔹 Step 3: Set Permissions and Execute

```bash
# Make the script executable
chmod +x /opt/scripts/database.sh

# Run the script
/opt/scripts/database.sh
```

**Success Indicators**:
- ✅ Script runs without errors.
- ✅ Output matches required message strings.
- ✅ `/opt/db_backups/kodekloud_db01.sql` exists.

---

## 🔹 Step 4: Verification

```bash
# Confirm database and user
mysql -u root -e "SHOW DATABASES;" | grep kodekloud_db01
mysql -u root -e "SELECT user, host FROM mysql.user WHERE user='kodekloud_roy';"

# Confirm dump was created
ls -lh /opt/db_backups/
```

---

## 📋 Quick Command Reference

```bash
# Create and deploy script
mkdir -p /opt/scripts /opt/db_backups

# Run a quick status check after execution
mysql -u kodekloud_roy -pasdfgdsd -h 127.0.0.1 kodekloud_db01 -e "SHOW TABLES;"
ls -lh /opt/db_backups/kodekloud_db01.sql
```

---

## 💡 Script Logic Explanation

| Step | Condition | Action |
|------|-----------|--------|
| DB Check | Exists | Print "Database already exists" |
| DB Check | Does NOT exist | Create DB + Print "Database ... has been created" |
| User Check | Exists | Skip creation |
| User Check | Does NOT exist | Create user with `%` wildcard host |
| Table Check | DB has tables | Print "database is not empty" |
| Table Check | DB is empty | Import `db.sql` + Print "imported database dump..." |
| Dump | Always | Write `kodekloud_db01.sql` to `/opt/db_backups/` |

---

## 💡 Common Script Issues & Fixes

### **Issue 1: Permission Denied on Script**
**Problem**: Script is not executable.
**Fix**: `chmod +x /opt/scripts/database.sh`

### **Issue 2: Incorrect Message String**
**Problem**: The task fails validation because of slightly different wording.
**Fix**: Match messages *exactly* as required (case-sensitive).

### **Issue 3: Root Access Required**
**Problem**: `mysql -u root` fails asking for password.
**Fix**: Ensure MariaDB root user has no password set, or use `mysql -u root -p` with the correct one. Most lab images allow passwordless root access.

---

## 🔧 Troubleshooting Failures

### **Error: ERROR 1044: Access denied for user 'root'@'localhost'**
**Symptoms**: Root cannot create databases.
**Solution**: Connect using `sudo mysql` or ensure you are running as root user.

### **Error: mysqldump: Got error: 1049: Unknown database**
**Symptoms**: Dump fails.
**Solution**: Ensure the database was successfully created before the dump step.

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The script must be fully **idempotent**—it must handle both the "first-run" (empty environment) and "re-run" (database and user already exist) scenarios without errors or duplicate entries.

**💡 Solution Approach**:
1. **Query Before Action**: Used `SELECT` queries to check for the database and user before attempting to `CREATE`, preventing duplicate entity errors.
2. **Wildcard Host**: Used `'%'` in all user operations to match the task requirement of granting access from any host.
3. **Table Counting**: Used `SHOW TABLES | wc -l` to determine if the database was populated, driving the conditional import logic.

---

## ⚠️ Important Production Notes

🔧 **Security**: Avoid embedding plaintext passwords directly in scripts. Use a credentials file (e.g., `~/.my.cnf`) with restricted permissions in production.
🔐 **Idempotency**: The `IF NOT EXISTS` pattern in SQL prevents duplication errors on re-runs.
📊 **Logging**: Add `exec >> /var/log/database_script.log 2>&1` to the top of the script for automated log capture.
🛡️ **Backup Integrity**: Verify dump files using `mysqlcheck` or by importing to a test database.

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Wrote a fully idempotent Bash script for MariaDB lifecycle automation.
- Implemented conditional database and user creation with appropriate messaging.
- Automated data import when the database is found empty.
- Produced a final `mysqldump` backup to the `/opt/db_backups/` directory.
- Script is executable and verified on `stdb01`.

**🔍 Verification Completed**:
- **stdb01**: Script ran successfully | DB + User created | Dump file confirmed.

**🔒 Configuration Summary**:
- **DB**: kodekloud_db01
- **User**: kodekloud_roy@%
- **Dump**: /opt/db_backups/kodekloud_db01.sql
- **Script**: /opt/scripts/database.sh
