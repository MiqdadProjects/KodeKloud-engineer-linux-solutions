# 🔐 Task 5 - Setup Password-less SSH from Jump Host to All App Servers

**📌 Task Description**

The system admins team of **xFusionCorp Industries** has set up some scripts on jump host that run on regular intervals and perform operations on all app servers in Stratos Datacenter. To make these scripts work properly, we need to ensure the **thor user** on jump host has password-less SSH access to all app servers through their respective sudo users. This is accomplished using SSH key-based authentication, which provides secure, automated access without password prompts.

**👉 Task Requirements**:
- **Source**: thor user on Jump Host
- **Target Servers**: All App Servers (stapp01, stapp02, stapp03)
- **Target Users**: 
  - App Server 1: `tony`
  - App Server 2: `steve`
  - App Server 3: `banner`
- **Operation Objectives**:
  - Generate SSH key pair for thor user
  - Distribute public key to all app servers
  - Enable password-less authentication
  - Verify SSH access without password prompts
- Use RSA 4096-bit encryption for security
- Maintain automation compatibility

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Verify Current User on Jump Host

```bash
whoami
hostname
```

**Purpose**: Confirm you're logged in as thor user on jump host.

**Expected Output**:
```
thor
jump_host
```

**Success Indicators**:
- ✅ Current user is `thor`
- ✅ Hostname is `jump_host`
- ✅ Ready to generate SSH keys

---

## 🔹 Step 2: Check for Existing SSH Keys (Optional)

```bash
ls -la ~/.ssh/
```

**Purpose**: Check if SSH keys already exist.

**Expected Output** (if no keys exist):
```
ls: cannot access '/home/thor/.ssh/': No such file or directory
```

**Or** (if directory exists but no keys):
```
total 8
drwx------ 2 thor thor 4096 Feb 09 18:50 .
drwxr-xr-x 5 thor thor 4096 Feb 09 18:50 ..
```

**Success Indicators**:
- ✅ Directory checked
- ✅ Ready to generate new keys

**Note**: If `id_rsa` and `id_rsa.pub` already exist, you can skip key generation or backup existing keys.

---

## 🔹 Step 3: Generate SSH Key Pair

```bash
ssh-keygen -t rsa -b 4096 -N "" -f ~/.ssh/id_rsa
```

**Purpose**: Generate a 4096-bit RSA key pair without a passphrase for automation.

**Command Breakdown**:
- `ssh-keygen`: SSH key generation utility
- `-t rsa`: Key type (RSA algorithm)
- `-b 4096`: Key size (4096 bits for enhanced security)
- `-N ""`: Empty passphrase (required for password-less automation)
- `-f ~/.ssh/id_rsa`: Output file path

**Expected Output**:
```
Generating public/private rsa key pair.
Created directory '/home/thor/.ssh'.
Your identification has been saved in /home/thor/.ssh/id_rsa.
Your public key has been saved in /home/thor/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:abc123def456... thor@jump_host
The key's randomart image is:
+---[RSA 4096]----+
|        .o.      |
|       .  o      |
|      . .. .     |
+----[SHA256]-----+
```

**Success Indicators**:
- ✅ Private key created: `~/.ssh/id_rsa`
- ✅ Public key created: `~/.ssh/id_rsa.pub`
- ✅ Key fingerprint displayed
- ✅ No passphrase set

---

## 🔹 Step 4: Verify SSH Keys Created

```bash
ls -l ~/.ssh/
```

**Purpose**: Confirm both private and public keys were created.

**Expected Output**:
```
total 8
-rw------- 1 thor thor 3243 Feb 09 18:55 id_rsa
-rw-r--r-- 1 thor thor  743 Feb 09 18:55 id_rsa.pub
```

**Success Indicators**:
- ✅ `id_rsa` exists (private key, 600 permissions)
- ✅ `id_rsa.pub` exists (public key, 644 permissions)
- ✅ Proper permissions set automatically

---

## 🔹 Step 5: View Public Key (Optional)

```bash
cat ~/.ssh/id_rsa.pub
```

**Purpose**: Display the public key that will be distributed.

**Expected Output**:
```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQC... thor@jump_host
```

**Success Indicators**:
- ✅ Public key displayed
- ✅ Starts with `ssh-rsa`
- ✅ Ends with `thor@jump_host`

---

## 🔹 Step 6: Copy Public Key to App Server 1 (tony)

```bash
ssh-copy-id tony@stapp01
```

