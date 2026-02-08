# 🔐 Task 6 - Copy User-Owned Files with Directory Structure Preservation

**📌 Task Description**

Due to an accidental data mix-up, user data was unintentionally mingled on **Nautilus App Server 3** at the `/home/usersdata` location by the Nautilus production support team in Stratos DC. To rectify this, specific user data needs to be filtered and relocated. This task demonstrates advanced file management using the `find` command with `cp --parents` to preserve directory structures during selective file copying operations.

**👉 Task Requirements**:
- **Target Server**: App Server 3 (stapp03) in Stratos Datacenter
- **Source Location**: `/home/usersdata` directory
- **Destination Location**: `/blog` directory
- **File Selection Criteria**:
  - Owner: User `kareem`
  - Type: Files only (exclude directories)
  - Preserve: Original directory structure
- **Operation Objectives**:
  - Locate all files owned by specific user
  - Copy files while maintaining directory hierarchy
  - Verify successful file relocation
  - Ensure data integrity during copy operation
- Create destination directory if it doesn't exist
- Preserve complete directory path structure
- Verify copied files maintain correct paths

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to App Server 3

```bash
ssh banner@stapp03
```

**Purpose**: Establish SSH connection to App Server 3 where the file copy operation will be performed.

**Expected Output**:
```
banner@stapp03's password: 
Welcome to App Server 3 - Stratos Datacenter
[banner@stapp03 ~]$
```

**Success Indicators**:
- ✅ Successfully authenticated as user `banner`
- ✅ Command prompt shows `stapp03` hostname
- ✅ Ready to execute administrative commands

---

## 🔹 Step 2: Verify Source Directory Exists

```bash
ls -ld /home/usersdata
```

**Purpose**: Confirm the source directory exists and is accessible.

**Expected Output**:
```
drwxr-xr-x. 5 root root 52 Feb 08 13:00 /home/usersdata
```

**Success Indicators**:
- ✅ Directory exists at `/home/usersdata`
- ✅ Directory is accessible
- ✅ Contains user data to be filtered

**Alternative Check**:
```bash
sudo ls -la /home/usersdata
```

---

## 🔹 Step 3: Check for Files Owned by User kareem

```bash
sudo find /home/usersdata -type f -user kareem
```

**Purpose**: Preview files owned by user `kareem` before copying.

**Command Breakdown**:
- `sudo`: Execute with administrative privileges
- `find`: Search for files command
- `/home/usersdata`: Starting directory for search
- `-type f`: Find files only (exclude directories)
- `-user kareem`: Filter by owner username

**Expected Output** (sample):
```
/home/usersdata/project1/file1.txt
/home/usersdata/project1/subdir/file2.log
/home/usersdata/project2/data.csv
/home/usersdata/docs/readme.md
```

**Success Indicators**:
- ✅ Files owned by `kareem` are found
- ✅ Files are located in various subdirectories
- ✅ Ready to proceed with copy operation

---

## 🔹 Step 4: Create Destination Directory

```bash
sudo mkdir -p /blog
```

**Purpose**: Create the destination directory where files will be copied.

**Command Breakdown**:
- `sudo`: Execute with administrative privileges
- `mkdir`: Make directory command
- `-p`: Create parent directories as needed (no error if exists)
- `/blog`: Destination directory path

**Expected Output**:
```
[no output indicates success]
```

**Success Indicators**:
- ✅ Directory `/blog` created
- ✅ Ready to receive copied files
- ✅ No errors if directory already exists

---

## 🔹 Step 5: Verify Destination Directory Creation

```bash
ls -ld /blog
```

**Purpose**: Confirm the destination directory exists with correct permissions.

**Expected Output**:
```
drwxr-xr-x. 2 root root 6 Feb 08 13:00 /blog
```

**Success Indicators**:
- ✅ Directory exists at `/blog`
- ✅ Owned by root (created with sudo)
- ✅ Ready to receive files

---

