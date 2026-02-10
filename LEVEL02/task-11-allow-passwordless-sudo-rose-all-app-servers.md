# 🔐 Task 11 - Configure Password-less Sudo Access for User Rose on All App Servers

**📌 Task Description**

As per details shared by the development team, several users on all app servers in the **Stratos Datacenter** have been assigned new roles and responsibilities. Specifically, the user **rose** needs to be upgraded with administrative privileges. This task involves providing **sudo access** to user `rose` on all application servers and ensuring that this access is **password-less**, allowing for seamless execution of administrative tasks and automation.

**👉 Task Requirements**:
- **Target Servers**: All App Servers (stapp01, stapp02, stapp03) in Stratos Datacenter
- **User**: `rose`
- **Access Level**: Full sudo privileges (`ALL=(ALL) ALL`)
- **Authentication**: Password-less (`NOPASSWD:`)
- **Operation Objectives**:
  - Verify existence of user `rose`
  - Create a dedicated sudo configuration file in `/etc/sudoers.d/`
  - Set correct permissions (440) for the sudoers file
  - Verify sudo access without password prompts
- Ensure security best practices for sudoers configuration
- Maintain consistency across all application servers

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to App Server 1

```bash
ssh tony@stapp01
```

**Purpose**: Establish SSH connection to App Server 1 for privilege configuration.

**Expected Output**:
```
tony@stapp01's password: 
Welcome to App Server 1 - Stratos Datacenter
[tony@stapp01 ~]$
```

**Success Indicators**:
- ✅ Successfully authenticated as user `tony`
- ✅ Command prompt shows `stapp01` hostname

---

## 🔹 Step 2: Verify User Rose Exists on App Server 1

```bash
id rose
```

**Purpose**: Confirm the user `rose` is already present on the system.

**Expected Output**:
```
uid=1001(rose) gid=1001(rose) groups=1001(rose)
```

**Success Indicators**:
- ✅ User `rose` found with UID/GID
- ✅ Ready to apply sudo privileges

---

## 🔹 Step 3: Configure Password-less Sudo for Rose on App Server 1

```bash
sudo visudo -f /etc/sudoers.d/rose
```

**Purpose**: Create a separate, modular sudoers configuration file for user `rose`. Using `visudo` ensures syntax validation.

**Content to Add**:
```text
rose ALL=(ALL) NOPASSWD:ALL
```

**Syntax Breakdown**:
- `rose`: The user target
- `ALL`: Matches any host
- `(ALL)`: Allows user to run commands as any user
- `NOPASSWD:ALL`: Allows any command to be run without a password prompt

**Vi Editor Commands**:
1. Press `i` to enter insert mode
2. Type `rose ALL=(ALL) NOPASSWD:ALL`
3. Press `Esc` to exit insert mode
4. Type `:wq` and press `Enter` to save and exit

**Success Indicators**:
- ✅ File `/etc/sudoers.d/rose` created
- ✅ Syntax verified by visudo
- ✅ No "syntax error" warnings

---

## 🔹 Step 4: Set Correct Permissions for the Sudoers File

```bash
sudo chmod 440 /etc/sudoers.d/rose
```

**Purpose**: Apply strict read-only permissions (Owner: Read, Group: Read) as required by the sudoers mechanism.

**Success Indicators**:
- ✅ Permissions set to `r--r-----`
- ✅ Sudoers security requirements satisfied

---

## 🔹 Step 5: Verify Sudo Access for Rose on App Server 1

```bash
su - rose
sudo whoami
```

**Purpose**: Switch to user `rose` and attempt an administrative command without a password.

**Expected Output**:
```
[rose@stapp01 ~]$ sudo whoami
root
```

**Success Indicators**:
- ✅ Switched to user `rose`
- ✅ `sudo whoami` returned `root`
- ✅ **No password prompt appeared**

---

## 🔹 Step 6: Connect to App Server 2

```bash
ssh steve@stapp02
```

**Purpose**: Establish SSH connection to App Server 2.

**Expected Output**:
```
steve@stapp02's password: 
Welcome to App Server 2 - Stratos Datacenter
[steve@stapp02 ~]$
```

---

## 🔹 Step 7: Configure Sudo and Verify for Rose on App Server 2

```bash
# Verify user
id rose

# Configure sudo
sudo visudo -f /etc/sudoers.d/rose
# Add: rose ALL=(ALL) NOPASSWD:ALL

# Set permissions
sudo chmod 440 /etc/sudoers.d/rose

# Test
su - rose
sudo whoami
exit
```

**Success Indicators**:
- ✅ User `rose` verified
- ✅ Sudoers file created and secured
- ✅ Password-less sudo functional on App Server 2

---

