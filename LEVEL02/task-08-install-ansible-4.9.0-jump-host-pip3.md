# 🔐 Task 8 - Install Ansible 4.9.0 on Jump Host Using pip3

**📌 Task Description**

During the weekly meeting, the **Nautilus DevOps team** discussed automation and configuration management solutions that they want to implement. While considering several options, the team has decided to go with **Ansible** for now due to its simple setup and minimal pre-requisites. The team wanted to start testing using Ansible, so they have decided to use jump host as an Ansible controller to test different kinds of tasks on the rest of the servers. Ansible is a powerful automation tool that uses SSH for agentless configuration management.

**👉 Task Requirements**:
- **Target Server**: Jump Host
- **Package**: Ansible version 4.9.0
- **Installation Method**: pip3 (Python package installer)
- **Global Availability**: All users must be able to run Ansible commands
- **Operation Objectives**:
  - Install python3-pip if not available
  - Install specific Ansible version (4.9.0) using pip3
  - Ensure Ansible binary is globally accessible
  - Verify installation and version
- Use sudo pip3 for system-wide installation
- Verify PATH configuration

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Check pip3 Version

```bash
pip3 --version
```

**Purpose**: Verify if pip3 is already installed on the system.

**Expected Output** (if installed):
```
pip 21.3.1 from /usr/lib/python3.9/site-packages/pip (python 3.9)
```

**Or** (if not installed):
```
bash: pip3: command not found
```

**Success Indicators**:
- ✅ pip3 version displayed (if installed)
- ✅ Ready to proceed with installation

---

## 🔹 Step 2: Install python3-pip Package

```bash
sudo yum install -y python3-pip
```

**Purpose**: Install pip3 package manager for Python 3.

**Command Breakdown**:
- `sudo`: Execute with administrative privileges
- `yum`: Package manager (RHEL/CentOS)
- `install -y`: Install package with automatic yes
- `python3-pip`: Python 3 package installer

**Expected Output** (sample):
```
Last metadata expiration check: 0:10:00 ago on Mon 10 Feb 2026 09:30:00 AM EST.
Dependencies resolved.
================================================================================
 Package              Arch        Version              Repository        Size
================================================================================
Installing:
 python3-pip          noarch      21.3.1-1.el8         baseos           1.8 M

Transaction Summary
================================================================================
Install  1 Package

Total download size: 1.8 M
Installed size: 8.0 M
Downloading Packages:
Running transaction
  Installing       : python3-pip-21.3.1-1.el8.noarch                      1/1
  Verifying        : python3-pip-21.3.1-1.el8.noarch                      1/1

Installed:
  python3-pip-21.3.1-1.el8.noarch

Complete!
```

**Success Indicators**:
- ✅ Package installed successfully
- ✅ "Complete!" message displayed
- ✅ pip3 now available

**Note**: If pip3 was already installed, you may see "Nothing to do" message.

---

## 🔹 Step 3: Verify pip3 Installation

```bash
pip3 --version
```

**Purpose**: Confirm pip3 is now available.

**Expected Output**:
```
pip 21.3.1 from /usr/lib/python3.9/site-packages/pip (python 3.9)
```

**Success Indicators**:
- ✅ pip3 version displayed
- ✅ Ready to install Ansible

---

## 🔹 Step 4: Install Ansible 4.9.0 Using pip3

```bash
sudo pip3 install ansible==4.9.0
```

**Purpose**: Install specific version of Ansible globally using pip3.

**Command Breakdown**:
- `sudo`: Install system-wide (global access for all users)
- `pip3`: Python 3 package installer
- `install`: Install command
- `ansible==4.9.0`: Package name with specific version