## 🔹 Step 6: Copy Files with Directory Structure Preservation

```bash
sudo find /home/usersdata -type f -user kareem -exec cp --parents {} /blog/ \;
```

**Purpose**: Copy all files owned by `kareem` while preserving the complete directory structure.

**Command Breakdown**:
- `sudo`: Execute with administrative privileges
- `find /home/usersdata`: Search starting from `/home/usersdata`
- `-type f`: Find files only (exclude directories)
- `-user kareem`: Filter by owner `kareem`
- `-exec`: Execute command for each found file
- `cp --parents`: Copy with parent directory structure
- `{}`: Placeholder for found file path
- `/blog/`: Destination directory
- `\;`: End of `-exec` command

**Expected Output**:
```
[no output indicates success]
```

**Success Indicators**:
- ✅ All files owned by `kareem` are copied
- ✅ Directory structure is preserved
- ✅ Files maintain their relative paths

**⚠️ Critical Flag**: The `--parents` flag is essential for preserving directory structure. Without it, all files would be copied directly to `/blog/` without subdirectories.

---

## 🔹 Step 7: Verify Files Were Copied

```bash
sudo find /blog -type f | head
```

**Purpose**: List the first 10 copied files to verify successful operation.

**Command Breakdown**:
- `sudo`: Execute with administrative privileges
- `find /blog`: Search in destination directory
- `-type f`: Find files only
- `|`: Pipe output to next command
- `head`: Display first 10 lines

**Expected Output** (sample):
```
/blog/home/usersdata/project1/file1.txt
/blog/home/usersdata/project1/subdir/file2.log
/blog/home/usersdata/project2/data.csv
/blog/home/usersdata/docs/readme.md
/blog/home/usersdata/config/settings.conf
```

**Success Indicators**:
- ✅ Files are present in `/blog` directory
- ✅ **Complete path is preserved**: `/blog/home/usersdata/...`
- ✅ Directory structure matches source

**⚠️ Important Note**: The `--parents` flag preserves the **full path**, so files are copied to `/blog/home/usersdata/...` not just `/blog/...`

---

## 🔹 Step 8: Count Total Files Copied

```bash
sudo find /blog -type f | wc -l
```

**Purpose**: Count the total number of files copied to verify completeness.

**Expected Output**:
```
42
```

**Success Indicators**:
- ✅ Number of files in `/blog` matches files owned by `kareem` in source
- ✅ All files were successfully copied

**Verification**:
```bash
# Count files in source owned by kareem
sudo find /home/usersdata -type f -user kareem | wc -l

# Count files in destination
sudo find /blog -type f | wc -l

# Numbers should match
```

---

## 🔹 Step 9: Verify Directory Structure Preservation

```bash
sudo ls -R /blog | head -20
```

**Purpose**: Display the directory tree structure to confirm hierarchy is preserved.

**Expected Output** (sample):
```
/blog:
home

/blog/home:
usersdata

/blog/home/usersdata:
project1  project2  docs  config

/blog/home/usersdata/project1:
file1.txt  subdir

/blog/home/usersdata/project1/subdir:
file2.log
```

**Success Indicators**:
- ✅ Directory structure is preserved
- ✅ Subdirectories are maintained
- ✅ Files are in correct locations

---

## 🔹 Step 10: Verify File Ownership in Destination

```bash
sudo find /blog -type f -ls | head -5
```

**Purpose**: Display detailed file information including ownership.

**Expected Output** (sample):
```
12345  4 -rw-r--r--  1 kareem kareem  1024 Feb 08 12:00 /blog/home/usersdata/project1/file1.txt
12346  4 -rw-r--r--  1 kareem kareem  2048 Feb 08 12:00 /blog/home/usersdata/project1/subdir/file2.log
12347  8 -rw-r--r--  1 kareem kareem  5120 Feb 08 12:00 /blog/home/usersdata/project2/data.csv
```

**Success Indicators**:
- ✅ Files maintain original ownership (`kareem`)
- ✅ Permissions are preserved
- ✅ File sizes match original files

