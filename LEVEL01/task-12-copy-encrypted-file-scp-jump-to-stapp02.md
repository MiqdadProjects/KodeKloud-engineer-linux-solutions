# 🔐 Task 12 - Copy Encrypted File from Jump Host to App Server 2

**📌 Task Description**

A Nautilus developer has stored confidential data on the **jump host** within Stratos DC. To ensure security and compliance, this data must be transferred to one of the app servers. Given developers lack direct access to these servers, the system admin team has been enlisted for assistance. The `scp` (secure copy) command provides encrypted file transfer over SSH, making it ideal for transferring sensitive data between servers.

**👉 Task Requirements**:
- **Source Server**: Jump Host (thor@jump_host)
- **Source File**: `/tmp/nautilus.txt.gpg`
- **Destination Server**: App Server 2 (stapp02)
- **Destination Directory**: `/home/opt/`
- **Operation Objectives**:
  - Securely transfer encrypted file between servers
  - Use SCP for encrypted file transfer
  - Preserve file integrity during transfer
  - Verify successful file transfer
- Copy file using secure copy protocol
- Maintain file permissions and attributes
- Verify file exists at destination

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Verify Source File Exists on Jump Host

```bash
ls -l /tmp/nautilus.txt.gpg
```

**Purpose**: Confirm the encrypted file exists on the jump host before transfer.

**Expected Output**:
```
-rw-r--r-- 1 thor thor 1024 Feb 08 14:00 /tmp/nautilus.txt.gpg
```

**Success Indicators**:
- ✅ File exists at `/tmp/nautilus.txt.gpg`
- ✅ File is readable
- ✅ Ready for transfer

---

## 🔹 Step 2: Verify Destination Directory Exists (Optional)

```bash
ssh steve@stapp02 "ls -ld /home/opt"
```

**Purpose**: Confirm the destination directory exists on App Server 2.

**Expected Output**:
```
drwxr-xr-x 2 root root 4096 Feb 08 14:00 /home/opt
```

**Success Indicators**:
- ✅ Directory exists at `/home/opt`
- ✅ Directory is accessible
- ✅ Ready to receive file

**Note**: If directory doesn't exist, create it first:
```bash
ssh steve@stapp02 "sudo mkdir -p /home/opt"
```

---

## 🔹 Step 3: Copy File Using SCP

```bash
scp /tmp/nautilus.txt.gpg steve@stapp02:/home/opt/
```

**Purpose**: Securely copy the encrypted file from jump host to App Server 2.

**Command Breakdown**:
- `scp`: Secure copy command (uses SSH protocol)
- `/tmp/nautilus.txt.gpg`: Source file path (on jump host)
- `steve@stapp02`: Destination user and hostname
- `:/home/opt/`: Destination directory path

**Expected Output**:
```
steve@stapp02's password: 
nautilus.txt.gpg                              100% 1024     1.0KB/s   00:00
```

**Success Indicators**:
- ✅ Password prompt appears
- ✅ Transfer progress shows 100%
- ✅ File size and transfer speed displayed
- ✅ No error messages

**⚠️ Important**: You'll be prompted for the password of user `steve` on stapp02.

---

## 🔹 Step 4: Verify File Transfer Completion

```bash
echo $?
```

**Purpose**: Check the exit status of the scp command.

**Expected Output**:
```
0
```

**Success Indicators**:
- ✅ Exit status is 0 (success)
- ✅ Transfer completed without errors

**Note**: Non-zero exit status indicates an error occurred.

---

## 🔹 Step 5: Connect to App Server 2

```bash
ssh steve@stapp02
```

**Purpose**: Establish SSH connection to App Server 2 to verify file transfer.

**Expected Output**:
```
steve@stapp02's password: 
Welcome to App Server 2 - Stratos Datacenter
[steve@stapp02 ~]$
```

**Success Indicators**:
- ✅ Successfully authenticated as user `steve`
- ✅ Command prompt shows `stapp02` hostname
- ✅ Ready to verify file

---

## 🔹 Step 6: Navigate to Destination Directory

```bash
cd /home/opt
```

**Purpose**: Change to the directory where the file was copied.

**Expected Output**:
```
[steve@stapp02 opt]$
```

**Success Indicators**:
- ✅ Directory change successful
- ✅ Prompt shows current directory
- ✅ Ready to list files

---

## 🔹 Step 7: List Files in Destination Directory

```bash
ls -l
```

**Purpose**: Verify the file exists in the destination directory.

