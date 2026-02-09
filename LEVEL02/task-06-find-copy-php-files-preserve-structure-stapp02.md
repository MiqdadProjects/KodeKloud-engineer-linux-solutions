# 🔐 Task 6 - Find and Copy PHP Files with Directory Structure on App Server 2

**📌 Task Description**

During a routine security audit, the team identified an issue on the **Nautilus App Server**. Some malicious content was identified within the website code. After digging into the issue, they found that there might be more infected files. Before doing a cleanup, they would like to find all similar files and copy them to a safe location for further investigation. This task involves using the `find` command with `cp --parents` to preserve directory structure while copying specific file types.

**👉 Task Requirements**:
- **Target Server**: App Server 2 (stapp02) in Stratos Datacenter
- **Source Location**: `/var/www/html/beta`
- **Destination**: `/beta`
- **File Type**: All `.php` files (not directories)
- **Operation Objectives**:
  - Find all PHP files in source directory
  - Copy files preserving parent directory structure
  - Do NOT copy entire directory content
  - Only copy files with .php extension
- Use `find` command for file discovery
- Use `cp --parents` to preserve directory structure
- Verify all PHP files copied correctly

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to App Server 2

```bash
ssh steve@stapp02
```

**Purpose**: Establish SSH connection to App Server 2 for file operations.

**Expected Output**:
```
steve@stapp02's password: 
Welcome to App Server 2 - Stratos Datacenter
[steve@stapp02 ~]$
```

**Success Indicators**:
- ✅ Successfully authenticated as user `steve`
- ✅ Command prompt shows `stapp02` hostname
- ✅ Ready to execute commands

---

## 🔹 Step 2: Create Destination Directory

```bash
sudo mkdir -p /beta
```

**Purpose**: Create the destination directory for copied files.

**Command Breakdown**:
- `sudo`: Execute with administrative privileges
- `mkdir`: Make directory command
- `-p`: Create parent directories as needed
- `/beta`: Destination directory path

**Expected Output**:
```
[no output indicates success]
```

**Success Indicators**:
- ✅ Directory created
- ✅ No error messages

---

## 🔹 Step 3: Verify Source Directory Exists

```bash
ls -ld /var/www/html/beta
```

**Purpose**: Confirm the source directory exists and is accessible.

**Expected Output**:
```
drwxr-xr-x 5 root root 4096 Feb 09 19:00 /var/www/html/beta
```

**Success Indicators**:
- ✅ Directory exists
- ✅ Directory is readable
- ✅ Ready to search for files

---

## 🔹 Step 4: Preview PHP Files in Source Directory (Optional)

```bash
sudo find /var/www/html/beta -type f -name "*.php"
```

**Purpose**: List all PHP files that will be copied.

**Expected Output** (sample):
```
/var/www/html/beta/index.php
/var/www/html/beta/config/database.php
/var/www/html/beta/includes/functions.php
/var/www/html/beta/admin/login.php
```

**Success Indicators**:
- ✅ PHP files listed
- ✅ Understanding of file count and locations

---

## 🔹 Step 5: Change to Source Directory

```bash
cd /var/www/html/beta
```

**Purpose**: Change to source directory to use relative paths for copying.

**Expected Output**:
```
[steve@stapp02 beta]$
```

**Success Indicators**:
- ✅ Current directory changed
- ✅ Prompt shows `beta` directory
- ✅ Ready to use relative paths

**⚠️ Important**: Working from the source directory allows `cp --parents` to preserve the relative directory structure.

---

## 🔹 Step 6: Find and Copy PHP Files with Directory Structure

```bash
sudo find . -type f -name "*.php" -exec cp --parents {} /beta/ \;
```

**Purpose**: Find all PHP files and copy them to /beta preserving directory structure.