**Purpose**: Copy thor's public key to tony's authorized_keys on App Server 1.

**Command Breakdown**:
- `ssh-copy-id`: Utility to install SSH key on remote server
- `tony@stapp01`: Target user and hostname

**Expected Output**:
```
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/thor/.ssh/id_rsa.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
tony@stapp01's password: 

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'tony@stapp01'"
and check to make sure that only the key(s) you wanted were added.
```

**Success Indicators**:
- ✅ Prompted for tony's password (one-time only)
- ✅ Key successfully added
- ✅ Message confirms 1 key added

---

## 🔹 Step 7: Copy Public Key to App Server 2 (steve)

```bash
ssh-copy-id steve@stapp02
```

**Purpose**: Copy thor's public key to steve's authorized_keys on App Server 2.

**Expected Output**:
```
steve@stapp02's password: 

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'steve@stapp02'"
and check to make sure that only the key(s) you wanted were added.
```

**Success Indicators**:
- ✅ Prompted for steve's password
- ✅ Key successfully added

---

## 🔹 Step 8: Copy Public Key to App Server 3 (banner)

```bash
ssh-copy-id banner@stapp03
```

**Purpose**: Copy thor's public key to banner's authorized_keys on App Server 3.

**Expected Output**:
```
banner@stapp03's password: 

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'banner@stapp03'"
and check to make sure that only the key(s) you wanted were added.
```

**Success Indicators**:
- ✅ Prompted for banner's password
- ✅ Key successfully added
- ✅ All three servers configured

---

## 🔹 Step 9: Test Password-less SSH to App Server 1

```bash
ssh tony@stapp01
```

**Purpose**: Verify password-less authentication works for App Server 1.

**Expected Output**:
```
Welcome to App Server 1 - Stratos Datacenter
[tony@stapp01 ~]$
```

**Success Indicators**:
- ✅ No password prompt
- ✅ Successfully logged in
- ✅ Command prompt shows `tony@stapp01`

**Exit and continue**:
```bash
exit
```

---

## 🔹 Step 10: Test Password-less SSH to App Server 2

```bash
ssh steve@stapp02
```

**Purpose**: Verify password-less authentication works for App Server 2.

**Expected Output**:
```
Welcome to App Server 2 - Stratos Datacenter
[steve@stapp02 ~]$
```

**Success Indicators**:
- ✅ No password prompt
- ✅ Successfully logged in

**Exit**:
```bash
exit
```

---

## 🔹 Step 11: Test Password-less SSH to App Server 3

```bash
ssh banner@stapp03
```

**Purpose**: Verify password-less authentication works for App Server 3.

**Expected Output**:
```
Welcome to App Server 3 - Stratos Datacenter
[banner@stapp03 ~]$
```

**Success Indicators**:
- ✅ No password prompt
- ✅ Successfully logged in
- ✅ All servers accessible without passwords

**Exit**:
```bash
exit
```

---

## 🔹 Step 12: Test Remote Command Execution (Optional)

```bash
ssh tony@stapp01 'hostname'
ssh steve@stapp02 'hostname'
ssh banner@stapp03 'hostname'
```

**Purpose**: Verify commands can be executed remotely without interaction.

**Expected Output**:
```
stapp01.stratos.xfusioncorp.com
stapp02.stratos.xfusioncorp.com
stapp03.stratos.xfusioncorp.com
```

**Success Indicators**:
- ✅ Commands execute without password prompts
- ✅ Output returned correctly
- ✅ Automation-ready

---

## 📋 Quick Command Reference

For quick copy-paste, execute from **Jump Host**:

```bash
# Verify current user
whoami
hostname

# Check for existing keys (optional)
ls -la ~/.ssh/

# Generate SSH key pair
ssh-keygen -t rsa -b 4096 -N "" -f ~/.ssh/id_rsa

# Verify keys created
ls -l ~/.ssh/

# View public key (optional)
cat ~/.ssh/id_rsa.pub

# Copy public key to all app servers
ssh-copy-id tony@stapp01
ssh-copy-id steve@stapp02
ssh-copy-id banner@stapp03

# Test password-less SSH
ssh tony@stapp01
exit

ssh steve@stapp02
exit

ssh banner@stapp03
exit

# Test remote command execution (optional)
ssh tony@stapp01 'hostname'
ssh steve@stapp02 'hostname'
ssh banner@stapp03 'hostname'
```

---