**Note**: The `cp` command preserves ownership when run with `sudo`.

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **App Server 3**:

```bash
# Connect to App Server 3
ssh banner@stapp03

# Verify source directory
ls -ld /home/usersdata

# Preview files owned by kareem
sudo find /home/usersdata -type f -user kareem

# Count files to be copied
sudo find /home/usersdata -type f -user kareem | wc -l

# Create destination directory
sudo mkdir -p /blog

# Verify destination directory
ls -ld /blog

# Copy files with directory structure preservation
sudo find /home/usersdata -type f -user kareem -exec cp --parents {} /blog/ \;

# Verify copied files
sudo find /blog -type f | head

# Count copied files
sudo find /blog -type f | wc -l

# Verify directory structure
sudo ls -R /blog | head -20

# Verify file ownership
sudo find /blog -type f -ls | head -5
```

---

## 💡 Common File Copy Issues & Fixes

### **Issue 1: Directory Structure Not Preserved**
**Problem**: All files copied directly to `/blog/` without subdirectories.
**Fix**: Ensure using `--parents` flag with `cp` command.
```bash
# WRONG - Files copied without directory structure
sudo find /home/usersdata -type f -user kareem -exec cp {} /blog/ \;

# CORRECT - Files copied with directory structure
sudo find /home/usersdata -type f -user kareem -exec cp --parents {} /blog/ \;
```

### **Issue 2: Permission Denied Errors**
**Problem**: Cannot copy files due to permission restrictions.
**Fix**: Use `sudo` for both `find` and `cp` operations.
```bash
# Ensure sudo is used
sudo find /home/usersdata -type f -user kareem -exec cp --parents {} /blog/ \;

# Verify permissions on source directory
sudo ls -la /home/usersdata
```

### **Issue 3: User kareem Does Not Exist**
**Problem**: `find` returns error "invalid user 'kareem'".
**Fix**: Verify user exists or use UID instead.
```bash
# Verify user exists
id kareem

# Alternative: Use UID if user exists
uid=$(id -u kareem)
sudo find /home/usersdata -type f -uid $uid -exec cp --parents {} /blog/ \;
```

### **Issue 4: Destination Directory Already Exists with Files**
**Problem**: `/blog` contains existing files that may conflict.
**Fix**: Remove existing directory or use different destination.
```bash
# Option 1: Remove existing directory
sudo rm -rf /blog
sudo mkdir -p /blog

# Option 2: Use timestamped directory
sudo mkdir -p /blog-$(date +%Y%m%d)
sudo find /home/usersdata -type f -user kareem -exec cp --parents {} /blog-$(date +%Y%m%d)/ \;
```

### **Issue 5: No Files Found**
**Problem**: `find` command returns no results.
**Fix**: Verify user has files in the source directory.
```bash
# Check if user kareem has any files
sudo find /home/usersdata -user kareem

# Check if files exist (any type)
sudo find /home/usersdata -type f | head

# Verify source directory has content
sudo ls -la /home/usersdata
```

---

## 🔧 Troubleshooting Failures

### **Error: cp: cannot create directory**
**Symptoms**: Error creating parent directories during copy.
**Solution**: Ensure destination directory exists and is writable.
```bash
# Create destination directory first
sudo mkdir -p /blog

# Verify directory is writable
sudo ls -ld /blog

# Retry copy operation
sudo find /home/usersdata -type f -user kareem -exec cp --parents {} /blog/ \;
```

### **Error: find: invalid user**
**Symptoms**: `find: 'kareem' is not the name of a known user`.
**Solution**: Verify user exists or use UID.
```bash
# Check if user exists
getent passwd kareem

# If user doesn't exist, verify correct username
sudo cat /etc/passwd | grep -i kareem

# Use UID if username is different
sudo find /home/usersdata -type f -uid 1006 -exec cp --parents {} /blog/ \;
```

