# 🔐 Task 9 - Configure Local Yum Repository on Backup Server

**📌 Task Description**

The **Nautilus production support team** and security team had a meeting last month in which they decided to use local yum repositories for maintaining packages needed for their servers. For now, they have decided to configure a local yum repo on **Nautilus Backup Server**. This is one of the pending items from last month. A local yum repository allows for faster package installation, reduced bandwidth usage, and better control over package versions in the infrastructure.

**👉 Task Requirements**:
- **Target Server**: Nautilus Backup Server (stbkp01)
- **Package Location**: `/packages/downloaded_rpms/`
- **Repository Name**: `yum_local`
- **Repository ID**: `yum_local`
- **Operation Objectives**:
  - Create repository metadata using createrepo
  - Configure yum repository file
  - Set repository to use local package directory
  - Install samba package from local repository
- Verify repository configuration
- Test package installation from local repo

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to Backup Server

```bash
ssh clint@stbkp01
```

**Purpose**: Establish SSH connection to Nautilus Backup Server.

**Expected Output**:
```
clint@stbkp01's password: 
Welcome to Nautilus Backup Server - Stratos Datacenter
[clint@stbkp01 ~]$
```

**Success Indicators**:
- ✅ Successfully authenticated as user `clint`
- ✅ Command prompt shows `stbkp01` hostname
- ✅ Ready to configure repository

---

## 🔹 Step 2: Verify createrepo Tool (Optional)

```bash
createrepo --version
```

**Purpose**: Check if createrepo is installed.

**Expected Output**:
```
createrepo 0.17.7
```

**Success Indicators**:
- ✅ createrepo version displayed
- ✅ Tool available for repository creation

**Note**: If not installed, use `sudo yum install -y createrepo`.

---

## 🔹 Step 3: Navigate to Package Directory

```bash
cd /packages/downloaded_rpms/
```

**Purpose**: Change to the directory containing RPM packages.

**Expected Output**:
```
[clint@stbkp01 downloaded_rpms]$
```

**Success Indicators**:
- ✅ Directory changed successfully
- ✅ Ready to create repository metadata

---

## 🔹 Step 4: Verify RPM Packages Exist (Optional)

```bash
ls -lh *.rpm | head -10
```

**Purpose**: Confirm RPM packages are present in the directory.

**Expected Output** (sample):
```
-rw-r--r-- 1 root root 850K Feb 10 09:00 samba-4.14.5-7.el8.x86_64.rpm
-rw-r--r-- 1 root root 220K Feb 10 09:00 samba-common-4.14.5-7.el8.noarch.rpm
-rw-r--r-- 1 root root 180K Feb 10 09:00 samba-libs-4.14.5-7.el8.x86_64.rpm
```

**Success Indicators**:
- ✅ RPM packages listed
- ✅ Packages available for repository

---

## 🔹 Step 5: Create Repository Metadata

```bash
sudo createrepo .
```

**Purpose**: Generate repository metadata for the RPM packages.

**Command Breakdown**:
- `sudo`: Execute with administrative privileges
- `createrepo`: Tool to create yum repository metadata
- `.`: Current directory (contains RPM packages)

**Expected Output**:
```
Spawning worker 0 with 150 pkgs
Workers Finished
Saving Primary metadata
Saving file lists metadata
Saving other metadata
Generating sqlite DBs
Sqlite DBs complete
```

**Success Indicators**:
- ✅ Metadata generated successfully
- ✅ `repodata` directory created
- ✅ No error messages

---

## 🔹 Step 6: Verify Repository Metadata Created

```bash
ls -ld repodata/
```

**Purpose**: Confirm repodata directory was created.

**Expected Output**:
```
drwxr-xr-x 2 root root 4096 Feb 10 09:40 repodata/
```

**Success Indicators**:
- ✅ `repodata` directory exists
- ✅ Repository metadata ready

---

## 🔹 Step 7: Create Yum Repository Configuration File

```bash
sudo vi /etc/yum.repos.d/yum_local.repo
```

**Purpose**: Create repository configuration file for yum.

**File Content**:
```ini
[yum_local]
name=Local Yum Repository
baseurl=file:///packages/downloaded_rpms/
enabled=1
gpgcheck=0
```