## 💡 Common SSH Key Authentication Issues & Fixes

### **Issue 1: Still Prompted for Password**
**Problem**: Password still required after ssh-copy-id.
**Fix**: Check authorized_keys permissions and SSH configuration.
```bash
# On remote server, check permissions
ssh tony@stapp01
ls -l ~/.ssh/authorized_keys
# Should be: -rw------- (600)

# Fix permissions if needed
chmod 600 ~/.ssh/authorized_keys
chmod 700 ~/.ssh
```

### **Issue 2: ssh-copy-id Command Not Found**
**Problem**: ssh-copy-id utility not available.
**Fix**: Manually copy public key.
```bash
# Manual method
cat ~/.ssh/id_rsa.pub | ssh tony@stapp01 'mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys'
```

### **Issue 3: Permission Denied (publickey)**
**Problem**: Public key authentication rejected.
**Fix**: Verify SSH server configuration allows key authentication.
```bash
# On remote server, check sshd_config
ssh tony@stapp01
sudo grep PubkeyAuthentication /etc/ssh/sshd_config
# Should be: PubkeyAuthentication yes

# Enable if disabled
sudo sed -i 's/^#PubkeyAuthentication yes/PubkeyAuthentication yes/' /etc/ssh/sshd_config
sudo systemctl restart sshd
```

### **Issue 4: Multiple Keys Conflict**
**Problem**: Wrong key being used.
**Fix**: Specify key explicitly.
```bash
# Use specific key
ssh -i ~/.ssh/id_rsa tony@stapp01

# Or configure in ~/.ssh/config
cat >> ~/.ssh/config << EOF
Host stapp01
    HostName stapp01
    User tony
    IdentityFile ~/.ssh/id_rsa
EOF
```

### **Issue 5: Host Key Verification Failed**
**Problem**: Host key changed or not recognized.
**Fix**: Accept new host key or update known_hosts.
```bash
# Remove old host key
ssh-keygen -R stapp01

# Connect and accept new key
ssh tony@stapp01
# Type 'yes' when prompted
```

---

## 🔧 Troubleshooting Failures

### **Error: Private Key Permissions Too Open**
**Symptoms**: `Permissions 0644 for '/home/thor/.ssh/id_rsa' are too open`.
**Solution**: Fix private key permissions.
```bash
chmod 600 ~/.ssh/id_rsa
chmod 644 ~/.ssh/id_rsa.pub
```

### **Error: Could Not Create Directory**
**Symptoms**: Cannot create .ssh directory.
**Solution**: Create directory manually with proper permissions.
```bash
mkdir -p ~/.ssh
chmod 700 ~/.ssh
```

### **Error: Connection Refused**
**Symptoms**: `ssh: connect to host stapp01 port 22: Connection refused`.
**Solution**: Verify SSH service is running on target server.
```bash
# Check if server is reachable
ping stapp01

# Verify SSH port is open
telnet stapp01 22
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The primary challenge was setting up password-less SSH authentication from a central jump host to multiple app servers with different user accounts. This requires generating SSH keys, distributing public keys securely, and verifying the configuration works for automation purposes.

**💡 Solution Approach**:
1. **Key Generation**: Created 4096-bit RSA key pair without passphrase.
2. **Key Distribution**: Used `ssh-copy-id` to install public key on all servers.
3. **Multi-User Setup**: Configured access for different users (tony, steve, banner).
4. **Verification**: Tested SSH connections without password prompts.
5. **Automation Testing**: Verified remote command execution works.

**🎯 Key Success Factors**:
- **No Passphrase**: `-N ""` flag enables automation.
- **ssh-copy-id**: Simplifies key distribution process.
- **Proper Permissions**: Automatic permission setting by ssh-keygen.
- **Multi-Server**: Repeated process for all three app servers.
- **Testing**: Verified both interactive and non-interactive access.

**⚠️ Critical Fixes**:
- Always use `-N ""` for automation (no passphrase).
- Use `ssh-copy-id` for proper key installation.
- Test actual SSH connections, not just key copying.
- Verify remote command execution for automation.
- Ensure proper permissions on .ssh directory and files.

**🔒 SSH Security Best Practices Applied**:
- **Strong Encryption**: 4096-bit RSA keys for enhanced security.
- **Key-Based Auth**: More secure than password authentication.
- **Proper Permissions**: 600 for private key, 644 for public key.
- **Verification**: Tested all connections before declaring success.
- **Documentation**: Clear record of which keys access which servers.

**⚠️ Important SSH Concepts**:
- **Public Key**: Shared with remote servers (id_rsa.pub).
- **Private Key**: Kept secret on jump host (id_rsa).
- **authorized_keys**: File on remote server containing allowed public keys.
- **ssh-copy-id**: Utility that handles key installation correctly.
- **Passphrase**: Not used for automation scenarios.

---

## ⚠️ Important Production Notes

🔧 **Key Size**: 4096-bit RSA provides strong security for production use.
🔐 **No Passphrase**: Required for automation but reduces security if key is compromised.
📊 **Key Management**: Implement key rotation policies for production.
🛡️ **Access Control**: Limit which servers can use specific keys.
🗄️ **Backup Keys**: Securely backup private keys for disaster recovery.
📝 **Documentation**: Maintain inventory of which keys access which servers.
🔍 **Monitoring**: Monitor SSH access logs for unauthorized attempts.
⚙️ **Automation**: Essential for CI/CD pipelines and scheduled tasks.

**SSH Key Types**:
```bash
# RSA (most compatible, recommended 4096-bit)
ssh-keygen -t rsa -b 4096

