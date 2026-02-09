# 🔐 Task 4 - Text File Manipulation with grep and sed on App Server 3

**📌 Task Description**

There is some data on **Nautilus App Server 3** in Stratos DC. Data needs to be altered in several of the files. This task involves using Linux text processing tools (`grep` and `sed`) to filter and transform text content. The operations include deleting lines containing specific words and replacing words while respecting word boundaries to avoid unintended substitutions.

**👉 Task Requirements**:
- **Target Server**: App Server 3 (stapp03) in Stratos Datacenter
- **Source File**: `/home/BSD.txt`
- **Operation A**: Delete all lines containing word `code` (case-sensitive)
  - Output: `/home/BSD_DELETE.txt`
- **Operation B**: Replace all occurrences of word `and` with `them` (word boundary aware)
  - Output: `/home/BSD_REPLACE.txt`
- **Operation Objectives**:
  - Use `grep -v` to filter out lines
  - Use `sed` with word boundaries for replacement
  - Preserve original file
  - Create two new output files
- Ensure case sensitivity for filtering
- Respect word boundaries to avoid partial matches

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to App Server 3

```bash
ssh banner@stapp03
```

**Purpose**: Establish SSH connection to App Server 3 for file manipulation.

**Expected Output**:
```
banner@stapp03's password: 
Welcome to App Server 3 - Stratos Datacenter
[banner@stapp03 ~]$
```

**Success Indicators**:
- ✅ Successfully authenticated as user `banner`
- ✅ Command prompt shows `stapp03` hostname
- ✅ Ready to execute commands

---

## 🔹 Step 2: Verify Source File Exists

```bash
ls -l /home/BSD.txt
```

**Purpose**: Confirm the source file exists and is readable.

**Expected Output**:
```
-rw-r--r-- 1 root root 2048 Feb 09 18:00 /home/BSD.txt
```

**Success Indicators**:
- ✅ File exists
- ✅ File is readable
- ✅ Ready for processing

---

## 🔹 Step 3: View Source File Content (Optional)

```bash
head -20 /home/BSD.txt
```

**Purpose**: Preview the source file content to understand the data.

**Expected Output** (sample):
```
This is a sample BSD license file.
The code is provided as-is.
You can use and modify this code.
Contributors include developers and testers.
```

**Success Indicators**:
- ✅ Content displayed
- ✅ Understanding of data structure

---

## 🔹 Step 4: Delete Lines Containing 'code' and Save to BSD_DELETE.txt

```bash
sudo grep -v 'code' /home/BSD.txt | sudo tee /home/BSD_DELETE.txt > /dev/null
```

**Purpose**: Filter out all lines containing the word 'code' (case-sensitive) and save to new file.

**Command Breakdown**:
- `sudo`: Execute with administrative privileges
- `grep -v 'code'`: Invert match - select lines NOT containing 'code'
- `/home/BSD.txt`: Source file
- `|`: Pipe output to next command
- `sudo tee /home/BSD_DELETE.txt`: Write to file with sudo privileges
- `> /dev/null`: Suppress tee's stdout (only write to file)

**Expected Output**:
```
[no output due to > /dev/null]
```

**Success Indicators**:
- ✅ Command completes without errors
- ✅ Output file created
- ✅ Lines containing 'code' removed

**⚠️ Important**: 
- `grep -v` inverts the match (selects non-matching lines)
- Case-sensitive: 'code' ≠ 'Code' ≠ 'CODE'
- Matches 'code' anywhere in the line (including words like 'decode', 'encode')

---

## 🔹 Step 5: Replace 'and' with 'them' and Save to BSD_REPLACE.txt

```bash
sudo sed 's/\band\b/them/g' /home/BSD.txt | sudo tee /home/BSD_REPLACE.txt > /dev/null
```

**Purpose**: Replace all occurrences of the word 'and' with 'them', respecting word boundaries.

**Command Breakdown**:
- `sudo`: Execute with administrative privileges
- `sed`: Stream editor for text transformation
- `s/\band\b/them/g`: Substitution command
  - `s/`: Substitute command
  - `\b`: Word boundary anchor
  - `and`: Pattern to find
  - `\b`: Word boundary anchor
  - `them`: Replacement text
  - `g`: Global flag (replace all occurrences in each line)
- `/home/BSD.txt`: Source file
- `| sudo tee /home/BSD_REPLACE.txt`: Write to file
- `> /dev/null`: Suppress stdout

