# 🔐 Task 02 - Configure SFTP Server for User Kareem on App Server 2

**📌 Task Description**

The **Nautilus** development team requires secure file transfer access to **App Server 2 (stapp02)** in the **Stratos Datacenter**. To maintain system security, the administrative team has decided to implement a restricted **SFTP-only** environment. This task involves creating a dedicated user account, configuring SSH to enforce SFTP protocols while disabling interactive shell access, and setting up a **Chroot** jail to ensure user isolation within their home directory.

**👉 Task Requirements**:
- **Target Server**: App Server 2 (`stapp02`)
- **User Account**: 
  - Name: `kareem`
  - Password: `Rc5C9EyvbU`
  - Group: `ftp` (Pre-existing)
- **Security Policies**:
  - Disable interactive shell access (`/sbin/nologin`).
  - Enable password authentication for this specific user.
  - Restrict access to SFTP only (No SSH, Port Forwarding, or Tunneling).
  - Implement a Chroot environment for home directory isolation.
- **Operation Objectives**:
  - Create and configure the user account.
  - Modify `/etc/ssh/sshd_config` with specific Match blocks.
  - Set strict ownership for Chroot compatibility (Root-owned home).
  - Create a writable `upload` directory for the user.
  - Verify connectivity from the Jump Host.

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to App Server 2

```bash
ssh steve@stapp02
```

**Purpose**: Establish an administrative SSH session to prepare the SFTP environment.

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

## 🔹 Step 2: Create SFTP User Account

```bash
# Create user with no shell access and primary group 'ftp'
sudo useradd -g ftp -s /sbin/nologin kareem

# Set the password
echo "kareem:Rc5C9EyvbU" | sudo chpasswd
```

**Purpose**: Provision the account with restricted login capabilities to prevent standard SSH sessions.

**Success Indicators**:
- ✅ User `kareem` created with UID/GID.
- ✅ Shell set to `/sbin/nologin`.

---

## 🔹 Step 3: Configure SSH for Restricted SFTP

```bash
sudo vi /etc/ssh/sshd_config
```

**Purpose**: Modify the SSH daemon configuration to enforce SFTP-only rules for the specific user.

**Configuration Block (Append to end of file)**:
```ssh
Match User kareem
    ForceCommand internal-sftp
    PasswordAuthentication yes
    ChrootDirectory /home/kareem
    PermitTunnel no
    AllowTcpForwarding no
    X11Forwarding no
```

**Success Indicators**:
- ✅ Match block defined specifically for `kareem`.
- ✅ `internal-sftp` subsystem enforced.

---

## 🔹 Step 4: Configure Chroot Directory Permissions

```bash
# SSH Chroot requires the jail directory to be owned by root
sudo chown root:root /home/kareem
sudo chmod 755 /home/kareem
```

**Purpose**: Enforce the security requirement that the Chroot directory must be owned by the root user and not writable by any other user group to prevent jailbreaks.

**Success Indicators**:
- ✅ `/home/kareem` ownership set to `root:root`.

---

## 🔹 Step 5: Create Writable Upload Directory

```bash
sudo mkdir /home/kareem/upload
sudo chown kareem:ftp /home/kareem/upload
```

**Purpose**: Since the home directory is owned by root and read-only for the user, a sub-directory is required where the user can actually upload files.

**Success Indicators**:
- ✅ Directory `/home/kareem/upload` created.
- ✅ User `kareem` has write access to this sub-directory.

---

## 🔹 Step 6: Restart SSH Service

```bash
sudo systemctl restart sshd
```

**Purpose**: Apply the new configuration changes to the SSH daemon.

---

## 🔹 Step 7: Verification from Jump Host

```bash
# Attempt to connect via SFTP
sftp kareem@stapp02
```

**Verification Steps**:
1. Enter password: `Rc5C9EyvbU`
2. Run `ls` to verify access to the `upload` folder.
3. Test SSH access: `ssh kareem@stapp02` (Should fail/disconnect immediately).