# Ed25519 (modern, faster, smaller keys)
ssh-keygen -t ed25519

# ECDSA (elliptic curve, good balance)
ssh-keygen -t ecdsa -b 521
```

**SSH Configuration File**:
```bash
# Create ~/.ssh/config for easier access
cat > ~/.ssh/config << 'EOF'
Host stapp01
    HostName stapp01.stratos.xfusioncorp.com
    User tony
    IdentityFile ~/.ssh/id_rsa

Host stapp02
    HostName stapp02.stratos.xfusioncorp.com
    User steve
    IdentityFile ~/.ssh/id_rsa

Host stapp03
    HostName stapp03.stratos.xfusioncorp.com
    User banner
    IdentityFile ~/.ssh/id_rsa
EOF

chmod 600 ~/.ssh/config

# Now you can use short names
ssh stapp01
```

**Security Recommendations**:
- Use strong key sizes (4096-bit RSA minimum)
- Implement key rotation policies (annually recommended)
- Restrict private key access (chmod 600)
- Use SSH agent for temporary key storage
- Implement jump host access controls
- Monitor authorized_keys files for unauthorized additions
- Use certificate-based authentication for large deployments
- Implement two-factor authentication where possible

**Automation Scripts**:
```bash
# Example: Run command on all app servers
for server in tony@stapp01 steve@stapp02 banner@stapp03; do
    echo "Executing on $server"
    ssh $server 'uptime'
done

# Parallel execution
parallel-ssh -H "tony@stapp01 steve@stapp02 banner@stapp03" 'hostname'
```

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Generated 4096-bit RSA SSH key pair for thor user
- Distributed public key to all three app servers
- Configured password-less SSH access for:
  - tony@stapp01
  - steve@stapp02
  - banner@stapp03
- Verified password-less authentication works
- Tested remote command execution

**🔍 Verification Completed**:
- **App Server 1**: Password-less SSH to tony@stapp01 working
- **App Server 2**: Password-less SSH to steve@stapp02 working
- **App Server 3**: Password-less SSH to banner@stapp03 working
- **Remote Commands**: Execution without password prompts verified

**🚀 Next Steps** (Optional):
- Configure SSH config file for easier access
- Implement key rotation schedule
- Set up SSH agent for key management
- Document key usage in automation scripts
- Monitor SSH access logs
- Implement backup strategy for private keys

**🔒 Configuration Validation**:
- ✅ SSH keys generated on jump host
- ✅ Public key distributed to all app servers
- ✅ No password prompts during SSH connections
- ✅ Remote command execution works
- ✅ Automation-ready configuration
- ✅ Proper file permissions maintained

**📊 Configuration Summary**:
- **Source**: thor@jump_host
- **Key Type**: RSA 4096-bit
- **Passphrase**: None (automation requirement)
- **Target Servers**: 3 (stapp01, stapp02, stapp03)
- **Target Users**: tony, steve, banner
- **Authentication**: Public key (password-less)

**🔍 Key Learning Points**:
- `ssh-keygen` generates public/private key pairs
- `-N ""` creates key without passphrase for automation
- `ssh-copy-id` properly installs public key on remote servers
- Password-less SSH enables automation and scripting
- Proper permissions are critical for SSH security
- Testing both interactive and command execution is important