**Expected Output** (sample):
```
Collecting ansible==4.9.0
  Downloading ansible-4.9.0.tar.gz (35.4 MB)
     |████████████████████████████████| 35.4 MB 2.5 MB/s
Collecting ansible-core~=2.11.7
  Downloading ansible-core-2.11.12.tar.gz (7.1 MB)
     |████████████████████████████████| 7.1 MB 5.2 MB/s
Collecting jinja2
  Downloading Jinja2-3.0.3-py3-none-any.whl (133 kB)
     |████████████████████████████████| 133 kB 8.5 MB/s
Collecting PyYAML
  Downloading PyYAML-6.0-cp39-cp39-linux_x86_64.whl (661 kB)
     |████████████████████████████████| 661 kB 10.2 MB/s
Collecting cryptography
  Downloading cryptography-36.0.1-cp36-abi3-linux_x86_64.whl (3.6 MB)
     |████████████████████████████████| 3.6 MB 8.1 MB/s
Installing collected packages: PyYAML, MarkupSafe, jinja2, cryptography, ansible-core, ansible
Successfully installed MarkupSafe-2.0.1 PyYAML-6.0 ansible-4.9.0 ansible-core-2.11.12 cryptography-36.0.1 jinja2-3.0.3
```

**Success Indicators**:
- ✅ Ansible 4.9.0 downloaded and installed
- ✅ Dependencies installed (ansible-core, jinja2, PyYAML, etc.)
- ✅ "Successfully installed" message displayed
- ✅ No error messages

**⚠️ Important**: Using `sudo` ensures Ansible is installed system-wide in `/usr/local/bin/`, making it accessible to all users.

---

## 🔹 Step 5: Verify Ansible Binary Location

```bash
which ansible
```

**Purpose**: Confirm Ansible binary is in system PATH.

**Expected Output**:
```
/usr/local/bin/ansible
```

**Success Indicators**:
- ✅ Ansible binary path displayed
- ✅ Located in `/usr/local/bin/` (global location)
- ✅ Accessible to all users

---

## 🔹 Step 6: Check PATH Environment Variable

```bash
echo $PATH
```

**Purpose**: Verify `/usr/local/bin` is in the system PATH.

**Expected Output** (sample):
```
/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/home/thor/.local/bin:/home/thor/bin
```

**Success Indicators**:
- ✅ `/usr/local/bin` present in PATH
- ✅ Ansible will be accessible from any location
- ✅ All users can run Ansible commands

---

## 🔹 Step 7: Verify Ansible Version

```bash
ansible --version
```

**Purpose**: Confirm correct Ansible version is installed.

**Expected Output**:
```
ansible [core 2.11.12]
  config file = None
  configured module search path = ['/home/thor/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/local/lib/python3.9/site-packages/ansible
  ansible collection location = /home/thor/.ansible/collections:/usr/share/ansible/collections
  executable location = /usr/local/bin/ansible
  python version = 3.9.7 (default, Sep 10 2021, 14:59:43) [GCC 8.5.0 20210514 (Red Hat 8.5.0-3)]
  jinja version = 3.0.3
  libyaml = True
```

**Success Indicators**:
- ✅ Ansible version displayed (core 2.11.x for Ansible 4.9.0)
- ✅ Executable location is `/usr/local/bin/ansible`
- ✅ Python version shown
- ✅ Installation complete

**⚠️ Note**: Ansible 4.9.0 uses ansible-core 2.11.x internally.

---

## 🔹 Step 8: Test Ansible Command (Optional)

```bash
ansible localhost -m ping
```

**Purpose**: Test Ansible functionality with a simple ping module.

**Expected Output**:
```
[WARNING]: No inventory was parsed, only implicit localhost is available
localhost | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

**Success Indicators**:
- ✅ Ansible executes successfully
- ✅ "SUCCESS" and "pong" response
- ✅ Ansible is fully functional

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **Jump Host**:

```bash
# Check if pip3 is installed
pip3 --version

# Install python3-pip
sudo yum install -y python3-pip

# Verify pip3 installation
pip3 --version

# Install Ansible 4.9.0
sudo pip3 install ansible==4.9.0

# Verify Ansible binary location
which ansible

# Check PATH
echo $PATH

# Verify Ansible version
ansible --version

# Test Ansible (optional)
ansible localhost -m ping
```

---

## 💡 Common Ansible Installation Issues & Fixes

### **Issue 1: pip3 Command Not Found**
**Problem**: pip3 not available after installation.
**Fix**: Ensure python3-pip package is installed.
```bash
# Install pip3
sudo yum install -y python3-pip

