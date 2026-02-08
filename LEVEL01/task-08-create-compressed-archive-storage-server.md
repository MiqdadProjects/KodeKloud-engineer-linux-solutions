# 🔐 Task 8 - Create and Transfer Compressed Archive on Storage Server

**📌 Task Description**

Within the **Stratos DC**, the Nautilus storage server hosts a directory named `/data`, serving as a repository for various developers' non-confidential data. Developer **mariyam** has requested a copy of their data stored in `/data/mariyam`. This task demonstrates file archiving and compression using `tar` with gzip compression, which is essential for data backup, transfer, and storage optimization.

**👉 Task Requirements**:
- **Target Server**: Storage Server in Stratos Datacenter
- **Source Directory**: `/data/mariyam`
- **Archive Name**: `mariyam.tar.gz`
- **Final Location**: `/home/` directory
- **Operation Objectives**:
  - Create compressed tar archive of user data
  - Use gzip compression for space efficiency
  - Transfer archive to accessible location
  - Verify archive creation and integrity
- Create `.tar.gz` compressed archive
- Preserve directory structure and permissions
- Move archive to specified location
- Verify successful archive creation

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to Storage Server

```bash
ssh natasha@ststor01
```

**Purpose**: Establish SSH connection to the Storage Server where the data archiving will be performed.

**Expected Output**:
```
natasha@ststor01's password: 
Welcome to Storage Server - Stratos Datacenter
[natasha@ststor01 ~]$
```

**Success Indicators**:
- ✅ Successfully authenticated as user `natasha`
- ✅ Command prompt shows `ststor01` hostname
- ✅ Ready to execute administrative commands

---

## 🔹 Step 2: Verify Source Directory Exists

```bash
ls -ld /data/mariyam
```

**Purpose**: Confirm the source directory exists and is accessible.

**Expected Output**:
```
drwxr-xr-x. 5 mariyam mariyam 4096 Feb 08 14:00 /data/mariyam
```

**Success Indicators**:
- ✅ Directory exists at `/data/mariyam`
- ✅ Directory is accessible
- ✅ Contains data to be archived

**Check Directory Contents**:
```bash
sudo ls -la /data/mariyam
```

**Expected Output** (sample):
```
total 24
drwxr-xr-x. 5 mariyam mariyam 4096 Feb 08 14:00 .
drwxr-xr-x. 8 root    root    4096 Feb 08 13:00 ..
-rw-r--r--. 1 mariyam mariyam 1024 Feb 08 14:00 file1.txt
-rw-r--r--. 1 mariyam mariyam 2048 Feb 08 14:00 file2.log
drwxr-xr-x. 2 mariyam mariyam 4096 Feb 08 14:00 project1
```

---

## 🔹 Step 3: Check Available Disk Space

```bash
df -h /tmp
```

**Purpose**: Verify sufficient disk space is available for creating the archive.

**Expected Output**:
```
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        50G   10G   40G  20% /
```

**Success Indicators**:
- ✅ Sufficient space available in `/tmp`
- ✅ Can proceed with archive creation

**Estimate Archive Size**:
```bash
sudo du -sh /data/mariyam
```

**Expected Output**:
```
150M    /data/mariyam
```

---

## 🔹 Step 4: Create Compressed Archive

```bash
sudo tar -czvf /tmp/mariyam.tar.gz -C /data mariyam
```

**Purpose**: Create a gzip-compressed tar archive of the `/data/mariyam` directory.

**Command Breakdown**:
- `sudo`: Execute with administrative privileges
- `tar`: Tape archive utility
- `-c`: Create new archive
- `-z`: Compress with gzip
- `-v`: Verbose mode (show files being archived)
- `-f /tmp/mariyam.tar.gz`: Output file path
- `-C /data`: Change to `/data` directory before archiving
- `mariyam`: Directory to archive (relative to `/data`)

**Expected Output** (sample):
```
mariyam/
mariyam/file1.txt
mariyam/file2.log
mariyam/project1/
mariyam/project1/data.csv
mariyam/project1/readme.md
```

