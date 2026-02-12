# 🔐 Task 14 - Setup Postfix and Dovecot Mail Server on stmail01

**📌 Task Description**

The **xFusionCorp Industries** team has planned to establish a centralized mail infrastructure within the **Stork Datacenter**. Following technical recommendations, the team has decided to deploy **Postfix** as the Mail Transfer Agent (MTA) and **Dovecot** as the IMAP/POP3 server. This setup will enable internal email communication across the Nautilus infrastructure. This task involves software installation, service configuration, and the creation of a secure mail user with a specialized directory structure.

**👉 Task Requirements**:
- **Target Server**: Stork DC Mail Server (`stmail01.stratos.xfusioncorp.com`)
- **MTA**: Postfix (Configured for internal domain)
- **IMAP/POP3**: Dovecot (Configured for Maildir support)
- **User Setup**:
  - Name: `john@stratos.xfusioncorp.com`
  - Password: `LQfKeWWxWD`
  - Mail Directory: `/home/john/Maildir`
- **Operation Objectives**:
  - Install Postfix and Dovecot packages
  - Configure Postfix network and domain parameters
  - Initialize the `john` user account and Maildir structure
  - Configure Dovecot authentication and storage locations
  - Enable and start both services
- Ensure services persist across reboots
- Validate configuration via systemctl and manual directory checks

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to Mail Server

```bash
ssh groot@stmail01.stratos.xfusioncorp.com
```

**Purpose**: Establish an administrative SSH session to the designated mail server.

**Expected Output**:
```
groot@stmail01's password: 
Welcome to Stork DC Mail Server
[groot@stmail01 ~]$
```

**Success Indicators**:
- ✅ Successfully authenticated as user `groot`
- ✅ Command prompt shows `stmail01` hostname
- ✅ Switched to root using `sudo -i`

---

## 🔹 Step 2: Install Required Packages

```bash
sudo yum install -y postfix dovecot
```

**Purpose**: Install the necessary mail handling and delivery software.

**Command Breakdown**:
- `yum install`: Red Hat package management installation
- `-y`: Non-interactive mode (auto-yes)
- `postfix`: The Mail Transfer Agent
- `dovecot`: The IMAP/POP3 server

**Success Indicators**:
- ✅ Both packages installed without dependency errors
- ✅ Configuration files available in `/etc/postfix/` and `/etc/dovecot/`

---

## 🔹 Step 3: Configure Postfix MTA

```bash
sudo vi /etc/postfix/main.cf
```

**Purpose**: Define the server's identity, listening interfaces, and mailbox format.

**Key Parameters to Update/Add**:
```ini
# Server Identities
myhostname = stmail01.stratos.xfusioncorp.com
mydomain = stratos.xfusioncorp.com
myorigin = $mydomain

# Network Settings
inet_interfaces = all
inet_protocols = ipv4

# Delivery Settings
mydestination = $myhostname, localhost.$mydomain, localhost, $mydomain
home_mailbox = Maildir/
```

**Vi Editor Workflow**:
1. Search for each parameter using `/` (e.g., `/myhostname`)
2. Press `i` to edit the line
3. Add `home_mailbox = Maildir/` at the end of the file if not found
4. Save and exit using `:wq`

**Success Indicators**:
- ✅ Configuration file updated correctly
- ✅ `Maildir/` (case-sensitive) set as the default delivery method

---

## 🔹 Step 4: Create Mail User Account

```bash
sudo useradd john
echo "john:LQfKeWWxWD" | sudo chpasswd
```

**Purpose**: Provision the primary user account for the email service with the specified credentials.

**Command Breakdown**:
- `useradd john`: Creates the system user and home directory
- `chpasswd`: Sets the password securely via standard input

**Success Indicators**:
- ✅ User `john` created (`id john` returns UID)
- ✅ Password updated successfully

---

## 🔹 Step 5: Initialize User Mailbox Structure

```bash
sudo su - john
mkdir -p Maildir/{new,cur,tmp}
exit
```

**Purpose**: Pre-create the standard `Maildir` structure to ensure the first incoming mail can be delivered correctly.

**Success Indicators**:
- ✅ Directory `/home/john/Maildir` exists
- ✅ Subdirectories `new`, `cur`, and `tmp` created

---

## 🔹 Step 6: Set Directory Permissions and Ownership

```bash
sudo chown -R john:john /home/john/Maildir
sudo chmod -R 700 /home/john/Maildir
```

**Purpose**: Ensure the mailbox is private and accessible only by the intended user.

**Success Indicators**:
- ✅ Ownership set to `john`
- ✅ Permissions set to `drwx------` (700)

---

## 🔹 Step 7: Enable and Start Postfix Service

```bash
sudo systemctl enable postfix
sudo systemctl start postfix
```

**Purpose**: Activate the mail transfer service and ensure boot persistence.

**Success Indicators**:
- ✅ Service status shows `active (running)`
- ✅ Postfix listening on port `25` (verified via `netstat -tulpn`)

---

## 🔹 Step 8: Configure Dovecot IMAP/POP3