**Command Breakdown**:
- `sudo`: Execute with administrative privileges
- `find .`: Search starting from current directory (.)
- `-type f`: Only find files (not directories)
- `-name "*.php"`: Match files ending with .php
- `-exec`: Execute command for each found file
- `cp --parents {}`: Copy with parent directory structure
  - `--parents`: Preserve directory hierarchy
  - `{}`: Placeholder for found file
- `/beta/`: Destination directory
- `\;`: End of -exec command

**Expected Output**:
```
[no output indicates success]
```

**Success Indicators**:
- ✅ Command completes without errors
- ✅ Files copied with directory structure
- ✅ No permission errors

**⚠️ Important**: 
- `--parents` flag preserves the relative path structure
- Starting from current directory (.) ensures relative paths are used
- Only .php files are copied, not entire directories

---

## 🔹 Step 7: Verify Destination Directory Structure

```bash
ls -R /beta
```

**Purpose**: Display the directory structure created in /beta.

**Expected Output** (sample):
```
/beta:
.

/beta/.:
index.php  config  includes  admin

/beta/./config:
database.php

/beta/./includes:
functions.php

/beta/./admin:
login.php
```

**Success Indicators**:
- ✅ Directory structure preserved
- ✅ PHP files present in correct locations
- ✅ Relative paths maintained

---

## 🔹 Step 8: Find and List All Copied PHP Files

```bash
sudo find /beta -type f -name "*.php"
```

**Purpose**: Verify all PHP files were copied successfully.

**Expected Output** (sample):
```
/beta/./index.php
/beta/./config/database.php
/beta/./includes/functions.php
/beta/./admin/login.php
```

**Success Indicators**:
- ✅ All PHP files listed
- ✅ Directory structure preserved
- ✅ File count matches source

---

## 🔹 Step 9: Count PHP Files in Source and Destination (Optional)

```bash
echo "Source files:"
sudo find /var/www/html/beta -type f -name "*.php" | wc -l
echo "Destination files:"
sudo find /beta -type f -name "*.php" | wc -l
```

**Purpose**: Verify the same number of files in both locations.

**Expected Output** (sample):
```
Source files:
4
Destination files:
4
```

**Success Indicators**:
- ✅ File counts match
- ✅ All files copied successfully

---

## 🔹 Step 10: Verify File Permissions (Optional)

```bash
ls -l /beta/./index.php
```

**Purpose**: Check permissions of copied files.

**Expected Output**:
```
-rw-r--r-- 1 root root 1024 Feb 09 19:05 /beta/./index.php
```

**Success Indicators**:
- ✅ File permissions preserved
- ✅ Ownership set to root (due to sudo)

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **App Server 2**:

```bash
# Connect to App Server 2
ssh steve@stapp02

# Create destination directory
sudo mkdir -p /beta

# Verify source directory
ls -ld /var/www/html/beta

# Preview PHP files (optional)
sudo find /var/www/html/beta -type f -name "*.php"

# Change to source directory
cd /var/www/html/beta

# Find and copy PHP files with directory structure
sudo find . -type f -name "*.php" -exec cp --parents {} /beta/ \;

# Verify directory structure
ls -R /beta

# List all copied PHP files
sudo find /beta -type f -name "*.php"

# Count files (optional)
echo "Source files:"
sudo find /var/www/html/beta -type f -name "*.php" | wc -l
echo "Destination files:"
sudo find /beta -type f -name "*.php" | wc -l
```

---

## 💡 Common File Copy Issues & Fixes

### **Issue 1: Directory Structure Not Preserved**
**Problem**: Files copied without parent directories.
**Fix**: Use `--parents` flag with cp.
```bash
# WRONG - Copies files flat
sudo find . -type f -name "*.php" -exec cp {} /beta/ \;

# CORRECT - Preserves directory structure
sudo find . -type f -name "*.php" -exec cp --parents {} /beta/ \;
```