**Success Indicators**:
- ✅ Files are listed as they're archived
- ✅ No error messages
- ✅ Archive created at `/tmp/mariyam.tar.gz`

**⚠️ Important**: The `-C /data` flag changes to `/data` directory before archiving, so the archive contains `mariyam/` not `/data/mariyam/`.

---

## 🔹 Step 5: Verify Archive Creation

```bash
ls -lh /tmp/mariyam.tar.gz
```

**Purpose**: Confirm the archive file was created and check its size.

**Expected Output**:
```
-rw-r--r--. 1 root root 45M Feb 08 14:00 /tmp/mariyam.tar.gz
```

**Success Indicators**:
- ✅ Archive file exists
- ✅ File size is reasonable (compressed from 150M to ~45M)
- ✅ Owned by root (created with sudo)

**Note**: Gzip compression typically reduces file size by 60-70% for text files.

---

## 🔹 Step 6: Test Archive Integrity

```bash
sudo tar -tzf /tmp/mariyam.tar.gz | head -10
```

**Purpose**: Verify the archive is valid and list its contents.

**Command Breakdown**:
- `sudo`: Execute with administrative privileges
- `tar`: Tape archive utility
- `-t`: List contents (test mode)
- `-z`: Decompress with gzip
- `-f /tmp/mariyam.tar.gz`: Archive file to test
- `| head -10`: Show first 10 entries

**Expected Output**:
```
mariyam/
mariyam/file1.txt
mariyam/file2.log
mariyam/project1/
mariyam/project1/data.csv
mariyam/project1/readme.md
```

**Success Indicators**:
- ✅ Archive contents are listed
- ✅ Directory structure is preserved
- ✅ No corruption errors

---

## 🔹 Step 7: Move Archive to /home Directory

```bash
sudo mv /tmp/mariyam.tar.gz /home/
```

**Purpose**: Transfer the archive from `/tmp` to the `/home` directory.

**Command Breakdown**:
- `sudo`: Execute with administrative privileges
- `mv`: Move command
- `/tmp/mariyam.tar.gz`: Source file
- `/home/`: Destination directory

**Expected Output**:
```
[no output indicates success]
```

**Success Indicators**:
- ✅ File moved successfully
- ✅ No longer exists in `/tmp`
- ✅ Now located in `/home`

---

## 🔹 Step 8: Verify Archive in Destination

```bash
ls -lh /home/mariyam.tar.gz
```

**Purpose**: Confirm the archive is now in the `/home` directory with correct size.

**Expected Output**:
```
-rw-r--r--. 1 root root 45M Feb 08 14:00 /home/mariyam.tar.gz
```

**Success Indicators**:
- ✅ Archive exists at `/home/mariyam.tar.gz`
- ✅ File size matches original
- ✅ Permissions preserved

---

## 🔹 Step 9: Verify Source File No Longer in /tmp

```bash
ls -l /tmp/mariyam.tar.gz
```

**Purpose**: Confirm the file was moved (not copied) from `/tmp`.

**Expected Output**:
```
ls: cannot access '/tmp/mariyam.tar.gz': No such file or directory
```

**Success Indicators**:
- ✅ File no longer exists in `/tmp`
- ✅ Move operation (not copy) was successful
- ✅ Disk space in `/tmp` is freed

---

## 🔹 Step 10: Final Verification - List Archive Contents

```bash
sudo tar -tzf /home/mariyam.tar.gz | wc -l
```

**Purpose**: Count total number of files and directories in the archive.

**Expected Output**:
```
42
```

**Success Indicators**:
- ✅ Archive contains expected number of entries
- ✅ Archive is accessible and valid
- ✅ Ready for delivery to developer

**Detailed Contents Verification**:
```bash
sudo tar -tzf /home/mariyam.tar.gz
```

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **Storage Server**:

```bash
# Connect to Storage Server
ssh natasha@ststor01

# Verify source directory
ls -ld /data/mariyam

# Check directory size
sudo du -sh /data/mariyam

# Check available disk space
df -h /tmp

# Create compressed archive
sudo tar -czvf /tmp/mariyam.tar.gz -C /data mariyam

# Verify archive creation
ls -lh /tmp/mariyam.tar.gz

# Test archive integrity
sudo tar -tzf /tmp/mariyam.tar.gz | head -10

# Move archive to /home
sudo mv /tmp/mariyam.tar.gz /home/

# Verify archive in destination
ls -lh /home/mariyam.tar.gz

# Verify source removed from /tmp
ls -l /tmp/mariyam.tar.gz

# Count files in archive
sudo tar -tzf /home/mariyam.tar.gz | wc -l
```

---

## 💡 Common Archive Creation Issues & Fixes

### **Issue 1: Permission Denied**
**Problem**: Cannot create archive due to permission restrictions.
**Fix**: Use `sudo` for accessing restricted directories.
```bash
# Ensure sudo is used
sudo tar -czvf /tmp/mariyam.tar.gz -C /data mariyam

# Verify permissions on source directory
sudo ls -ld /data/mariyam
```

### **Issue 2: Archive Contains Full Path**
**Problem**: Archive contains `/data/mariyam/` instead of just `mariyam/`.
**Fix**: Use `-C` flag to change directory before archiving.
```bash
# WRONG - Archive contains full path
sudo tar -czvf /tmp/mariyam.tar.gz /data/mariyam

# CORRECT - Archive contains relative path
sudo tar -czvf /tmp/mariyam.tar.gz -C /data mariyam
```

### **Issue 3: No Space Left on Device**
**Problem**: Insufficient disk space to create archive.
**Fix**: Check disk space and use alternative location or clean up.
```bash
# Check disk space
df -h /tmp

# Use alternative location with more space
sudo tar -czvf /var/tmp/mariyam.tar.gz -C /data mariyam

# Or clean up /tmp
sudo rm -rf /tmp/old_files
```

### **Issue 4: Archive Corruption**
**Problem**: Archive is corrupted or cannot be extracted.
**Fix**: Verify archive integrity and recreate if needed.
```bash
# Test archive integrity
sudo tar -tzf /tmp/mariyam.tar.gz

# If corrupted, recreate
sudo rm /tmp/mariyam.tar.gz
sudo tar -czvf /tmp/mariyam.tar.gz -C /data mariyam
```

### **Issue 5: File Already Exists**
**Problem**: Archive file already exists at destination.
**Fix**: Remove existing file or use different name.
```bash
# Check if file exists
ls -l /home/mariyam.tar.gz

# Option 1: Remove existing file
sudo rm /home/mariyam.tar.gz
sudo mv /tmp/mariyam.tar.gz /home/

# Option 2: Use timestamped name
sudo tar -czvf /tmp/mariyam-$(date +%Y%m%d).tar.gz -C /data mariyam
```

---

## 🔧 Troubleshooting Failures

### **Error: Directory Not Found**
**Symptoms**: `tar: /data/mariyam: Cannot stat: No such file or directory`.
**Solution**: Verify directory path and existence.
```bash
# Check if directory exists
ls -ld /data/mariyam

# Verify correct path
sudo find /data -type d -name "mariyam"

# Use correct path in tar command
sudo tar -czvf /tmp/mariyam.tar.gz -C /data mariyam
```

### **Error: Cannot Open Archive**
**Symptoms**: `tar: Cannot open: Permission denied`.
**Solution**: Ensure using sudo and destination is writable.
```bash
# Use sudo
sudo tar -czvf /tmp/mariyam.tar.gz -C /data mariyam

# Verify /tmp is writable
ls -ld /tmp

# Use alternative location if needed
sudo tar -czvf /var/tmp/mariyam.tar.gz -C /data mariyam
```

### **Error: gzip Not Found**
**Symptoms**: `tar: Cannot use -z: gzip not found`.
**Solution**: Install gzip or use alternative compression.
```bash
# Install gzip
sudo yum install gzip  # RHEL/CentOS
sudo apt install gzip  # Debian/Ubuntu

# Or use bzip2 compression
sudo tar -cjvf /tmp/mariyam.tar.bz2 -C /data mariyam
```