# Verify
pip3 --version
```

### **Issue 2: Ansible Not in PATH**
**Problem**: Ansible command not found after installation.
**Fix**: Verify installation location and PATH.
```bash
# Find Ansible
find /usr -name ansible 2>/dev/null

# Add to PATH if needed (usually not required)
export PATH=$PATH:/usr/local/bin

# Make permanent in ~/.bashrc
echo 'export PATH=$PATH:/usr/local/bin' >> ~/.bashrc
```

### **Issue 3: Wrong Ansible Version**
**Problem**: Different version installed.
**Fix**: Uninstall and reinstall specific version.
```bash
# Uninstall current version
sudo pip3 uninstall ansible ansible-core -y

# Install specific version
sudo pip3 install ansible==4.9.0

# Verify
ansible --version
```

### **Issue 4: Permission Denied**
**Problem**: Cannot install without sudo.
**Fix**: Use sudo for system-wide installation.
```bash
# WRONG - User-only installation
pip3 install ansible==4.9.0

# CORRECT - System-wide installation
sudo pip3 install ansible==4.9.0
```

### **Issue 5: Dependency Conflicts**
**Problem**: Package dependency errors during installation.
**Fix**: Upgrade pip and retry.
```bash
# Upgrade pip3
sudo pip3 install --upgrade pip

# Retry Ansible installation
sudo pip3 install ansible==4.9.0
```

---

## 🔧 Troubleshooting Failures

### **Error: No module named 'ansible'**
**Symptoms**: Ansible installed but Python can't find it.
**Solution**: Verify Python path and reinstall.
```bash
# Check Python path
python3 -c "import sys; print(sys.path)"

# Reinstall Ansible
sudo pip3 uninstall ansible -y
sudo pip3 install ansible==4.9.0
```

### **Error: Network Connection Failed**
**Symptoms**: Cannot download Ansible package.
**Solution**: Check network and proxy settings.
```bash
# Test connectivity
ping pypi.org

# Use proxy if needed
sudo pip3 install ansible==4.9.0 --proxy http://proxy:port
```

### **Error: Insufficient Disk Space**
**Symptoms**: Installation fails due to disk space.
**Solution**: Clean up disk space.
```bash
# Check disk space
df -h

# Clean pip cache
sudo pip3 cache purge

# Clean yum cache
sudo yum clean all
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The primary challenge was installing a specific version of Ansible globally using pip3 so that all users on the system can access it. Understanding the difference between user-level and system-wide installation, and ensuring the correct version is installed, is crucial for proper Ansible deployment.

**💡 Solution Approach**:
1. **Prerequisite Check**: Verified pip3 availability.
2. **pip3 Installation**: Installed python3-pip package if needed.
3. **Global Installation**: Used `sudo pip3` for system-wide access.
4. **Version Specification**: Used `==4.9.0` to install exact version.
5. **Verification**: Confirmed version, location, and PATH configuration.

**🎯 Key Success Factors**:
- **sudo pip3**: Ensures system-wide installation for all users.
- **Version Specification**: `==4.9.0` installs exact required version.
- **PATH Verification**: Confirmed `/usr/local/bin` in PATH.
- **Binary Location**: Verified Ansible in `/usr/local/bin/`.
- **Testing**: Confirmed Ansible functionality with ping module.

**⚠️ Critical Fixes**:
- Always use `sudo pip3` for global installation.
- Specify exact version with `==4.9.0`.
- Verify Ansible binary is in `/usr/local/bin/`.
- Check PATH includes `/usr/local/bin`.
- Test with `ansible --version` to confirm.

**🔒 Ansible Installation Best Practices Applied**:
- **System-wide Access**: Using sudo ensures all users can run Ansible.
- **Version Control**: Specific version ensures consistency.
- **Verification**: Multiple checks confirm successful installation.
- **PATH Management**: Ensured binary is in standard system PATH.
- **Testing**: Validated functionality before declaring success.