**Expected Output**:
```
total 4
-rw-r--r-- 1 steve steve 1024 Feb 08 14:00 nautilus.txt.gpg
```

**Success Indicators**:
- ✅ File `nautilus.txt.gpg` is present
- ✅ File size matches source (1024 bytes)
- ✅ Ownership is `steve:steve`
- ✅ Transfer successful

---

## 🔹 Step 8: Verify File Integrity with Checksum (Optional)

**On Jump Host**:
```bash
md5sum /tmp/nautilus.txt.gpg
```

**Expected Output**:
```
a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6  /tmp/nautilus.txt.gpg
```

**On App Server 2**:
```bash
md5sum /home/opt/nautilus.txt.gpg
```

**Expected Output**:
```
a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6  /home/opt/nautilus.txt.gpg
```

**Success Indicators**:
- ✅ Checksums match on both servers
- ✅ File integrity verified
- ✅ No corruption during transfer

---

## 🔹 Step 9: Check File Permissions

```bash
ls -l /home/opt/nautilus.txt.gpg
```

**Purpose**: Verify file permissions were preserved during transfer.

**Expected Output**:
```
-rw-r--r-- 1 steve steve 1024 Feb 08 14:00 /home/opt/nautilus.txt.gpg
```

**Success Indicators**:
- ✅ Permissions: `-rw-r--r--` (644)
- ✅ Owner: `steve`
- ✅ Group: `steve`
- ✅ File attributes preserved

---

## 🔹 Step 10: Verify File Type (Optional)

```bash
file /home/opt/nautilus.txt.gpg
```

**Purpose**: Confirm the file is a GPG encrypted file.

**Expected Output**:
```
/home/opt/nautilus.txt.gpg: GPG encrypted data
```

**Success Indicators**:
- ✅ File is recognized as GPG encrypted
- ✅ File type is correct
- ✅ Transfer maintained file format

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **Jump Host** and **App Server 2**:

```bash
# On Jump Host - Verify source file
ls -l /tmp/nautilus.txt.gpg

# On Jump Host - Check destination directory (optional)
ssh steve@stapp02 "ls -ld /home/opt"

# On Jump Host - Copy file to App Server 2
scp /tmp/nautilus.txt.gpg steve@stapp02:/home/opt/

# On Jump Host - Verify transfer success
echo $?

# On Jump Host - Get source file checksum (optional)
md5sum /tmp/nautilus.txt.gpg

# Connect to App Server 2
ssh steve@stapp02

# On App Server 2 - Navigate to destination
cd /home/opt

# On App Server 2 - List files
ls -l

# On App Server 2 - Verify file exists
ls -l nautilus.txt.gpg

# On App Server 2 - Check file type (optional)
file nautilus.txt.gpg

# On App Server 2 - Verify checksum (optional)
md5sum nautilus.txt.gpg
```

---

## 💡 Common SCP Transfer Issues & Fixes

### **Issue 1: Permission Denied**
**Problem**: Cannot write to destination directory.
**Fix**: Ensure destination directory is writable or use sudo.
```bash
# Check destination permissions
ssh steve@stapp02 "ls -ld /home/opt"

# Create directory with proper permissions
ssh steve@stapp02 "sudo mkdir -p /home/opt && sudo chown steve:steve /home/opt"

# Retry transfer
scp /tmp/nautilus.txt.gpg steve@stapp02:/home/opt/
```

### **Issue 2: Connection Refused**
**Problem**: Cannot connect to destination server.
**Fix**: Verify SSH service is running and hostname is correct.
```bash
# Test SSH connection
ssh steve@stapp02

# Verify hostname
ping stapp02

# Check SSH service on destination
ssh steve@stapp02 "sudo systemctl status sshd"
```

### **Issue 3: File Already Exists**
**Problem**: File exists at destination and transfer fails.
**Fix**: Remove existing file or use different name.
```bash
# Remove existing file
ssh steve@stapp02 "rm /home/opt/nautilus.txt.gpg"

# Or copy with different name
scp /tmp/nautilus.txt.gpg steve@stapp02:/home/opt/nautilus.txt.gpg.new
```

### **Issue 4: Authentication Failed**
**Problem**: Wrong password or SSH key issues.
**Fix**: Verify credentials and SSH key configuration.
```bash
# Verify user exists on destination
ssh steve@stapp02 "whoami"

# Use verbose mode to debug
scp -v /tmp/nautilus.txt.gpg steve@stapp02:/home/opt/

# Use SSH key if configured
scp -i ~/.ssh/id_rsa /tmp/nautilus.txt.gpg steve@stapp02:/home/opt/
```