### **Issue 2: Entire Directory Copied**
**Problem**: All files copied, not just PHP files.
**Fix**: Ensure `-name "*.php"` filter is used.
```bash
# WRONG - Copies everything
sudo cp -r /var/www/html/beta /beta/

# CORRECT - Only PHP files
cd /var/www/html/beta
sudo find . -type f -name "*.php" -exec cp --parents {} /beta/ \;
```

### **Issue 3: Absolute Paths in Destination**
**Problem**: Full path replicated in destination.
**Fix**: Change to source directory first, use relative paths.
```bash
# WRONG - Creates /beta/var/www/html/beta/...
sudo find /var/www/html/beta -type f -name "*.php" -exec cp --parents {} /beta/ \;

# CORRECT - Creates /beta/./...
cd /var/www/html/beta
sudo find . -type f -name "*.php" -exec cp --parents {} /beta/ \;
```

### **Issue 4: Permission Denied**
**Problem**: Cannot copy files or create directories.
**Fix**: Use sudo for file operations.
```bash
# Use sudo
sudo find . -type f -name "*.php" -exec cp --parents {} /beta/ \;
```

### **Issue 5: Directories Included in Results**
**Problem**: Directories with .php in name included.
**Fix**: Use `-type f` to filter only files.
```bash
# Ensure -type f is specified
sudo find . -type f -name "*.php" -exec cp --parents {} /beta/ \;
```

---

## 🔧 Troubleshooting Failures

### **Error: cp: cannot create directory**
**Symptoms**: `cp: cannot create directory '/beta/./config': Permission denied`.
**Solution**: Ensure destination directory exists and use sudo.
```bash
# Create destination with proper permissions
sudo mkdir -p /beta

# Use sudo for copy operation
sudo find . -type f -name "*.php" -exec cp --parents {} /beta/ \;
```

### **Error: No such file or directory**
**Symptoms**: Source directory doesn't exist.
**Solution**: Verify source path and permissions.
```bash
# Check if source exists
ls -ld /var/www/html/beta

# Verify you have read permissions
sudo ls -la /var/www/html/beta
```

### **Error: Destination has wrong structure**
**Symptoms**: Files not in expected locations.
**Solution**: Ensure you're in source directory when using relative paths.
```bash
# Change to source directory first
cd /var/www/html/beta

# Then run find with relative path
sudo find . -type f -name "*.php" -exec cp --parents {} /beta/ \;
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The primary challenge was copying specific file types while preserving their directory structure without copying the entire directory tree. Using `find` with `cp --parents` and understanding the importance of relative paths is crucial for this selective copying operation.

**💡 Solution Approach**:
1. **Destination Creation**: Created /beta directory for isolated storage.
2. **Directory Navigation**: Changed to source directory for relative paths.
3. **Selective Find**: Used `find . -type f -name "*.php"` to locate only PHP files.
4. **Structure Preservation**: Used `cp --parents` to maintain directory hierarchy.
5. **Verification**: Counted and listed files to confirm successful copy.

**🎯 Key Success Factors**:
- **Relative Paths**: Working from source directory ensures relative path preservation.
- **--parents Flag**: Critical for maintaining directory structure.
- **Type Filter**: `-type f` ensures only files, not directories.
- **Name Pattern**: `"*.php"` selects only PHP files.
- **Verification**: Multiple checks confirm successful operation.

**⚠️ Critical Fixes**:
- Must `cd` to source directory before running find.
- Use `--parents` flag with cp command.
- Include `-type f` to exclude directories.
- Use `.` (current directory) as find starting point.
- Verify with find command on destination.

**🔒 File Management Best Practices Applied**:
- **Non-destructive**: Original files remain untouched.
- **Selective Copying**: Only targeted file types copied.
- **Structure Preservation**: Directory hierarchy maintained.
- **Verification**: Confirmed operation success.
- **Isolation**: Copied to separate location for investigation.

**⚠️ Important Concepts**:
- **find Command**: Powerful file search utility.
- **--parents Flag**: Preserves directory structure in cp.
- **Relative vs Absolute**: Affects destination structure.
- **-exec**: Executes command for each found file.
- **Type Filtering**: Distinguishes files from directories.

---

## ⚠️ Important Production Notes

🔧 **Relative Paths**: Essential for preserving directory structure correctly.
🔐 **Security Isolation**: Copying suspicious files to separate location for analysis.
📊 **Selective Copy**: Only specific file types, not entire directory.
🛡️ **Verification**: Always verify file counts match source.
🗄️ **Backup**: Consider creating backup before investigation.
📝 **Documentation**: Document findings from security audit.
🔍 **Analysis**: Investigate copied files for malicious content.
⚙️ **Cleanup**: Plan cleanup strategy after investigation.

**find Command Reference**:
```bash
# Basic find operations
find /path -name "pattern"           # Find by name
find /path -type f                   # Find files only
find /path -type d                   # Find directories only
find /path -name "*.php"             # Find PHP files
find /path -iname "*.PHP"            # Case-insensitive

