# 🔐 Task 22 - GPG Data Encryption and Decryption on ststor01

**📌 Task Description**

Data security and integrity are paramount for **xFusionCorp Industries**. To protect confidential information during transit and storage within the **Stratos Datacenter**, the security team has mandated the use of **GPG (GNU Privacy Guard)** for asymmetric encryption and decryption. This task involves importing existing cryptographic keys onto the **Nautilus Storage Server (ststor01)** and performing specific encryption/decryption operations on sensitive text files using a defined passphrase and user identity.

**👉 Task Requirements**:
- **Target Server**: Nautilus Storage Server (`ststor01`)
- **Cryptography Tool**: GPG (GNU Privacy Guard)
- **Keys Location**: `/home/*_key.asc`
- **Passphrase**: `kodekloud`
- **User Identity**: `kodekloud@kodekloud.com`
- **Operation Objectives**:
  - Import public and private keys provided in `/home/`
  - Encrypt `/home/encrypt_me.txt` to `/home/encrypted_me.asc`
  - Decrypt `/home/decrypt_me.asc` to `/home/decrypted_me.txt`
  - Verify the integrity of the decrypted content
- Use non-interactive batch mode where appropriate
- Ensure correct key association with the recipient ID
- Maintain strict file path compliance

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to Storage Server

```bash
ssh natasha@ststor01
```

**Purpose**: Establish an administrative session to the storage server.

**Expected Output**:
```
natasha@ststor01's password: 
Welcome to Nautilus Storage Server
[natasha@ststor01 ~]$
```

**Success Indicators**:
- ✅ Successfully authenticated as user `natasha`
- ✅ Switched to root/admin context if necessary to access files in `/home/`

---

## 🔹 Step 2: Import Cryptographic Public Key

```bash
gpg --import /home/public_key.asc
```

**Purpose**: Add the recipient's public key to the local GPG keyring for encryption purposes.

**Success Indicators**:
- ✅ Output shows: `gpg: key ...: public key "kodekloud <kodekloud@kodekloud.com>" imported`
- ✅ Key verified using `gpg --list-keys`

---

## 🔹 Step 3: Import Cryptographic Private Key

```bash
gpg --batch --yes --pinentry-mode loopback \
    --passphrase kodekloud \
    --import /home/private_key.asc
```

**Purpose**: Import the private key required for decrypting incoming data. Using `--batch` and `--pinentry-mode loopback` allows the passphrase to be provided automatically.

**Success Indicators**:
- ✅ Output shows: `gpg: key ...: secret key imported`
- ✅ Key verified using `gpg --list-secret-keys`

---

## 🔹 Step 4: Encrypt Sensitive Content

```bash
gpg --output /home/encrypted_me.asc \
    --encrypt \
    --recipient kodekloud@kodekloud.com \
    /home/encrypt_me.txt
```

**Purpose**: Transform the plain-text file into an encrypted ASCII-armored file intended only for the specified recipient ID.

**Command Breakdown**:
- `--output`: Specifies the name of the resulting encrypted file.
- `--encrypt`: Initiates the encryption process.
- `--recipient`: Links the encryption to the public key of the target identity.

**Success Indicators**:
- ✅ File `/home/encrypted_me.asc` created.
- ✅ No "untrusted key" errors (or manually accepted).

---

## 🔹 Step 5: Decrypt Incoming Data

```bash
gpg --batch --yes --pinentry-mode loopback \
    --passphrase kodekloud \
    --output /home/decrypted_me.txt \
    --decrypt /home/decrypt_me.asc
```

**Purpose**: Reverse the encryption on the received task file to retrieve its original content using the imported private key and specified passphrase.

**Success Indicators**:
- ✅ File `/home/decrypted_me.txt` created.
- ✅ GPG reports: `gpg: encrypted with ... bit RSA key ...`

---

## 🔹 Step 6: Final Verification of Content

```bash
cat /home/decrypted_me.txt
```

**Purpose**: Read the decrypted file to ensure the process was successful and the data is intelligible.

**Expected Content**:
```text
Welcome to xFusionCorp Industries. This is KodeKloud System Administration Lab
```

**Success Indicators**:
- ✅ Decrypted text matches the expected lab greeting.
- ✅ File permissions on output files are appropriate.

---

## 📋 Quick Command Reference

GPG Workflow for ststor01:

```bash
# 1. Key Import (Non-interactive)
gpg --import /home/public_key.asc
gpg --batch --yes --passphrase kodekloud --import /home/private_key.asc

# 2. Encryption
gpg --output /home/encrypted_me.asc --encrypt --recipient kodekloud@kodekloud.com /home/encrypt_me.txt

# 3. Decryption
gpg --batch --yes --passphrase kodekloud --output /home/decrypted_me.txt --decrypt /home/decrypt_me.asc
```

---

## 💡 Common GPG Issues & Fixes

### **Issue 1: Pinentry Missing**
**Problem**: The system prompts for a GUI password box which is unavailable in the terminal.
**Fix**: Force the use of the loopback pinentry mode to accept the command-line passphrase.
```bash
--pinentry-mode loopback
```

### **Issue 2: Key Not Trusted**
**Problem**: GPG warns that the public key is not trusted.
**Fix**: Either sign the key locally or use `--trust-model always` in the command.

### **Issue 3: Secret Key Not Available**
**Problem**: Decryption fails with "No secret key".
**Fix**: Verify the private key was imported correctly using `gpg -K`.

---

## 🔧 Troubleshooting Failures

### **Error: gpg: decryption failed: No secret key**
**Symptoms**: Private key import failed or wrong recipient used during encryption.
**Solution**: Re-import the private key using the `--passphrase` provided in the task.

### **Error: gpg: /home/encrypted_me.asc: permission denied**
**Symptoms**: Unable to write the output file.
**Solution**: Ensure you are running as a user with write permissions to `/home/` or use `sudo`.

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The primary challenge of Task 22 is handling asymmetric keys in a non-interactive bash environment. Standard GPG commands often trigger a secure TTY prompt for passphrases, which can hang or fail in automated sessions.

**💡 Solution Approach**:
1. **Passphrase Automation**: Utilized `--batch` and `--pinentry-mode loopback` to pipe the `kodekloud` passphrase directly into the GPG process.
2. **Recipient Mapping**: Explicitly targeted the user ID `kodekloud@kodekloud.com` during encryption to ensure the metadata matched the provided public key.
3. **Sequence Logic**: Public keys were imported first to establish the identity before performing the operations.

---

## ⚠️ Important Production Notes

🔧 **Key Management**: Never store private keys in plain-text `/home/` directories in actual production environments.
🔐 **Entropy**: GPG requires system entropy to generate or handle keys. On some VMs, this can be slow.
📊 **Encryption Level**: These tasks typically use RSA 2048 or 4096. Higher bits provide better security at the cost of slight performance overhead.
🛡️ **Audit**: Use `gpg --list-packets` to inspect the metadata of encrypted files if debugging is required.

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Imported **Public** and **Private** GPG keys onto `ststor01`.
- Encrypted the source file `/home/encrypt_me.txt` for the specified recipient.
- Decrypted the target file `/home/decrypt_me.asc` using the provided passphrase.
- Validated the resulting decrypted content successfully.

**🔍 Verification Completed**:
- **ststor01**: Encrypted and Decrypted files verified on filesystem.
- **Content Integrity**: Decrypted text matches expected strings.

**🔒 Configuration Summary**:
- **Identity**: kodekloud@kodekloud.com
- **Passphrase**: kodekloud
- **Method**: GPG RSA Cryptography
- **Location**: Storage Server