**Expected Output**:
```
[no output due to > /dev/null]
```

**Success Indicators**:
- ✅ Command completes without errors
- ✅ Output file created
- ✅ Only whole word 'and' replaced, not partial matches

**⚠️ Important**: 
- `\b` ensures word boundaries (won't match 'and' in 'band', 'sand', 'android')
- `g` flag replaces all occurrences in each line, not just the first
- Case-sensitive: 'and' ≠ 'And' ≠ 'AND'

---

## 🔹 Step 6: Verify Output Files Were Created

```bash
ls -l /home/BSD_DELETE.txt /home/BSD_REPLACE.txt
```

**Purpose**: Confirm both output files exist and have content.

**Expected Output**:
```
-rw-r--r-- 1 root root 1024 Feb 09 18:45 /home/BSD_DELETE.txt
-rw-r--r-- 1 root root 2048 Feb 09 18:45 /home/BSD_REPLACE.txt
```

**Success Indicators**:
- ✅ Both files exist
- ✅ Files have reasonable sizes
- ✅ Timestamps show recent creation

---

## 🔹 Step 7: Verify BSD_DELETE.txt Content

```bash
head /home/BSD_DELETE.txt
```

**Purpose**: Verify that lines containing 'code' were removed.

**Expected Output** (sample):
```
This is a sample BSD license file.
Contributors include developers and testers.
You may distribute this freely.
```

**Success Indicators**:
- ✅ No lines containing 'code' visible
- ✅ Other content preserved
- ✅ File is readable

**Verification**:
```bash
grep 'code' /home/BSD_DELETE.txt
```
**Expected**: No output (no lines containing 'code')

---

## 🔹 Step 8: Verify BSD_REPLACE.txt Content

```bash
head /home/BSD_REPLACE.txt
```

**Purpose**: Verify that 'and' was replaced with 'them' while preserving word boundaries.

**Expected Output** (sample):
```
This is a sample BSD license file.
The code is provided as-is.
You can use them modify this code.
Contributors include developers them testers.
```

**Success Indicators**:
- ✅ 'and' replaced with 'them'
- ✅ Words like 'band', 'sand' unchanged
- ✅ All content preserved

**Verification**:
```bash
grep 'and' /home/BSD_REPLACE.txt
```
**Expected**: May show lines with 'and' as part of other words (like 'band'), but not standalone 'and'

---

## 🔹 Step 9: Count Lines to Verify Filtering (Optional)

```bash
wc -l /home/BSD.txt /home/BSD_DELETE.txt
```

**Purpose**: Compare line counts to verify deletion operation.

**Expected Output** (sample):
```
  50 /home/BSD.txt
  35 /home/BSD_DELETE.txt
  85 total
```

**Success Indicators**:
- ✅ BSD_DELETE.txt has fewer lines than original
- ✅ Difference represents lines containing 'code'

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **App Server 3**:

```bash
# Connect to App Server 3
ssh banner@stapp03

# Verify source file
ls -l /home/BSD.txt

# Preview content (optional)
head -20 /home/BSD.txt

# Delete lines containing 'code'
sudo grep -v 'code' /home/BSD.txt | sudo tee /home/BSD_DELETE.txt > /dev/null

# Replace 'and' with 'them' (word boundaries)
sudo sed 's/\band\b/them/g' /home/BSD.txt | sudo tee /home/BSD_REPLACE.txt > /dev/null

# Verify files created
ls -l /home/BSD_DELETE.txt /home/BSD_REPLACE.txt

# Verify BSD_DELETE.txt content
head /home/BSD_DELETE.txt
grep 'code' /home/BSD_DELETE.txt  # Should return nothing

# Verify BSD_REPLACE.txt content
head /home/BSD_REPLACE.txt
grep '\band\b' /home/BSD_REPLACE.txt  # Should return nothing

# Count lines (optional)
wc -l /home/BSD.txt /home/BSD_DELETE.txt /home/BSD_REPLACE.txt
```

---

## 💡 Common Text Processing Issues & Fixes

### **Issue 1: Case Sensitivity Not Respected**
**Problem**: Need case-insensitive matching.
**Fix**: Use `-i` flag for grep or `I` flag for sed.
```bash
# Case-insensitive grep
grep -vi 'code' /home/BSD.txt

# Case-insensitive sed
sed 's/\band\b/them/gI' /home/BSD.txt
```

### **Issue 2: Word Boundaries Not Working**
**Problem**: Partial word matches occurring.
**Fix**: Ensure `\b` is used correctly in sed.
```bash
# WRONG - Matches 'and' in 'band', 'sand'
sed 's/and/them/g' /home/BSD.txt

# CORRECT - Only matches whole word 'and'
sed 's/\band\b/them/g' /home/BSD.txt
```

### **Issue 3: Permission Denied Writing Output**
**Problem**: Cannot write to /home directory.
**Fix**: Use sudo with tee command.
```bash
# WRONG - Permission denied
grep -v 'code' /home/BSD.txt > /home/BSD_DELETE.txt

# CORRECT - Use sudo with tee
sudo grep -v 'code' /home/BSD.txt | sudo tee /home/BSD_DELETE.txt > /dev/null
```

### **Issue 4: Original File Modified**
**Problem**: Accidentally modified source file.
**Fix**: Never use `-i` flag; always output to new file.
```bash
# DANGEROUS - Modifies original
sed -i 's/and/them/g' /home/BSD.txt

# SAFE - Creates new file
sed 's/and/them/g' /home/BSD.txt > /home/BSD_REPLACE.txt
```

### **Issue 5: Multiple Replacements Per Line**
**Problem**: Only first occurrence replaced.
**Fix**: Use `g` (global) flag in sed.
```bash
# WRONG - Only replaces first 'and' per line
sed 's/\band\b/them/' /home/BSD.txt

# CORRECT - Replaces all 'and' per line
sed 's/\band\b/them/g' /home/BSD.txt
```

---

## 🔧 Troubleshooting Failures

### **Error: File Not Found**
**Symptoms**: `No such file or directory: /home/BSD.txt`.
**Solution**: Verify file path and existence.
```bash
# Check if file exists
ls -l /home/BSD.txt

# Check current directory
pwd

# Use absolute path
sudo grep -v 'code' /home/BSD.txt | sudo tee /home/BSD_DELETE.txt > /dev/null
```

### **Error: Permission Denied**
**Symptoms**: Cannot read source or write output files.
**Solution**: Use sudo for file operations.
```bash
# Use sudo for both read and write
sudo grep -v 'code' /home/BSD.txt | sudo tee /home/BSD_DELETE.txt > /dev/null
```

### **Error: Empty Output File**
**Symptoms**: Output file created but empty.
**Solution**: Verify grep/sed pattern and source file content.
```bash
# Check source file has content
cat /home/BSD.txt

# Test grep pattern
grep -v 'code' /home/BSD.txt

# Verify output redirection
sudo grep -v 'code' /home/BSD.txt | sudo tee /home/BSD_DELETE.txt
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The primary challenge was performing text transformations while respecting word boundaries and case sensitivity. Using `grep -v` for line deletion and `sed` with `\b` word boundaries for precise word replacement requires understanding of regular expressions and text processing tools.

**💡 Solution Approach**:
1. **Line Deletion**: Used `grep -v 'code'` to invert match and exclude lines.
2. **Word Replacement**: Used `sed 's/\band\b/them/g'` with word boundaries.
3. **Output Redirection**: Used `tee` with sudo for proper file permissions.
4. **Verification**: Checked output files with head and grep commands.
5. **Preservation**: Original file remained unchanged.

**🎯 Key Success Factors**:
- **grep -v**: Inverts match to select non-matching lines.
- **Word Boundaries**: `\b` in sed prevents partial word matches.
- **Global Flag**: `g` in sed replaces all occurrences per line.
- **tee Command**: Allows writing with sudo privileges.
- **Case Sensitivity**: Default behavior preserves case distinction.

**⚠️ Critical Fixes**:
- Always use `\b` for word boundaries in sed.
- Use `grep -v` to exclude (delete) matching lines.
- Include `g` flag for global replacement in sed.
- Use `sudo tee` for writing to protected directories.
- Redirect tee output to /dev/null to suppress stdout.

**🔒 Text Processing Best Practices Applied**:
- **Non-destructive**: Original file preserved.
- **Word Boundaries**: Prevents unintended partial matches.
- **Verification**: Multiple checks to confirm results.
- **Case Sensitivity**: Explicit handling of case requirements.
- **Proper Permissions**: Using sudo where needed.

**⚠️ Important Text Processing Concepts**:
- **grep -v**: Inverts match (selects non-matching lines).
- **Word Boundary \b**: Matches position between word and non-word character.
- **Global Flag g**: Replaces all occurrences, not just first.
- **tee Command**: Writes to file while displaying on stdout.
- **Pipe |**: Chains commands for complex operations.

---

## ⚠️ Important Production Notes

🔧 **Word Boundaries**: Essential for precise word replacement without affecting partial matches.
🔐 **Case Sensitivity**: Default behavior; use flags for case-insensitive operations.
📊 **Backup Original**: Always preserve source file when transforming data.
🛡️ **Testing**: Test patterns on sample data before production files.
🗄️ **Regular Expressions**: Understanding regex is crucial for text processing.
📝 **Documentation**: Document transformation logic for future reference.
🔍 **Verification**: Always verify output matches expectations.
⚙️ **Automation**: Script repetitive text transformations.

**grep Command Reference**:
```bash
# Basic grep
grep 'pattern' file              # Find lines containing pattern
grep -v 'pattern' file            # Invert match (exclude pattern)
grep -i 'pattern' file            # Case-insensitive
grep -w 'word' file               # Match whole words only
grep -c 'pattern' file            # Count matching lines
grep -n 'pattern' file            # Show line numbers
grep -r 'pattern' directory       # Recursive search

# Multiple patterns
grep -e 'pattern1' -e 'pattern2' file
grep 'pattern1\|pattern2' file    # OR operation
```

**sed Command Reference**:
```bash
# Basic substitution
sed 's/old/new/' file             # Replace first occurrence per line
sed 's/old/new/g' file            # Replace all occurrences (global)
sed 's/old/new/2' file            # Replace second occurrence
sed 's/old/new/gI' file           # Global + case-insensitive

# Word boundaries
sed 's/\bold\b/new/g' file        # Replace whole word only

# Delete operations
sed '/pattern/d' file             # Delete lines matching pattern
sed '1d' file                     # Delete first line
sed '1,5d' file                   # Delete lines 1-5

# In-place editing (DANGEROUS)
sed -i 's/old/new/g' file         # Modifies original file
sed -i.bak 's/old/new/g' file     # Creates backup
```

**Security Recommendations**:
- Never use `sed -i` without backup on important files
- Test regex patterns on sample data first
- Verify output before replacing original files
- Use version control for critical files
- Document all transformations
- Implement approval process for production changes
- Regular backups before bulk operations
- Audit trail for file modifications

**Advanced Text Processing**:
```bash
# Combine grep and sed
grep 'pattern' file | sed 's/old/new/g'

# Multiple sed operations
sed -e 's/old1/new1/g' -e 's/old2/new2/g' file

# sed with line ranges
sed '1,10s/old/new/g' file        # Replace in lines 1-10

# Complex word boundary examples
sed 's/\bto\b/from/g' file        # Won't match 'into', 'upto'
sed 's/\band\b/or/g' file         # Won't match 'band', 'sand'
```

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Deleted all lines containing 'code' from BSD.txt
- Created BSD_DELETE.txt with filtered content
- Replaced all occurrences of 'and' with 'them' (word boundaries respected)
- Created BSD_REPLACE.txt with replaced content
- Original file preserved

**🔍 Verification Completed**:
- **BSD_DELETE.txt**: Created, no lines containing 'code'
- **BSD_REPLACE.txt**: Created, 'and' replaced with 'them', word boundaries respected
- **Original file**: BSD.txt unchanged
- **File permissions**: Proper ownership and permissions

**🚀 Next Steps** (Optional):
- Compare original and output files with diff
- Archive original file for backup
- Document transformation logic
- Automate similar operations with scripts
- Implement validation checks

**🔒 Configuration Validation**:
- ✅ Lines with 'code' removed from BSD_DELETE.txt
- ✅ Word 'and' replaced with 'them' in BSD_REPLACE.txt
- ✅ Word boundaries respected (no partial matches)
- ✅ Case sensitivity maintained
- ✅ Original file unchanged
- ✅ Both output files created successfully

**📊 Configuration Summary**:
- **Server**: App Server 3 (stapp03)
- **Source File**: /home/BSD.txt
- **Output File 1**: /home/BSD_DELETE.txt (lines with 'code' removed)
- **Output File 2**: /home/BSD_REPLACE.txt ('and' → 'them')
- **Tools Used**: grep, sed, tee
- **Preservation**: Original file unchanged

**🔍 Key Learning Points**:
- `grep -v` inverts match to exclude lines
- `\b` in sed provides word boundary matching
- `g` flag in sed enables global replacement
- `tee` allows writing with sudo privileges
- Word boundaries prevent partial word matches
- Case sensitivity is default behavior