# Find with actions
find /path -name "*.php" -delete     # Delete found files
find /path -name "*.php" -ls         # List found files
find /path -name "*.php" -exec cmd {} \;  # Execute command

# Find with multiple conditions
find /path -type f -name "*.php" -size +1M    # Files > 1MB
find /path -type f -mtime -7                   # Modified in last 7 days
find /path -type f -user www-data              # Owned by user
```

**cp --parents Examples**:
```bash
# Without --parents (flat copy)
cd /var/www/html/beta
cp ./config/database.php /backup/
# Result: /backup/database.php

# With --parents (structure preserved)
cd /var/www/html/beta
cp --parents ./config/database.php /backup/
# Result: /backup/./config/database.php
```

**Security Recommendations**:
- Isolate suspicious files before analysis
- Use separate investigation environment
- Document all findings and actions taken
- Implement file integrity monitoring
- Regular security audits and scans
- Maintain backup before cleanup
- Review web server logs for attack patterns
- Update and patch vulnerable code

**Alternative Methods**:
```bash
# Using rsync (preserves more attributes)
cd /var/www/html/beta
sudo find . -type f -name "*.php" -print0 | \
  sudo rsync -av --files-from=- --from0 . /beta/

# Using tar (preserves permissions and timestamps)
cd /var/www/html/beta
sudo find . -type f -name "*.php" | \
  sudo tar -cf - -T - | sudo tar -xf - -C /beta/

# Using cpio
cd /var/www/html/beta
sudo find . -type f -name "*.php" | \
  sudo cpio -pdm /beta/
```

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Found all PHP files in /var/www/html/beta
- Copied files to /beta preserving directory structure
- Did NOT copy entire directory content
- Only .php files copied, not other file types
- Verified successful copy operation

**🔍 Verification Completed**:
- Destination directory created: /beta
- PHP files copied with directory structure preserved
- File count matches between source and destination
- Directory structure verified with ls -R
- All PHP files listed and confirmed

**🚀 Next Steps** (Optional):
- Analyze copied PHP files for malicious content
- Document security findings
- Plan remediation strategy
- Update security policies
- Implement file integrity monitoring
- Clean up infected files after investigation

**🔒 Configuration Validation**:
- ✅ All .php files copied
- ✅ Directory structure preserved
- ✅ Original files unchanged
- ✅ Isolated location for investigation
- ✅ No entire directory copy (only PHP files)
- ✅ Verification confirms successful operation

**📊 Configuration Summary**:
- **Server**: App Server 2 (stapp02)
- **Source**: /var/www/html/beta
- **Destination**: /beta
- **File Type**: .php files only
- **Method**: find + cp --parents
- **Structure**: Preserved with relative paths

**🔍 Key Learning Points**:
- `find` command locates files by type and name
- `cp --parents` preserves directory structure
- Working from source directory ensures relative paths
- `-type f` filters only files, not directories
- `-exec` executes command for each found file
- Verification is critical for security operations