### **Error: Move Failed**
**Symptoms**: `mv: cannot move '/tmp/mariyam.tar.gz': Permission denied`.
**Solution**: Use sudo for moving to restricted directories.
```bash
# Use sudo for move operation
sudo mv /tmp/mariyam.tar.gz /home/

# Verify destination directory permissions
ls -ld /home
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The primary challenge was creating a compressed archive with the correct directory structure (relative path instead of absolute path) and transferring it to the specified location. Understanding the `-C` flag behavior and the difference between absolute and relative paths in tar archives is crucial.

**💡 Solution Approach**:
1. **Directory Verification**: Confirmed source directory exists and is accessible.
2. **Disk Space Check**: Verified sufficient space for archive creation.
3. **Relative Path Archiving**: Used `-C /data` to change directory before archiving.
4. **Compression**: Applied gzip compression with `-z` flag for space efficiency.
5. **Temporary Location**: Created archive in `/tmp` first for safety.
6. **Transfer**: Moved archive to `/home` directory as specified.
7. **Verification**: Confirmed archive integrity and final location.

**🎯 Key Success Factors**:
- **`-C` Flag Usage**: Changed to parent directory to create relative path archive.
- **Gzip Compression**: Used `-z` flag for efficient compression.
- **Verbose Mode**: Used `-v` flag to monitor archiving progress.
- **Temporary Storage**: Created archive in `/tmp` before moving to final location.
- **Integrity Testing**: Verified archive contents before transfer.

**⚠️ Critical Fixes**:
- Always use `-C` flag to avoid absolute paths in archives.
- Verify disk space before creating large archives.
- Test archive integrity with `tar -tzf` before transfer.
- Use `mv` instead of `cp` to save disk space.
- Apply `sudo` for accessing restricted directories.

**🔒 Linux Archive Management Best Practices Applied**:
- **Relative Paths**: Used `-C` to create portable archives with relative paths.
- **Compression**: Applied gzip compression to reduce storage and transfer time.
- **Verification**: Tested archive integrity before delivery.
- **Temporary Storage**: Used `/tmp` for intermediate operations.
- **Permission Handling**: Used `sudo` for restricted directory access.

**⚠️ Important Concepts**:
- **`-C` Flag**: Changes directory before archiving (creates relative paths).
- **Compression Ratio**: Gzip typically achieves 60-70% compression for text files.
- **Archive Portability**: Relative paths make archives portable across systems.
- **Move vs Copy**: `mv` is more efficient than `cp` for large files.
- **Integrity Testing**: Always verify archives before distribution.

---

## ⚠️ Important Production Notes

🔧 **Relative Paths**: Always use `-C` flag to create portable archives with relative paths.
🔐 **Compression Selection**: Use gzip (-z) for speed, bzip2 (-j) for better compression, xz (-J) for best compression.
📊 **Disk Space**: Verify sufficient space before creating large archives.
🛡️ **Integrity Verification**: Always test archives with `tar -tzf` before distribution.
🗄️ **Backup Strategy**: Include checksums (md5sum/sha256sum) for archive verification.
📝 **Documentation**: Document archive contents and creation date.
🔍 **Monitoring**: Monitor disk usage during archive creation.
⚙️ **Automation**: Use scripts for recurring archive operations.

**tar Command Options Explained**:
```bash
# Create archive
-c, --create              Create new archive

# Compression options
-z, --gzip                Compress with gzip (.tar.gz)
-j, --bzip2               Compress with bzip2 (.tar.bz2)
-J, --xz                  Compress with xz (.tar.xz)

# Verbose and file options
-v, --verbose             Show files being processed
-f, --file=ARCHIVE        Archive file name

# Directory options
-C, --directory=DIR       Change to directory before operation

# Extract options
-x, --extract             Extract files from archive
-t, --list                List archive contents

# Preserve options
-p, --preserve-permissions Preserve file permissions
--preserve                Preserve all attributes
```

**Compression Comparison**:
```bash
# gzip - Fast, moderate compression
sudo tar -czvf archive.tar.gz -C /data mariyam

# bzip2 - Slower, better compression
sudo tar -cjvf archive.tar.bz2 -C /data mariyam

