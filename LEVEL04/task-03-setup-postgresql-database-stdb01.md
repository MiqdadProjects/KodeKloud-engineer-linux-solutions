# 🔐 Task 03 - Setup PostgreSQL Database Server on stdb01

**📌 Task Description**

The **Nautilus** application development team is preparing to deploy a new application that requires a **PostgreSQL** backend. The database server is already installed on the **Nautilus database server (stdb01)**. As a prerequisite, several administrative tasks must be performed, including creating a dedicated database user, initializing a new database, and configuring access privileges. Security and operational continuity are paramount; specifically, the PostgreSQL service must remain active throughout the configuration without being restarted.

**👉 Task Requirements**:
- **Target Server**: Nautilus Database Server (`stdb01`)
- **Database Engine**: PostgreSQL
- **Database User**: `kodekloud_sam`
- **User Password**: `YchZHRcLkL`
- **Database Name**: `kodekloud_db9`
- **Operation Objectives**:
  - Connect to the database server using administrative credentials.
  - Create the application-specific database user with a secure password.
  - Create the target database.
  - Grant full administrative privileges to the new user for the specific database.
  - Verify connectivity using the new credentials.
- **Constraints**: 🚨 **Do not restart the PostgreSQL server service.**

**💡 Note**: Use the `postgres` system user for administrative access to the PostgreSQL shell (`psql`).

---

## 🔹 Step 1: Connect to the Database Server

```bash
ssh peter@stdb01
sudo su -
```

**Purpose**: Establish a secure shell session on the database server and elevate privileges to perform system-level operations.

---

## 🔹 Step 2: Access PostgreSQL Administrative Shell

Switch to the default PostgreSQL administrative user and enter the CLI.

```bash
# Switch to postgres user
sudo -i -u postgres

# Enter psql shell
psql
```

**Purpose**: Gain access to the PostgreSQL interactive terminal (`psql`) as the superuser to manage databases and roles.

---

## 🔹 Step 3: Create User and Database

Execute the following SQL commands within the `psql` shell.

```sql
-- Create the application user with password
CREATE USER kodekloud_sam WITH PASSWORD 'YchZHRcLkL';

-- Create the application database
CREATE DATABASE kodekloud_db9;

-- Grant all privileges on the database to the user
GRANT ALL PRIVILEGES ON DATABASE kodekloud_db9 TO kodekloud_sam;
```

**Success Indicators**:
- ✅ `CREATE ROLE` message received after user creation.
- ✅ `CREATE DATABASE` message received after database creation.
- ✅ `GRANT` message received after permission assignment.

---

## 🔹 Step 4: Exit and Verify Connectivity

Exit the administrative shell and test the new credentials locally.

```bash
# Exit psql
\q

# Test connection as the new user to the new database
psql -U kodekloud_sam -d kodekloud_db9 -h localhost
```

**Expected Connection Prompt**:
`kodekloud_db9=>`

**Purpose**: Confirm that the user can successfully authenticate and access the target database without superuser intervention.

---

## 📋 Quick Command Reference

```sql
-- Administrative Setup
sudo -i -u postgres psql -c "CREATE USER kodekloud_sam WITH PASSWORD 'YchZHRcLkL';"
sudo -i -u postgres psql -c "CREATE DATABASE kodekloud_db9;"
sudo -i -u postgres psql -c "GRANT ALL PRIVILEGES ON DATABASE kodekloud_db9 TO kodekloud_sam;"

-- Verification
psql -U kodekloud_sam -d kodekloud_db9 -h localhost
```

---

## 💡 Common PostgreSQL Issues & Fixes

### **Issue 1: Ident Authentication Failure**
**Problem**: Connection fails with "Peer authentication failed for user" even with the correct password.
**Fix**: Update `pg_hba.conf` to use `md5` or `scram-sha-256` for local TCP connections instead of `peer/ident`. However, in this task, specifying `-h localhost` forces a TCP connection which usually defaults to password auth.

### **Issue 2: Permission Denied on Schema**
**Problem**: User can connect but cannot create tables.
**Fix**: Ensure `GRANT ALL ON SCHEMA public` is also applied if the user needs to create objects within the default schema.
```sql
GRANT ALL ON SCHEMA public TO kodekloud_sam;
```

---

## 🔧 Troubleshooting Failures

### **Error: psql: FATAL: role "kodekloud_sam" does not exist**
**Symptoms**: Authentication fails during verification.
**Solution**: Re-run the `CREATE USER` command. Ensure there are no typos in the username and that the command ended with a semicolon.

### **Error: Connection refused (Is the server running?)**
**Symptoms**: Cannot enter `psql`.
**Solution**: Check the service status: `systemctl status postgresql`. Since you cannot restart, ensure it started correctly during boot or by other automated processes.

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The requirement to **not restart the service** is a critical operational constraint. Standard practice for changing configuration files like `postgresql.conf` or `pg_hba.conf` usually involves a reload or restart.

**💡 Solution Approach**:
1. **Dynamic Management**: All requested changes (User/DB/Grant) are performed via SQL DDL commands, which are immediate and do not require service interruptions.
2. **Local Loopback Verification**: Using `-h localhost` ensures the connection test uses the network stack rather than local Unix sockets, providing a more realistic test of application connectivity.
3. **Privilege Granularity**: Granting `ALL PRIVILEGES` specifically on the new database ensures the user has full control over their application data while adhering to the principle of least privilege regarding other databases on the server.

---

## ⚠️ Important Production Notes

🔧 **Password Security**: Never pass passwords in command line arguments in production history; use `.pgpass` files or environment variables.
🔐 **External Access**: By default, PostgreSQL only listens on `localhost`. For application servers to connect, update `listen_addresses` in `postgresql.conf`.
📊 **Backup**: After creating the database, ensure it is added to the `pg_dump` backup schedule.
🛡️ **Extension Management**: If the app requires extensions (like `postgis`), they must be created by a superuser within the database.

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Created user **kodekloud_sam** with secure authentication.
- Created database **kodekloud_db9**.
- Assigned **full administrative privileges** to the user for the database.
- Verified connectivity without service interruption.

**🔍 Verification Completed**:
- **stdb01**: Psql handshake successful via localhost.
- **Service Uptime**: Maintain uninterrupted service throughout.

**🔒 Configuration Summary**:
- **User**: kodekloud_sam
- **DB**: kodekloud_db9
- **Host**: localhost
- **Status**: Operational
