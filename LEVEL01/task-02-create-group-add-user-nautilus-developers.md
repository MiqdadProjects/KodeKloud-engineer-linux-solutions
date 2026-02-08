# 🔐 Task 2 - Create Group and Add User Across All App Servers

**📌 Task Description**

The system admin team at **xFusionCorp Industries** has streamlined access management by implementing group-based access control. This approach enhances security through centralized permission management and simplifies user administration across multiple servers. The task involves creating a consistent group structure and user membership across all application servers in the Stratos Datacenter.

**👉 Task Requirements**:
- **Target Servers**: All App Servers (stapp01, stapp02, stapp03) in Stratos Datacenter
- **Group Configuration**:
  - Group Name: `nautilus_developers`
  - Scope: Must exist on all App servers
- **User Configuration**:
  - Username: `jarod`
  - Group Membership: `nautilus_developers`
  - User Creation: Create user if it doesn't already exist
- **Security Objectives**:
  - Consistent group-based access control across all servers
  - Centralized developer access management
  - Simplified permission administration
- Verify group creation on all servers
- Verify user exists and is member of the group
- Ensure consistency across all application servers

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to App Server 1

```bash
ssh tony@stapp01
```

**Purpose**: Establish SSH connection to App Server 1 to begin group and user configuration.

**Expected Output**:
```
tony@stapp01's password: 
Welcome to App Server 1 - Stratos Datacenter
[tony@stapp01 ~]$
```

**Success Indicators**:
- ✅ Successfully authenticated as user `tony`
- ✅ Command prompt shows `stapp01` hostname
- ✅ Ready to execute administrative commands

---

## 🔹 Step 2: Create the nautilus_developers Group

```bash
sudo groupadd nautilus_developers
```

**Purpose**: Create a new system group for developer access control.

**Command Breakdown**:
- `sudo`: Execute with administrative privileges
- `groupadd`: Group creation command
- `nautilus_developers`: Name of the group to create

**Expected Output**:
```
[no output indicates success]
```

**Success Indicators**:
- ✅ Command completes without errors
- ✅ Group is created in `/etc/group`

---

## 🔹 Step 3: Verify Group Creation

```bash
grep "^nautilus_developers:" /etc/group
```

**Purpose**: Confirm the group was created successfully.

**Expected Output**:
```
nautilus_developers:x:1001:
```

**Field Breakdown**:
- `nautilus_developers`: Group name
- `x`: Password placeholder (groups rarely use passwords)
- `1001`: GID (Group ID) - may vary
- ``: Group members list (empty initially)

**Alternative Verification**:
```bash
getent group nautilus_developers
```

**Expected Output**:
```
nautilus_developers:x:1001:
```

---

## 🔹 Step 4: Check if User jarod Exists

```bash
id jarod
```

**Purpose**: Verify whether the user `jarod` already exists before attempting creation.