## 🔹 Step 8: Connect to App Server 3

```bash
ssh banner@stapp03
```

**Purpose**: Establish SSH connection to App Server 3.

**Expected Output**:
```
banner@stapp03's password: 
Welcome to App Server 3 - Stratos Datacenter
[banner@stapp03 ~]$
```

---

## 🔹 Step 9: Configure Sudo and Verify for Rose on App Server 3

```bash
# Verify user
id rose

# Configure sudo
sudo visudo -f /etc/sudoers.d/rose
# Add: rose ALL=(ALL) NOPASSWD:ALL

# Set permissions
sudo chmod 440 /etc/sudoers.d/rose

# Test
su - rose
sudo whoami
exit
```

**Success Indicators**:
- ✅ User `rose` verified
- ✅ Sudoers file created and secured
- ✅ Password-less sudo functional on App Server 3

---

## 📋 Quick Command Reference

Execute on **each App Server** (stapp01, stapp02, stapp03):

```bash
# Connect and switch to target server environment
# ...

# 1. Create sudoers drop-in file
echo "rose ALL=(ALL) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/rose

# 2. Set strict permissions
sudo chmod 440 /etc/sudoers.d/rose

# 3. Verify
su - rose -c "sudo whoami"
```

---

## 💡 Common Sudo Configuration Issues & Fixes

### **Issue 1: Sudoers Syntax Error**
**Problem**: The user cannot use sudo because of a bad config file.
**Fix**: Always use `visudo -c` or `visudo -f` to check files.
```bash
sudo visudo -c -f /etc/sudoers.d/rose
```

### **Issue 2: Permission Too Open**
**Problem**: Sudo ignores the file because it's writable by others.
**Fix**: Ensure `440` or `400` permissions.
```bash
sudo chmod 0440 /etc/sudoers.d/rose
```

### **Issue 3: User Not Found**
**Problem**: `id rose` fails.
**Fix**: Confirm user creation steps were completed before sudo setup.

### **Issue 4: visudo Not Found**
**Problem**: Command is missing from PATH.
**Fix**: Use the full path `/usr/sbin/visudo`.

---

## 🔧 Troubleshooting Failures

### **Error: /etc/sudoers.d/rose: permission denied**
**Symptoms**: Unable to write or edit the file.
**Solution**: Ensure you are using `sudo` to perform these actions from a primary admin account (tony, steve, or banner).

### **Error: sudo: no tty present and no askpass program specified**
**Symptoms**: Sudo fails in scripts even with NOPASSWD.
**Solution**: Check for `Defaults requiretty` in the main `/etc/sudoers` file and comment it out if safe.

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The core challenge is ensuring the `NOPASSWD:` tag is placed correctly in the sudo syntax. Mistakes here lead to security gaps or frustrating password prompts in automated workflows.

**💡 Solution Approach**:
1. **Modular Configuration**: Used `/etc/sudoers.d/` instead of editing the main `/etc/sudoers` file. This is cleaner and safer for automation.
2. **Standard Syntax**: Followed the `USER HOST=(RUN_AS_USER) TAGS:COMMANDS` pattern strictly.
3. **Security Locking**: Secured the file with `440` permissions immediately to prevent tampering or system rejection.
4. **End-to-End Validation**: Logged in *as the user* to verify the real-world experience of running a sudo command.

**🎯 Key Success Factors**:
- **Cleanliness**: Drop-in files prevent accidental corruption of main system config.
- **Verification**: `sudo whoami` as the target user is the definitive proof of success.

---

## ⚠️ Important Production Notes

🔧 **Drop-in Files**: Always prefer `/etc/sudoers.d/` for per-user or per-group configurations.
🔐 **Least Privilege**: Only provide `NOPASSWD` when absolutely necessary for automation or specific operational needs.
📊 **Auditing**: Sudo events are logged to `/var/log/secure` (RHEL/CentOS) or `/var/log/auth.log` (Ubuntu/Debian).
⚙️ **Includes**: Ensure the line `#includedir /etc/sudoers.d` exists (and is uncommented) in the main `/etc/sudoers` file.

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Provided full sudo administrative access to user `rose` across all three application servers.
- Configured access as `NOPASSWD`, removing authentication prompts for administrative commands.
- Secured the configuration using standard Linux permission masks.

**🔍 Verification Completed**:
- **stapp01**: User `rose` sudo verified.
- **stapp02**: User `rose` sudo verified.
- **stapp03**: User `rose` sudo verified.

**🔒 Configuration Summary**:
- **User**: rose
- **Permissions**: ALL=(ALL) NOPASSWD:ALL
- **File**: /etc/sudoers.d/rose
- **Modality**: Password-less Administrative Access