# xz - Slowest, best compression
sudo tar -cJvf archive.tar.xz -C /data mariyam

# No compression - Fastest, largest size
sudo tar -cvf archive.tar -C /data mariyam
```

**Archive Verification Methods**:
```bash
# List contents
sudo tar -tzf mariyam.tar.gz

# List with details
sudo tar -tzvf mariyam.tar.gz

# Count files
sudo tar -tzf mariyam.tar.gz | wc -l

# Extract to test (in temp location)
mkdir /tmp/test
sudo tar -xzf /home/mariyam.tar.gz -C /tmp/test
ls -la /tmp/test/mariyam

# Generate checksum
md5sum /home/mariyam.tar.gz > /home/mariyam.tar.gz.md5
sha256sum /home/mariyam.tar.gz > /home/mariyam.tar.gz.sha256
```

**Extraction Commands** (for reference):
```bash
# Extract to current directory
sudo tar -xzvf /home/mariyam.tar.gz

# Extract to specific directory
sudo tar -xzvf /home/mariyam.tar.gz -C /destination/path

# Extract specific file
sudo tar -xzvf /home/mariyam.tar.gz mariyam/file1.txt

# Extract and preserve permissions
sudo tar -xzvpf /home/mariyam.tar.gz
```

**Security Recommendations**:
- Verify archive contents before distribution
- Use checksums (md5sum, sha256sum) for integrity verification
- Encrypt sensitive archives with gpg or openssl
- Set appropriate permissions on archives
- Document archive contents and purpose
- Implement access controls on archive storage locations
- Regularly test archive restoration procedures

**Performance Optimization**:
```bash
# Use pigz for parallel gzip compression (faster)
sudo tar -cv -C /data mariyam | pigz > /tmp/mariyam.tar.gz

# Exclude unnecessary files
sudo tar -czvf /tmp/mariyam.tar.gz -C /data \
  --exclude='*.log' \
  --exclude='*.tmp' \
  mariyam

# Show progress with pv
sudo tar -czf - -C /data mariyam | pv > /tmp/mariyam.tar.gz
```

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Created compressed archive `mariyam.tar.gz` of `/data/mariyam` directory
- Applied gzip compression for space efficiency
- Transferred archive to `/home/` directory
- Verified archive integrity and location
- Preserved directory structure with relative paths

**🔍 Verification Completed**:
- Source directory exists: `/data/mariyam`
- Archive created: `/tmp/mariyam.tar.gz`
- Archive integrity tested: `tar -tzf` successful
- Archive moved to destination: `/home/mariyam.tar.gz`
- Source removed from /tmp: Move operation confirmed
- Final location verified: `ls -lh /home/mariyam.tar.gz`

**🚀 Next Steps** (Optional):
- Generate checksum for archive verification
- Set appropriate permissions on archive
- Notify developer mariyam of archive availability
- Document archive creation in change log
- Schedule archive cleanup after delivery
- Implement automated archiving for recurring requests

**🔒 Data Integrity Validation**:
- ✅ Archive created successfully with gzip compression
- ✅ Directory structure preserved (relative paths)
- ✅ Archive integrity verified with `tar -tzf`
- ✅ File permissions preserved in archive
- ✅ Archive transferred to specified location
- ✅ Original data remains intact in `/data/mariyam`

**📊 Operation Summary**:
- **Source**: `/data/mariyam`
- **Archive**: `mariyam.tar.gz` (gzip compressed)
- **Temporary Location**: `/tmp/mariyam.tar.gz`
- **Final Location**: `/home/mariyam.tar.gz`
- **Compression**: gzip (typically 60-70% reduction)
- **Directory Structure**: Relative path (`mariyam/` not `/data/mariyam/`)

**🔍 Key Learning Points**:
- `-C` flag creates archives with relative paths (portable)
- `-z` flag applies gzip compression for space efficiency
- `-v` flag provides visibility into archiving process
- `mv` is more efficient than `cp` for large files
- Always test archive integrity before distribution
- Temporary storage (`/tmp`) is useful for intermediate operations