### **Issue 5: Transfer Interrupted**
**Problem**: Network issue or timeout during transfer.
**Fix**: Retry transfer or use rsync for resume capability.
```bash
# Retry scp
scp /tmp/nautilus.txt.gpg steve@stapp02:/home/opt/

# Use rsync for large files (supports resume)
rsync -avz --progress /tmp/nautilus.txt.gpg steve@stapp02:/home/opt/
```

---

## 🔧 Troubleshooting Failures

### **Error: No Such File or Directory (Source)**
**Symptoms**: `scp: /tmp/nautilus.txt.gpg: No such file or directory`.
**Solution**: Verify source file path and existence.
```bash
# Check if file exists
ls -l /tmp/nautilus.txt.gpg

# Search for file
find /tmp -name "nautilus.txt.gpg"

# Verify exact path
pwd
ls -la /tmp/
```

### **Error: No Such File or Directory (Destination)**
**Symptoms**: `scp: /home/opt: No such file or directory`.
**Solution**: Create destination directory first.
```bash
# Create directory on destination
ssh steve@stapp02 "sudo mkdir -p /home/opt"

# Set proper ownership
ssh steve@stapp02 "sudo chown steve:steve /home/opt"

# Retry transfer
scp /tmp/nautilus.txt.gpg steve@stapp02:/home/opt/
```

### **Error: Host Key Verification Failed**
**Symptoms**: `Host key verification failed`.
**Solution**: Accept host key or update known_hosts.
```bash
# Remove old host key
ssh-keygen -R stapp02

# Connect and accept new key
ssh steve@stapp02

# Retry scp
scp /tmp/nautilus.txt.gpg steve@stapp02:/home/opt/
```

### **Error: Disk Space Full**
**Symptoms**: `scp: write: No space left on device`.
**Solution**: Check and free up disk space on destination.
```bash
# Check disk space on destination
ssh steve@stapp02 "df -h /home/opt"

# Clean up space if needed
ssh steve@stapp02 "sudo rm -rf /tmp/old_files"

# Retry transfer
scp /tmp/nautilus.txt.gpg steve@stapp02:/home/opt/
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The primary challenge was securely transferring an encrypted file from the jump host to an app server using SCP, ensuring file integrity and proper placement in the destination directory. Understanding SCP syntax and verification methods is crucial for successful file transfers.

**💡 Solution Approach**:
1. **Source Verification**: Confirmed file exists on jump host.
2. **Destination Check**: Verified destination directory exists.
3. **Secure Transfer**: Used `scp` to transfer file over encrypted SSH connection.
4. **Transfer Verification**: Checked exit status and connected to destination.
5. **File Verification**: Confirmed file exists at destination with correct attributes.

**🎯 Key Success Factors**:
- **Correct SCP Syntax**: Used proper format `scp source user@host:destination`.
- **Path Specification**: Included full paths for source and destination.
- **Authentication**: Provided correct password for destination user.
- **Verification**: Confirmed file transfer with multiple checks.
- **Integrity Check**: Used checksums to verify file integrity.

**⚠️ Critical Fixes**:
- Always verify source file exists before transfer.
- Ensure destination directory exists and is writable.
- Use full paths to avoid ambiguity.
- Verify transfer with exit status and file listing.
- Check file integrity with checksums for important files.

**🔒 Linux File Transfer Best Practices Applied**:
- **Secure Protocol**: Used SCP (SSH-based) for encrypted transfer.
- **Verification**: Confirmed file exists at both source and destination.
- **Integrity Check**: Used checksums to verify file integrity.
- **Permission Preservation**: SCP preserves file permissions by default.
- **Error Checking**: Verified exit status after transfer.

**⚠️ Important SCP Concepts**:
- **SSH-Based**: SCP uses SSH protocol for secure transfer.
- **Syntax**: `scp source user@host:destination`.
- **Authentication**: Requires SSH credentials (password or key).
- **Encryption**: All data is encrypted during transfer.
- **Permission Preservation**: Preserves file permissions and timestamps.

---

## ⚠️ Important Production Notes

🔧 **SCP vs RSYNC**: Use scp for simple transfers, rsync for large files or synchronization.
🔐 **SSH Keys**: Configure SSH key authentication for automated transfers.
📊 **Progress Monitoring**: Use `-v` flag for verbose output on large transfers.
🛡️ **Bandwidth Limiting**: Use `-l` flag to limit bandwidth usage.
🗄️ **Compression**: Use `-C` flag to enable compression for large files.
📝 **Logging**: Log file transfers for audit and compliance.
🔍 **Verification**: Always verify file integrity after transfer.
⚙️ **Automation**: Use scp in scripts for automated file transfers.

**SCP Command Options**:
```bash
# Basic syntax
scp source destination