### **A. Protocol Activation**
```bash
sudo vi /etc/dovecot/dovecot.conf
```
Update line:
```ini
protocols = imap pop3 lmtp
```

### **B. Mail Location Mapping**
```bash
sudo vi /etc/dovecot/conf.d/10-mail.conf
```
Update line:
```ini
mail_location = maildir:~/Maildir
```

### **C. Authentication Configuration**
```bash
sudo vi /etc/dovecot/conf.d/10-auth.conf
```
Update lines:
```ini
disable_plaintext_auth = no
auth_mechanisms = plain login
```

**Success Indicators**:
- ✅ Dovecot configured to use the same `Maildir` as Postfix
- ✅ Plaintext auth enabled for initial testing/internal use

---

## 🔹 Step 9: Enable and Start Dovecot Service

```bash
sudo systemctl enable dovecot
sudo systemctl start dovecot
```

**Purpose**: Activate the IMAP/POP3 retrieval service.

**Final Verification**:
```bash
sudo systemctl status postfix dovecot
```

**Success Indicators**:
- ✅ Both services show healthy active status
- ✅ Ready to receive and serve emails for `john@stratos.xfusioncorp.com`

---

## 📋 Quick Command Reference

Run on **stmail01** for rapid setup:

```bash
# Configuration Snippets
# Postfix main.cf (append)
echo "home_mailbox = Maildir/" | sudo tee -a /etc/postfix/main.cf

# User Setup
sudo useradd john && echo "john:LQfKeWWxWD" | sudo chpasswd
sudo -u john mkdir -p /home/john/Maildir/{new,cur,tmp}
sudo chmod 700 /home/john/Maildir

# Dovecot Config (Fast Edit)
sudo sed -i 's/#protocols = imap/protocols = imap pop3 lmtp/' /etc/dovecot/dovecot.conf
sudo sed -i 's|#mail_location = |mail_location = maildir:~/Maildir|' /etc/dovecot/conf.d/10-mail.conf

# Service Rollout
sudo systemctl enable --now postfix dovecot
```

---

## 💡 Common Mail Server Issues & Fixes

### **Issue 1: Mail Not Delivered to Maildir**
**Problem**: Emails appear in `/var/spool/mail/` (mbox format) instead of `/home/john/Maildir`.
**Fix**: Ensure `home_mailbox = Maildir/` is set in `main.cf` and **Postfix is restarted**. The trailing slash is required.

### **Issue 2: Connection Refused by Dovecot**
**Problem**: Client cannot connect via IMAP/POP3.
**Fix**: Verify `protocols` in `dovecot.conf` and ensure firewall allows ports 110/143.

### **Issue 3: "Permission Denied" in Logs**
**Problem**: Postfix/Dovecot cannot write to the user home directory.
**Fix**: Check ownership (`chown`) and ensure parent directory (`/home/john`) has executable permissions for the service.

---

## 🔧 Troubleshooting Failures

### **Error: Fatal: parameter home_mailbox: trailing slash...**
**Symptoms**: Postfix fails to start.
**Solution**: The `Maildir/` value MUST end with a `/` to signal the directory format to Postfix.

### **Error: Dovecot authentication failed**
**Symptoms**: User `john` cannot log in via IMAP.
**Solution**: Check `/var/log/maillog`. Ensure `disable_plaintext_auth = no` is set if NOT using SSL/TLS.

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
Syncing the storage format between Postfix (sending/storing) and Dovecot (retrieving) is the most common point of failure. If one uses `mbox` and the other uses `Maildir`, the mailbox will appear empty or delivery will fail.

**💡 Solution Approach**:
1. **Unified Storage**: Explicitly set `Maildir/` in both `main.cf` and `10-mail.conf`.
2. **Directory Initialization**: Pre-created the internal `new`, `cur`, `tmp` folders so the services don't fail on first-write permissions.
3. **Internal Domain Logic**: Configured `myorigin` and `mydestination` to handle the `stratos.xfusioncorp.com` domain locally.

---

## ⚠️ Important Production Notes

🔧 **DNS Records**: Ensure an `MX` record exists in your DNS for `stratos.xfusioncorp.com` pointing to this server.
🔐 **Security**: In production, ALWAYS enable SSL/TLS (STARTTLS) and disable plaintext authentication.
📊 **Logs**: Monitor `/var/log/maillog` for all mail-related errors and activities.
🛡️ **Firewall**: Ensure ports `25` (SMTP), `110` (POP3), and `143` (IMAP) are open.

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Successfully installed and configured **Postfix** as the MTA on `stmail01`.
- Configured **Dovecot** for IMAP/POP3 retrieval using the **Maildir** format.
- Provisioned the user account `john` with the required credentials.
- Verified service persistence and operational availability.

**🔍 Verification Completed**:
- **stmail01**: Postfix and Dovecot active | User `john` created | Mailbox structure ready.

**🔒 Configuration Summary**:
- **Domain**: stratos.xfusioncorp.com
- **Mail Format**: Maildir
- **Services**: Postfix, Dovecot
- **User**: john