**Configuration Breakdown**:
- `[yum_local]`: Repository ID (must match requirement)
- `name=`: Human-readable repository name
- `baseurl=`: Location of repository (file:// for local path)
- `enabled=1`: Repository is enabled
- `gpgcheck=0`: Disable GPG signature checking

**Vi Editor Commands**:
1. Press `i` to enter insert mode
2. Paste the configuration above
3. Press `Esc` to exit insert mode
4. Type `:wq` and press `Enter` to save and exit

**Alternative (Non-interactive)**:
```bash
sudo tee /etc/yum.repos.d/yum_local.repo > /dev/null << 'EOF'
[yum_local]
name=Local Yum Repository
baseurl=file:///packages/downloaded_rpms/
enabled=1
gpgcheck=0
EOF
```

**Success Indicators**:
- ✅ File created successfully
- ✅ Configuration saved

---

## 🔹 Step 8: Verify Repository Configuration File

```bash
cat /etc/yum.repos.d/yum_local.repo
```

**Purpose**: Confirm repository configuration is correct.

**Expected Output**:
```
[yum_local]
name=Local Yum Repository
baseurl=file:///packages/downloaded_rpms/
enabled=1
gpgcheck=0
```

**Success Indicators**:
- ✅ Configuration matches requirements
- ✅ Repository ID is `yum_local`
- ✅ baseurl points to correct directory

---

## 🔹 Step 9: Clean Yum Cache

```bash
sudo yum clean all
```

**Purpose**: Clear yum cache to recognize new repository.

**Expected Output**:
```
Updating Subscription Management repositories.
Unable to read consumer identity
24 files removed
```

**Success Indicators**:
- ✅ Cache cleaned
- ✅ Ready to refresh repository list

---

## 🔹 Step 10: Verify Repository is Listed

```bash
sudo yum repolist
```

**Purpose**: Confirm yum_local repository is recognized.

**Expected Output** (sample):
```
repo id                    repo name
baseos                     BaseOS
yum_local                  Local Yum Repository
```

**Success Indicators**:
- ✅ `yum_local` appears in repository list
- ✅ Repository is enabled and accessible

---

## 🔹 Step 11: Install Samba from Local Repository

```bash
sudo yum install -y samba --disablerepo="*" --enablerepo="yum_local"
```

**Purpose**: Install samba package exclusively from the local repository.

**Command Breakdown**:
- `sudo yum install -y samba`: Install samba package
- `--disablerepo="*"`: Disable all repositories
- `--enablerepo="yum_local"`: Enable only yum_local repository

**Expected Output** (sample):
```
Local Yum Repository                                    150 kB/s | 1.2 MB     00:08
Dependencies resolved.
================================================================================
 Package           Arch       Version              Repository           Size
================================================================================
Installing:
 samba             x86_64     4.14.5-7.el8         yum_local           850 k
Installing dependencies:
 samba-common      noarch     4.14.5-7.el8         yum_local           220 k
 samba-libs        x86_64     4.14.5-7.el8         yum_local           180 k

Transaction Summary
================================================================================
Install  3 Packages

Total size: 1.2 M
Installed size: 4.5 M
Downloading Packages:
Running transaction
  Installing       : samba-common-4.14.5-7.el8.noarch                     1/3
  Installing       : samba-libs-4.14.5-7.el8.x86_64                       2/3
  Installing       : samba-4.14.5-7.el8.x86_64                            3/3

Installed:
  samba-4.14.5-7.el8.x86_64
  samba-common-4.14.5-7.el8.noarch
  samba-libs-4.14.5-7.el8.x86_64

Complete!
```

**Success Indicators**:
- ✅ Package installed from `yum_local` repository
- ✅ Dependencies resolved from local repo
- ✅ "Complete!" message displayed

---

## 🔹 Step 12: Verify Samba Installation

```bash
rpm -q samba
```

**Purpose**: Confirm samba package is installed.

**Expected Output**:
```
samba-4.14.5-7.el8.x86_64
```

**Success Indicators**:
- ✅ Samba version displayed
- ✅ Installation confirmed
- ✅ Task complete

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **Backup Server**:

```bash
# Connect to Backup Server
ssh clint@stbkp01

# Verify createrepo (optional)
createrepo --version

# Navigate to package directory
cd /packages/downloaded_rpms/

# Create repository metadata
sudo createrepo .

# Verify repodata created
ls -ld repodata/

# Create repository configuration
sudo tee /etc/yum.repos.d/yum_local.repo > /dev/null << 'EOF'
[yum_local]
name=Local Yum Repository
baseurl=file:///packages/downloaded_rpms/
enabled=1
gpgcheck=0
EOF

# Verify configuration
cat /etc/yum.repos.d/yum_local.repo

# Clean yum cache
sudo yum clean all

# Verify repository listed
sudo yum repolist

# Install samba from local repo
sudo yum install -y samba --disablerepo="*" --enablerepo="yum_local"

# Verify installation
rpm -q samba
```

---

## 💡 Common Yum Repository Issues & Fixes

### **Issue 1: createrepo Command Not Found**
**Problem**: createrepo not installed.
**Fix**: Install createrepo package.
```bash
sudo yum install -y createrepo
```

### **Issue 2: Repository Not Listed**
**Problem**: yum_local doesn't appear in repolist.
**Fix**: Verify configuration and clean cache.
```bash
# Check configuration
cat /etc/yum.repos.d/yum_local.repo

# Clean cache
sudo yum clean all

# Rebuild cache
sudo yum makecache
```

### **Issue 3: baseurl Path Incorrect**
**Problem**: Repository path not found.
**Fix**: Verify baseurl uses correct file:// protocol.
```bash
# WRONG - Missing file://
baseurl=/packages/downloaded_rpms/

# CORRECT - With file:// protocol
baseurl=file:///packages/downloaded_rpms/
```

### **Issue 4: Package Not Found in Repository**
**Problem**: Package not available in local repo.
**Fix**: Verify package exists and metadata is current.
```bash
# List packages in repository
ls /packages/downloaded_rpms/*.rpm | grep samba

# Regenerate metadata
cd /packages/downloaded_rpms/
sudo createrepo --update .
```

### **Issue 5: GPG Check Errors**
**Problem**: GPG signature verification fails.
**Fix**: Disable gpgcheck or import GPG keys.
```bash
# Disable GPG check (already done in config)
gpgcheck=0

# Or import GPG keys
sudo rpm --import /path/to/RPM-GPG-KEY
```

---

## 🔧 Troubleshooting Failures

### **Error: Cannot Create repodata**
**Symptoms**: Permission denied creating repository metadata.
**Solution**: Use sudo and verify directory permissions.
```bash
# Check directory permissions
ls -ld /packages/downloaded_rpms/

# Use sudo
sudo createrepo /packages/downloaded_rpms/
```

### **Error: No Packages in Repository**
**Symptoms**: Repository shows 0 packages.
**Solution**: Verify RPM files exist and regenerate metadata.
```bash
# Count RPM files
ls /packages/downloaded_rpms/*.rpm | wc -l

# Regenerate metadata
cd /packages/downloaded_rpms/
sudo createrepo --update .
```

### **Error: Repository Configuration Syntax**
**Symptoms**: Yum fails to parse repository file.
**Solution**: Verify INI format and syntax.
```bash
# Check for syntax errors
sudo yum repolist -v

# Verify file format
cat /etc/yum.repos.d/yum_local.repo
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The primary challenge was creating a local yum repository from existing RPM packages, configuring yum to recognize it, and installing packages exclusively from this local source. Understanding createrepo, yum repository configuration, and the file:// URL protocol is crucial for local repository management.

**💡 Solution Approach**:
1. **Metadata Creation**: Used createrepo to generate repository metadata.
2. **Configuration**: Created yum repository file with correct format.
3. **Repository ID**: Set to `yum_local` as required.
4. **Local Path**: Used `file:///` protocol for local directory.
5. **Selective Installation**: Used --disablerepo and --enablerepo flags.

**🎯 Key Success Factors**:
- **createrepo**: Generates necessary metadata for yum.
- **Repository ID**: `[yum_local]` matches requirement exactly.
- **file:// Protocol**: Three slashes for absolute path.
- **gpgcheck=0**: Disables signature checking for local packages.
- **Selective Repo**: Ensures package comes from local repo only.

**⚠️ Critical Fixes**:
- Use `file:///` (three slashes) for absolute paths.
- Repository ID must match `[yum_local]`.
- Run createrepo in package directory.
- Clean yum cache after configuration changes.
- Use --disablerepo and --enablerepo for selective installation.

**🔒 Repository Management Best Practices Applied**:
- **Local Control**: Reduces external dependencies.
- **Version Control**: Maintains specific package versions.
- **Bandwidth Savings**: No external downloads required.
- **Verification**: Multiple checks confirm configuration.
- **Documentation**: Clear configuration for future reference.

**⚠️ Important Yum Repository Concepts**:
- **createrepo**: Generates repodata directory with metadata.
- **baseurl**: Specifies repository location (file://, http://, ftp://).
- **Repository ID**: Unique identifier in square brackets.
- **gpgcheck**: Controls GPG signature verification.
- **enabled**: Controls if repository is active.

---

## ⚠️ Important Production Notes

🔧 **Repository Updates**: Run `createrepo --update` when adding new packages.
🔐 **Security**: Consider enabling gpgcheck with proper GPG keys in production.
📊 **Mirroring**: Can mirror external repos for offline use.
🛡️ **Access Control**: Restrict repository directory permissions.
🗄️ **Backup**: Regular backups of repository packages and metadata.
📝 **Documentation**: Document repository purpose and contents.
🔍 **Monitoring**: Monitor disk space for repository growth.
⚙️ **Automation**: Script repository updates and maintenance.

**createrepo Command Reference**:
```bash
# Create repository
createrepo /path/to/rpms

# Update existing repository
createrepo --update /path/to/rpms

# Create with specific options
createrepo --database --unique-md-filenames /path/to/rpms

# Check repository
repoquery --repofrompath=local,/path/to/rpms --list-available
```

**Yum Repository Configuration Options**:
```ini
[repository_id]
name=Repository Name
baseurl=file:///path/to/repo
       http://server/path
       ftp://server/path
enabled=1              # 1=enabled, 0=disabled
gpgcheck=1             # 1=check signatures, 0=no check
gpgkey=file:///path/to/GPG-KEY
priority=1             # Lower number = higher priority
exclude=package1 package2
includepkgs=package1 package2
```

**Security Recommendations**:
- Enable GPG checking in production environments
- Restrict repository directory permissions (755 recommended)
- Regular security audits of repository packages
- Implement access controls for repository updates
- Monitor repository for unauthorized changes
- Use HTTPS for network-accessible repositories
- Regular vulnerability scanning of packages
- Document package sources and versions

**Repository Maintenance**:
```bash
# Add new packages
cp new-package.rpm /packages/downloaded_rpms/
cd /packages/downloaded_rpms/
sudo createrepo --update .

# Remove old packages
rm old-package.rpm
sudo createrepo --update .

# Verify repository integrity
sudo yum clean all
sudo yum repolist
sudo yum list available --disablerepo="*" --enablerepo="yum_local"
```

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Created repository metadata using createrepo
- Configured yum repository named `yum_local`
- Set Repository ID to `yum_local`
- Configured repository to use `/packages/downloaded_rpms/`
- Installed samba package from local repository
- Verified installation successfully

**🔍 Verification Completed**:
- Repository metadata created (repodata directory)
- Configuration file created at `/etc/yum.repos.d/yum_local.repo`
- Repository appears in `yum repolist`
- Samba installed exclusively from yum_local
- Package installation verified with rpm -q

**🚀 Next Steps** (Optional):
- Add more packages to repository as needed
- Enable GPG checking with proper keys
- Create repository update automation
- Document repository contents
- Implement repository backup strategy
- Configure repository access controls

**🔒 Configuration Validation**:
- ✅ Repository ID is `yum_local`
- ✅ Repository name set correctly
- ✅ baseurl points to `/packages/downloaded_rpms/`
- ✅ Repository enabled and functional
- ✅ Samba installed from local repository
- ✅ All requirements met

**📊 Configuration Summary**:
- **Server**: Nautilus Backup Server (stbkp01)
- **Repository Name**: yum_local
- **Repository ID**: yum_local
- **Package Location**: /packages/downloaded_rpms/
- **Configuration File**: /etc/yum.repos.d/yum_local.repo
- **Test Package**: samba (installed successfully)

**🔍 Key Learning Points**:
- `createrepo` generates yum repository metadata
- Repository configuration uses INI format
- `file:///` protocol for local paths (three slashes)
- Repository ID in square brackets must be unique
- `--disablerepo` and `--enablerepo` control package sources
- Local repositories reduce external dependencies