**⚠️ Important Ansible Concepts**:
- **Ansible vs ansible-core**: Ansible 4.x includes ansible-core 2.11.x.
- **pip3 vs yum**: pip3 allows specific version installation.
- **Global vs User**: sudo pip3 installs globally, pip3 installs per-user.
- **PATH**: `/usr/local/bin` is standard for pip-installed binaries.
- **Agentless**: Ansible doesn't require agents on managed nodes.

---

## ⚠️ Important Production Notes

🔧 **Version Pinning**: Always specify exact version for production consistency.
🔐 **Global Access**: System-wide installation ensures all automation users can run Ansible.
📊 **Ansible Collections**: Ansible 4.x uses collections for modules.
🛡️ **SSH Keys**: Configure SSH keys for password-less authentication to managed nodes.
🗄️ **Inventory**: Create inventory file for managed hosts.
📝 **Configuration**: Use ansible.cfg for custom settings.
🔍 **Testing**: Always test Ansible installation before production use.
⚙️ **Updates**: Plan for Ansible version upgrades carefully.

**Ansible Version Reference**:
```bash
# Check Ansible version
ansible --version

# Check ansible-core version
ansible-core --version

# List installed Ansible packages
pip3 list | grep ansible

# Show Ansible package details
pip3 show ansible
```

**Ansible Configuration**:
```bash
# Create ansible.cfg (optional)
cat > ~/ansible.cfg << 'EOF'
[defaults]
inventory = ~/inventory
host_key_checking = False
remote_user = ansible
EOF

# Create inventory file
cat > ~/inventory << 'EOF'
[app_servers]
stapp01 ansible_user=tony
stapp02 ansible_user=steve
stapp03 ansible_user=banner

[db_servers]
stdb01 ansible_user=peter
EOF
```

**Security Recommendations**:
- Use Ansible Vault for sensitive data
- Implement role-based access control
- Regular Ansible version updates
- Secure SSH key management
- Audit Ansible playbook executions
- Use least privilege principle
- Encrypt sensitive variables
- Regular security audits

**Ansible Quick Start**:
```bash
# Test connectivity to all hosts
ansible all -m ping -i inventory

# Run ad-hoc command
ansible app_servers -m command -a "uptime" -i inventory

# Create simple playbook
cat > test.yml << 'EOF'
---
- name: Test Playbook
  hosts: all
  tasks:
    - name: Print hostname
      command: hostname
      register: result
    - name: Show result
      debug:
        var: result.stdout
EOF

# Run playbook
ansible-playbook test.yml -i inventory
```

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Installed python3-pip package on Jump Host
- Installed Ansible version 4.9.0 using pip3
- Verified Ansible binary is globally accessible
- Confirmed all users can run Ansible commands
- Validated installation with version check

**🔍 Verification Completed**:
- pip3 installed and functional
- Ansible 4.9.0 installed successfully
- Ansible binary located at `/usr/local/bin/ansible`
- PATH includes `/usr/local/bin`
- Ansible version confirmed as 4.9.0 (core 2.11.x)

**🚀 Next Steps** (Optional):
- Configure SSH keys for managed nodes
- Create Ansible inventory file
- Write test playbooks
- Configure ansible.cfg
- Test connectivity to app servers
- Implement Ansible roles and playbooks

**🔒 Configuration Validation**:
- ✅ Ansible 4.9.0 installed
- ✅ Global installation (all users can access)
- ✅ Binary in `/usr/local/bin/`
- ✅ PATH configured correctly
- ✅ Version verified
- ✅ Functionality tested

**📊 Configuration Summary**:
- **Server**: Jump Host
- **Package**: Ansible 4.9.0
- **Installation Method**: pip3
- **Installation Scope**: System-wide (global)
- **Binary Location**: /usr/local/bin/ansible
- **Python Version**: 3.9.x
- **ansible-core Version**: 2.11.x

**🔍 Key Learning Points**:
- `sudo pip3` installs packages system-wide
- Specific versions installed with `==version`
- `/usr/local/bin` is standard location for pip binaries
- Ansible 4.x includes ansible-core 2.11.x
- `which` command locates binary in PATH
- `ansible --version` shows detailed installation info