**Expected Output (if user doesn't exist)**:
```
id: 'jarod': no such user
```

**Expected Output (if user exists)**:
```
uid=1002(jarod) gid=1002(jarod) groups=1002(jarod)
```

**Success Indicators**:
- ✅ If user doesn't exist, proceed to user creation
- ✅ If user exists, skip to group membership step

---

## 🔹 Step 5: Create User jarod (If Needed)

```bash
sudo useradd -m -s /bin/bash jarod
```

**Purpose**: Create the user `jarod` with a home directory and bash shell.

**Command Breakdown**:
- `sudo`: Execute with administrative privileges
- `useradd`: User creation command
- `-m`: Create home directory (`/home/jarod`)
- `-s /bin/bash`: Set default shell to bash
- `jarod`: Username to create

**Expected Output**:
```
[no output indicates success]
```

**Success Indicators**:
- ✅ User created successfully
- ✅ Home directory created at `/home/jarod`
- ✅ Default shell set to `/bin/bash`

---

## 🔹 Step 6: Add User jarod to nautilus_developers Group

```bash
sudo usermod -aG nautilus_developers jarod
```

**Purpose**: Add the user `jarod` to the `nautilus_developers` group as a supplementary group.

**Command Breakdown**:
- `sudo`: Execute with administrative privileges
- `usermod`: User modification command
- `-aG`: Append to supplementary groups (preserves existing group memberships)
- `nautilus_developers`: Group to add user to
- `jarod`: Username to modify

**Expected Output**:
```
[no output indicates success]
```

**⚠️ Important**: Always use `-aG` (append) instead of `-G` alone to avoid removing user from other groups.

---

## 🔹 Step 7: Verify User and Group Membership

```bash
id jarod
```

**Purpose**: Confirm the user exists and is a member of the `nautilus_developers` group.

**Expected Output**:
```
uid=1002(jarod) gid=1002(jarod) groups=1002(jarod),1001(nautilus_developers)
```

**Success Indicators**:
- ✅ User `jarod` exists with valid UID
- ✅ Primary group is `jarod` (auto-created)
- ✅ Supplementary groups include `nautilus_developers`

**Alternative Verification**:
```bash
groups jarod
```

**Expected Output**:
```
jarod : jarod nautilus_developers
```

---

## 🔹 Step 8: Verify Group Members

```bash
grep "^nautilus_developers:" /etc/group
```

**Purpose**: Confirm `jarod` is listed as a member of the group.

**Expected Output**:
```
nautilus_developers:x:1001:jarod
```

**Success Indicators**:
- ✅ Group exists
- ✅ User `jarod` is listed in the members field

---

## 🔹 Step 9: Exit from App Server 1

```bash
exit
```

**Purpose**: Close SSH connection to App Server 1.

**Expected Output**:
```
logout
Connection to stapp01 closed.
```

---

## 🔹 Step 10: Repeat for App Server 2

```bash
ssh steve@stapp02
```

**Purpose**: Connect to App Server 2 to replicate the configuration.

**Expected Output**:
```
steve@stapp02's password: 
Welcome to App Server 2 - Stratos Datacenter
[steve@stapp02 ~]$
```

**Execute the same commands**:
```bash
# Create group
sudo groupadd nautilus_developers

# Create user (if needed)
sudo useradd -m -s /bin/bash jarod

# Add user to group
sudo usermod -aG nautilus_developers jarod

# Verify
id jarod
```

**Expected Output**:
```
uid=1002(jarod) gid=1002(jarod) groups=1002(jarod),1001(nautilus_developers)
```

**Exit from App Server 2**:
```bash
exit
```

---

## 🔹 Step 11: Repeat for App Server 3

```bash
ssh banner@stapp03
```

**Purpose**: Connect to App Server 3 to complete the configuration across all servers.

**Expected Output**:
```
banner@stapp03's password: 
Welcome to App Server 3 - Stratos Datacenter
[banner@stapp03 ~]$
```

**Execute the same commands**:
```bash
# Create group
sudo groupadd nautilus_developers

# Create user (if needed)
sudo useradd -m -s /bin/bash jarod

# Add user to group
sudo usermod -aG nautilus_developers jarod

# Verify
id jarod
```

**Expected Output**:
```
uid=1002(jarod) gid=1002(jarod) groups=1002(jarod),1001(nautilus_developers)
```

**Exit from App Server 3**:
```bash
exit
```

---

## 🔹 Step 12: Final Verification Across All Servers

**Purpose**: Confirm consistent configuration across all three App servers.

**Verification Checklist**:
- ✅ Group `nautilus_developers` exists on stapp01, stapp02, stapp03
- ✅ User `jarod` exists on all three servers
- ✅ User `jarod` is member of `nautilus_developers` on all servers
- ✅ Configuration is consistent across the datacenter

**Quick Verification Script** (optional):
```bash
for server in stapp01 stapp02 stapp03; do
  echo "=== Checking $server ==="
  ssh user@$server "id jarod && getent group nautilus_developers"
done
```

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **each App Server** (stapp01, stapp02, stapp03):

```bash
# Connect to App Server 1
ssh tony@stapp01

# Create group
sudo groupadd nautilus_developers

# Create user
sudo useradd -m -s /bin/bash jarod

# Add user to group
sudo usermod -aG nautilus_developers jarod

# Verify
id jarod
grep "^nautilus_developers:" /etc/group

# Exit
exit

# Repeat for App Server 2
ssh steve@stapp02
sudo groupadd nautilus_developers
sudo useradd -m -s /bin/bash jarod
sudo usermod -aG nautilus_developers jarod
id jarod
exit

# Repeat for App Server 3
ssh banner@stapp03
sudo groupadd nautilus_developers
sudo useradd -m -s /bin/bash jarod
sudo usermod -aG nautilus_developers jarod
id jarod
exit
```

---

## 💡 Common Group and User Management Issues & Fixes

### **Issue 1: Group Already Exists**
**Problem**: Error `groupadd: group 'nautilus_developers' already exists`.
**Fix**: This is acceptable - verify the group exists and proceed to user creation.
```bash
# Verify group exists
getent group nautilus_developers

# Continue with user creation and group membership
```

### **Issue 2: User Already Exists**
**Problem**: Error `useradd: user 'jarod' already exists`.
**Fix**: Skip user creation and proceed directly to adding user to group.
```bash
# Verify user exists
id jarod

# Add to group
sudo usermod -aG nautilus_developers jarod
```

### **Issue 3: User Not in Group After usermod**
**Problem**: `id jarod` doesn't show `nautilus_developers` in groups.
**Fix**: Verify command syntax and check for typos.
```bash
# Correct command (note the -aG flags)
sudo usermod -aG nautilus_developers jarod

# Verify group membership
id jarod
groups jarod
```

### **Issue 4: Permission Denied**
**Problem**: Cannot create group or user without sudo.
**Fix**: Ensure using sudo for all administrative commands.
```bash
# Always use sudo for user/group management
sudo groupadd nautilus_developers
sudo useradd -m -s /bin/bash jarod
sudo usermod -aG nautilus_developers jarod
```

### **Issue 5: Inconsistent GIDs Across Servers**
**Problem**: Group has different GIDs on different servers.
**Fix**: This is normal behavior unless you specify a GID. For consistency, you can specify a GID:
```bash
# Create group with specific GID
sudo groupadd -g 5000 nautilus_developers
```

---

## 🔧 Troubleshooting Failures

### **Error: Group Creation Fails**
**Symptoms**: `groupadd` command returns an error.
**Solution**: Check if group already exists or if there's a naming conflict.
```bash
# Check if group exists
getent group nautilus_developers

# Check for similar group names
grep "nautilus" /etc/group

# If group exists, verify and continue
# If naming conflict, choose alternative name or remove conflicting group
```

### **Error: User Creation Fails**
**Symptoms**: `useradd` command returns an error.
**Solution**: Verify user doesn't exist and check for system constraints.
```bash
# Check if user exists
id jarod
grep "^jarod:" /etc/passwd

# If user exists, skip creation
# If UID conflict, specify custom UID
sudo useradd -u 2000 -m -s /bin/bash jarod
```

### **Error: usermod Removes User from Other Groups**
**Symptoms**: User loses membership in other groups after `usermod`.
**Solution**: Always use `-aG` (append) instead of `-G` alone.
```bash
# WRONG - This replaces all supplementary groups
sudo usermod -G nautilus_developers jarod

# CORRECT - This appends to existing groups
sudo usermod -aG nautilus_developers jarod
```

### **Error: Changes Not Reflected Immediately**
**Symptoms**: User's active session doesn't show new group membership.
**Solution**: Group changes require logout/login or new session.
```bash
# Current session won't reflect changes
# User must logout and login again, or start a new shell
su - jarod

# Or verify with id command (shows updated info)
id jarod
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The primary challenge was ensuring consistent group and user configuration across multiple application servers (stapp01, stapp02, stapp03) while maintaining proper group membership and avoiding conflicts with existing users or groups.

**💡 Solution Approach**:
1. **Sequential Server Configuration**: Connected to each App server individually to apply identical configurations.
2. **Pre-verification**: Checked for existing groups and users before creation to avoid conflicts.
3. **Group Creation First**: Created the `nautilus_developers` group before user operations.
4. **Conditional User Creation**: Verified user existence before attempting creation.
5. **Proper Group Membership**: Used `usermod -aG` to append user to group without affecting other memberships.
6. **Comprehensive Verification**: Confirmed user and group configuration on each server using `id` and `getent` commands.

**🎯 Key Success Factors**:
- **Consistent Commands**: Used identical commands across all three servers.
- **Append Flag Usage**: Used `-aG` flag to preserve existing group memberships.
- **User Creation with Home**: Used `-m` flag to create home directory automatically.
- **Shell Assignment**: Set `/bin/bash` for proper user shell access.
- **Verification on Each Server**: Confirmed configuration after each server setup.

**⚠️ Critical Fixes**:
- Always check if group/user exists before creation to handle idempotency.
- Use `-aG` (append) instead of `-G` alone to avoid removing user from other groups.
- Verify group membership immediately after `usermod` to confirm success.
- Ensure consistent configuration across all servers for proper access control.

**🔒 Linux Group Management Best Practices Applied**:
- **Group-Based Access Control**: Centralized permission management through groups.
- **Supplementary Groups**: Used supplementary groups to add permissions without changing primary group.
- **Consistent Naming**: Used descriptive group name (`nautilus_developers`) for clarity.
- **Multi-Server Consistency**: Applied identical configuration across all application servers.
- **Verification at Each Step**: Confirmed each operation before proceeding to next server.

**⚠️ Important Security Concepts**:
- **Group Isolation**: Groups provide logical separation of access permissions.
- **Principle of Least Privilege**: Users gain only necessary group memberships.
- **Centralized Management**: Group-based access simplifies permission administration.
- **Audit Trail**: Group memberships are logged in `/etc/group` for tracking.

---

## ⚠️ Important Production Notes

🔧 **Group Management**: Always verify group doesn't exist before creation to avoid errors.
🔐 **User Creation**: Check user existence first to handle both new and existing users gracefully.
📊 **GID Consistency**: For strict consistency across servers, specify GID using `groupadd -g <GID>`.
🛡️ **Group Membership**: Always use `-aG` flag with `usermod` to append, not replace, group memberships.
🗄️ **Backup Considerations**: Include `/etc/group`, `/etc/passwd`, and `/etc/gshadow` in backup strategies.
📝 **Audit Trail**: Log group and user modifications for compliance and security tracking.
🔍 **Regular Audits**: Periodically review group memberships to identify unauthorized access.
⚙️ **Automation**: Consider using configuration management tools (Ansible, Puppet) for consistent multi-server user/group management.

**Group-Based Access Control Notes**:
- Groups simplify permission management by assigning permissions to groups instead of individual users
- Users inherit all permissions from their group memberships
- Supplementary groups allow users to belong to multiple groups simultaneously
- Changes to group permissions automatically apply to all group members
- Group-based access is essential for role-based access control (RBAC)

**Multi-Server Consistency Recommendations**:
- Use centralized authentication (LDAP, Active Directory) for large-scale deployments
- Maintain documentation of group purposes and membership policies
- Implement automated configuration management to ensure consistency
- Use specific GIDs when creating groups to maintain consistency across servers
- Regularly audit group memberships across all servers to detect drift

**Security Recommendations**:
- Limit group membership to only necessary users
- Document the purpose and permissions of each group
- Regularly review and remove unused groups
- Use descriptive group names that clearly indicate purpose
- Monitor group membership changes through system logs (`/var/log/secure`)
- Implement approval workflows for group membership changes in production environments

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Created `nautilus_developers` group on all three App servers (stapp01, stapp02, stapp03)
- Created user `jarod` on all servers with home directory and bash shell
- Added user `jarod` to `nautilus_developers` group on all servers
- Verified consistent configuration across entire Stratos Datacenter
- Implemented group-based access control for developer management

**🔍 Verification Completed**:
- Group exists on all servers: `getent group nautilus_developers`
- User exists on all servers: `id jarod`
- User is member of group on all servers: `groups jarod`
- Configuration is consistent across stapp01, stapp02, and stapp03

**🚀 Next Steps** (Optional):
- Configure application permissions for `nautilus_developers` group
- Add additional developers to the group as needed
- Implement group-based sudo rules if administrative access is required
- Document group purpose and membership policies
- Set up automated monitoring for group membership changes