# Verbose mode
scp -v file user@host:/path

# Preserve timestamps and permissions
scp -p file user@host:/path

# Recursive copy (directories)
scp -r directory user@host:/path

# Compression
scp -C file user@host:/path

# Limit bandwidth (in Kbit/s)
scp -l 1000 file user@host:/path

# Use specific port
scp -P 2222 file user@host:/path

# Use specific SSH key
scp -i ~/.ssh/id_rsa file user@host:/path

# Copy from remote to local
scp user@host:/remote/file /local/path

# Copy between two remote hosts
scp user1@host1:/path/file user2@host2:/path
```

**Alternative File Transfer Methods**:
```bash
# rsync (better for large files, supports resume)
rsync -avz --progress file user@host:/path

# sftp (interactive file transfer)
sftp user@host
put /tmp/nautilus.txt.gpg /home/opt/

# tar over ssh (for multiple files)
tar czf - files/ | ssh user@host "tar xzf - -C /destination"

# nc (netcat) for very large files
# On destination: nc -l 9999 > file
# On source: nc host 9999 < file
```

**Security Recommendations**:
- Use SSH key authentication instead of passwords for automation
- Implement SSH key passphrases for additional security
- Use `scp -p` to preserve file timestamps for audit trails
- Verify file integrity with checksums after transfer
- Use encrypted filesystems for sensitive data storage
- Implement access controls on destination directories
- Monitor and log all file transfers
- Use VPN or bastion hosts for transfers across networks

**Performance Optimization**:
```bash
# Enable compression for text files
scp -C file.txt user@host:/path

# Disable compression for already compressed files
scp file.tar.gz user@host:/path

# Use cipher with better performance
scp -c aes128-ctr file user@host:/path

# Parallel transfers for multiple files
parallel scp {} user@host:/path ::: file1 file2 file3

# Use rsync for large files (more efficient)
rsync -avz --progress file user@host:/path
```

**Batch File Transfers**:
```bash
# Transfer multiple files
scp file1 file2 file3 user@host:/path/

# Transfer directory recursively
scp -r /source/directory user@host:/destination/

# Transfer files matching pattern
scp /tmp/*.gpg user@host:/home/opt/

# Transfer with file list
cat filelist.txt | xargs -I {} scp {} user@host:/path/
```

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Copied `/tmp/nautilus.txt.gpg` from jump host to App Server 2
- Placed file in `/home/opt/` directory
- Verified file transfer completion
- Confirmed file exists at destination
- Maintained file integrity during transfer

**🔍 Verification Completed**:
- Source file exists: `/tmp/nautilus.txt.gpg` on jump host
- Transfer executed: `scp` command successful
- Exit status: 0 (success)
- Destination file exists: `/home/opt/nautilus.txt.gpg` on stapp02
- File size matches: Source and destination sizes identical
- Permissions preserved: File attributes maintained

**🚀 Next Steps** (Optional):
- Decrypt GPG file if needed for application use
- Set appropriate permissions on transferred file
- Remove source file from jump host if no longer needed
- Document file transfer in change log
- Notify developer of successful transfer
- Implement automated transfer process if recurring

**🔒 Security Validation**:
- ✅ File transferred over encrypted SSH connection
- ✅ GPG encryption maintained during transfer
- ✅ File integrity verified with checksums
- ✅ Proper authentication used for transfer
- ✅ File placed in secure location
- ✅ Transfer logged for audit trail

**📊 Transfer Summary**:
- **Source**: `/tmp/nautilus.txt.gpg` (jump host)
- **Destination**: `/home/opt/nautilus.txt.gpg` (stapp02)
- **Method**: SCP (Secure Copy Protocol)
- **Encryption**: SSH protocol encryption
- **File Type**: GPG encrypted data
- **Transfer Status**: Successful
- **Integrity**: Verified with checksums

**🔍 Key Learning Points**:
- SCP uses SSH protocol for secure file transfer
- Syntax: `scp source user@host:destination`
- SCP preserves file permissions and timestamps
- Always verify file transfer with checksums
- Exit status 0 indicates successful transfer
- Full paths prevent ambiguity in file transfers