**Success Indicators**:
- ✅ SFTP connection established successfully.
- ✅ Standard SSH connection rejected.
- ✅ User trapped within `/home/kareem`.

---

## 📋 Quick Command Reference

SFTP Setup for **stapp02**:

```bash
# User Provisioning
sudo useradd -g ftp -s /sbin/nologin kareem
echo "kareem:Rc5C9EyvbU" | sudo chpasswd

# Directory Hardening
sudo chown root:root /home/kareem
sudo mkdir /home/kareem/upload
sudo chown kareem:ftp /home/kareem/upload

# SSH Config Update
cat <<EOF | sudo tee -a /etc/ssh/sshd_config
Match User kareem
    ForceCommand internal-sftp
    ChrootDirectory %h
    PasswordAuthentication yes
    AllowTcpForwarding no
EOF

sudo systemctl restart sshd
```

---

## 💡 Common SFTP Issues & Fixes

### **Issue 1: Connection Closed Immediately**
**Problem**: The Chroot directory has incorrect permissions (owned by user instead of root).
**Fix**: Ensure `/home/kareem` is owned by `root:root`.

### **Issue 2: Subsystem Failure**
**Problem**: `internal-sftp` is not configured correctly in the main `sshd_config`.
**Fix**: Ensure the line `Subsystem sftp internal-sftp` exists in the global section of the config.

### **Issue 3: Password Authentication Failed**
**Problem**: Global `PasswordAuthentication` is set to `no`.
**Fix**: The `Match User` block correctly overrides this for `kareem`, but ensure it is typed correctly.

---

## 🔧 Troubleshooting Failures

### **Error: fatal: bad ownership or modes for chroot directory**
**Symptoms**: User is disconnected as soon as they provide a password.
**Solution**: Run `ls -ld /home/kareem`. It MUST be `root:root` with `755` permissions.

### **Error: Received message too long 1416128883**
**Symptoms**: Error message during SFTP login.
**Solution**: This often happens when the user's `.bashrc` or shell profile generates text output. Since we use `internal-sftp` and `nologin`, this should be avoided.

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The paradox of a Chroot environment is that the user must be isolated in a directory they cannot write to (the root-owned home). This often leads to user confusion when they cannot upload files to their apparent "root" directory in SFTP.

**💡 Solution Approach**:
1. **Root Stewardship**: Strictly enforced `root:root` ownership on `/home/kareem` to satisfy the SSH daemon's security checker.
2. **Functional Delegation**: Created a secondary `upload` directory with user-level ownership, providing a clear and functional workspace within the jail.
3. **Command Enforcement**: Used `ForceCommand internal-sftp` within the `Match` block to ensure that even if a shell were somehow accessible, the process would immediately switch to the SFTP handler.

---

## ⚠️ Important Production Notes

🔧 **Monitoring**: Monitor `/var/log/secure` for failed SFTP login attempts.
🔐 **Security**: Disable `X11Forwarding` and `AllowTcpForwarding` in the Match block for maximum lockdown.
📊 **Quota**: In a multi-user environment, consider implementing XFS or EXT4 quotas on the `upload` directory to prevent disk exhaustion.
🛡️ **Audit**: Regularly audit the `/etc/ssh/sshd_config` file to ensure the `Match` blocks are correctly positioned (they must always be at the end of the file).

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Created the restricted account `kareem` in the `ftp` group.
- Implemented **SFTP-only** access using `internal-sftp`.
- Secured the environment via **Chroot** home directory isolation.
- Verified password-based authentication and service persistence.

**🔍 Verification Completed**:
- **stapp02**: SSH service active | User `kareem` restricted.
- **Connectivity**: SFTP access verified from Jump Host.

**🔒 Configuration Summary**:
- **Format**: SFTP-only
- **Chroot Path**: /home/kareem
- **Auth**: Password
- **Shell**: /sbin/nologin