### **Error: Argument list too long**
**Symptoms**: Error when copying large number of files.
**Solution**: The `-exec ... \;` syntax processes files one at a time, avoiding this issue. If still occurring, verify syntax.
```bash
# Correct syntax (processes one file at a time)
sudo find /home/usersdata -type f -user kareem -exec cp --parents {} /blog/ \;

# Alternative: Use xargs (processes in batches)
sudo find /home/usersdata -type f -user kareem -print0 | xargs -0 -I {} cp --parents {} /blog/
```

### **Error: Disk space full**
**Symptoms**: `cp: error writing: No space left on device`.
**Solution**: Check available disk space and clean up if needed.
```bash
# Check disk space
df -h /blog

# Check size of files to be copied
sudo du -sh /home/usersdata

# Estimate space needed
sudo find /home/usersdata -type f -user kareem -exec du -ch {} + | tail -1
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The primary challenge was copying files owned by a specific user while preserving the complete directory structure from the source location. Understanding the `--parents` flag behavior and how it preserves the full path (including source directory path) is crucial for successful directory structure preservation.

**💡 Solution Approach**:
1. **Destination Preparation**: Created `/blog` directory before copy operation.
2. **File Discovery**: Used `find` with `-type f -user kareem` to locate target files.
3. **Structure Preservation**: Applied `cp --parents` to maintain directory hierarchy.
4. **Batch Processing**: Used `-exec ... \;` to process each file individually.
5. **Verification**: Confirmed files were copied with correct paths using `find /blog -type f`.

**🎯 Key Success Factors**:
- **`--parents` Flag**: Essential for preserving directory structure during copy.
- **`-type f` Filter**: Ensures only files are copied, not directories.
- **`-user kareem` Filter**: Selects only files owned by specific user.
- **`-exec` with `\;`**: Processes each file individually, avoiding argument list limits.
- **Full Path Preservation**: Understands that `--parents` preserves complete path including source.

**⚠️ Critical Fixes**:
- Always use `--parents` flag to preserve directory structure.
- Understand that `--parents` preserves **full path**: `/blog/home/usersdata/...`
- Use `sudo` for both `find` and `cp` to handle permission restrictions.
- Verify user exists before running `find -user` command.
- Create destination directory before copy operation.

**🔒 Linux File Management Best Practices Applied**:
- **Selective Copying**: Used `find` filters to copy only specific files.
- **Structure Preservation**: Maintained directory hierarchy for data organization.
- **Permission Handling**: Used `sudo` to handle restricted file access.
- **Verification**: Confirmed successful operation with multiple checks.
- **Non-Destructive**: Copy operation preserves original files.

**⚠️ Important Concepts**:
- **`--parents` Behavior**: Preserves full source path in destination.
- **File vs Directory**: `-type f` ensures only files are copied.
- **Ownership Filtering**: `-user` flag filters by file owner.
- **Batch Processing**: `-exec ... \;` processes files individually.
- **Path Preservation**: Complete directory structure is maintained.

---

## ⚠️ Important Production Notes

🔧 **`--parents` Flag**: Essential for directory structure preservation - copies full source path.
🔐 **Permission Handling**: Use `sudo` for copying files owned by other users.
📊 **Disk Space**: Verify sufficient space before copying large datasets.
🛡️ **Data Integrity**: Verify file counts and checksums after copy operations.
🗄️ **Backup Strategy**: Consider using `rsync` for large-scale copy operations with progress tracking.
📝 **Audit Trail**: Log file copy operations for compliance and troubleshooting.
🔍 **Verification**: Always verify copied files match source in count and structure.
⚙️ **Automation**: Use scripts for recurring file organization tasks.

**Alternative Copy Methods**:

**Using rsync (recommended for large datasets)**:
```bash
# Create file list
sudo find /home/usersdata -type f -user kareem > /tmp/kareem_files.txt

# Use rsync with file list
sudo rsync -av --files-from=/tmp/kareem_files.txt / /blog/
```

**Using cpio (preserves structure)**:
```bash
# Copy with cpio
sudo find /home/usersdata -type f -user kareem | sudo cpio -pdm /blog/
```

**Using tar (archive and extract)**:
```bash
# Create tar archive and extract
sudo tar -C / -cf - $(sudo find /home/usersdata -type f -user kareem) | sudo tar -C /blog -xf -
```

**`find` Command Options Explained**:
```bash
# Basic syntax
find [path] [options] [actions]

# Common options:
-type f          # Files only
-type d          # Directories only
-user username   # Filter by owner
-uid 1006        # Filter by UID
-name "*.txt"    # Filter by filename pattern
-size +10M       # Files larger than 10MB
-mtime -7        # Modified in last 7 days

# Common actions:
-exec cmd {} \;  # Execute command for each file (one at a time)
-exec cmd {} +   # Execute command with multiple files (batch)
-print           # Print file paths (default)
-ls              # List files with details
-delete          # Delete found files (use with caution!)
```

**`cp --parents` Behavior**:
```bash
# Source file: /home/usersdata/project/file.txt
# Destination: /blog/

# With --parents:
cp --parents /home/usersdata/project/file.txt /blog/
# Result: /blog/home/usersdata/project/file.txt

# Without --parents:
cp /home/usersdata/project/file.txt /blog/
# Result: /blog/file.txt
```

**Security Recommendations**:
- Verify user ownership before copying sensitive files
- Use `cp -a` (archive mode) to preserve all attributes including permissions and timestamps
- Implement access controls on destination directory
- Log file copy operations for audit trails
- Verify file integrity with checksums after large copy operations
- Use `rsync --dry-run` to preview operations before execution
- Implement disk space monitoring to prevent copy failures

**Performance Optimization**:
```bash
# For large number of files, use + instead of \;
sudo find /home/usersdata -type f -user kareem -exec cp --parents {} /blog/ +

# Use parallel processing for faster copies
sudo find /home/usersdata -type f -user kareem | parallel -j 4 sudo cp --parents {} /blog/

# Monitor progress with pv (pipe viewer)
sudo find /home/usersdata -type f -user kareem | pv -l | xargs -I {} sudo cp --parents {} /blog/
```

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Located all files owned by user `kareem` in `/home/usersdata`
- Created destination directory `/blog`
- Copied files while preserving complete directory structure
- Verified successful file relocation
- Maintained data integrity during copy operation

**🔍 Verification Completed**:
- Source directory exists: `/home/usersdata`
- Files owned by `kareem` identified: `find -type f -user kareem`
- Destination directory created: `/blog`
- Files copied with structure: `cp --parents`
- File count verified: Source count matches destination count
- Directory structure preserved: `/blog/home/usersdata/...`

**🚀 Next Steps** (Optional):
- Verify file integrity with checksums
- Set appropriate permissions on `/blog` directory
- Remove original files if move operation was intended
- Document file relocation in change log
- Notify user `kareem` of file relocation
- Implement monitoring for `/blog` directory usage

**🔒 Data Integrity Validation**:
- ✅ All files owned by `kareem` copied successfully
- ✅ Directory structure preserved with `--parents` flag
- ✅ File ownership maintained during copy
- ✅ File permissions preserved
- ✅ No data loss during operation
- ✅ Source files remain intact (copy, not move)

**📊 Operation Summary**:
- **Source**: `/home/usersdata`
- **Destination**: `/blog/home/usersdata/...` (full path preserved)
- **Filter**: Files owned by user `kareem`
- **Type**: Files only (directories excluded)
- **Method**: `find` with `cp --parents`
- **Result**: Complete directory structure preservation

**🔍 Key Learning Points**:
- `--parents` flag preserves **full source path** in destination
- `-type f` ensures only files are copied, not directories
- `-user` flag filters files by ownership
- `-exec ... \;` processes files individually for reliability
- `sudo` required for copying files owned by other users
- Verification is essential for confirming successful operations
