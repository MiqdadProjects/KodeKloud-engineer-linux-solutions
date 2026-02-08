# 🔐 Task 16 - Configure Firewall to Allow Port 8086 on Backup Server

**📌 Task Description**

The Nautilus system admins team has rolled out a web UI application for their backup utility on the **Nautilus backup server** within the Stratos Datacenter. This application operates on **port 8086**, and **firewalld** is active on the server. To meet operational needs, firewall rules must be configured to allow incoming connections to the backup utility's web interface. The `firewall-cmd` command provides a dynamic firewall management interface for controlling network traffic.

**👉 Task Requirements**:
- **Target Server**: Backup Server (stbkp01) in Stratos Datacenter
- **Application Port**: 8086/tcp
- **Firewall Zone**: public
- **Operation Objectives**:
  - Ensure firewalld service is running
  - Allow incoming connections on port 8086/tcp
  - Configure rule in public zone
  - Make configuration permanent
  - Verify firewall rule is active
- Add persistent firewall rule
- Reload firewall to apply changes
- Verify port is open in firewall

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to Backup Server

```bash
ssh clint@stbkp01
```

**Purpose**: Establish SSH connection to the Backup Server for firewall configuration.

**Expected Output**:
```
clint@stbkp01's password: 
Welcome to Backup Server - Stratos Datacenter
[clint@stbkp01 ~]$
```

**Success Indicators**:
- ✅ Successfully authenticated as user `clint`
- ✅ Command prompt shows `stbkp01` hostname
- ✅ Ready to execute administrative commands

---

## 🔹 Step 2: Check Firewalld Service Status

```bash
sudo systemctl status firewalld
```

**Purpose**: Verify if firewalld service is running.

**Expected Output** (if running):
```
● firewalld.service - firewalld - dynamic firewall daemon
   Loaded: loaded (/usr/lib/systemd/system/firewalld.service; enabled; vendor preset: enabled)
   Active: active (running) since Sat 2026-02-08 15:00:00 EST; 1h ago
     Docs: man:firewalld(1)
 Main PID: 1234 (firewalld)
    Tasks: 2
   Memory: 25.5M
   CGroup: /system.slice/firewalld.service
           └─1234 /usr/bin/python3 -s /usr/sbin/firewalld --nofork --nopid
```

**Success Indicators**:
- ✅ Service is `active (running)`
- ✅ Service is `enabled`
- ✅ Ready to configure firewall rules

**If service is not running**, proceed to Step 3. Otherwise, skip to Step 5.

---

## 🔹 Step 3: Start Firewalld Service (If Not Running)

```bash
sudo systemctl start firewalld
```

**Purpose**: Start the firewalld service if it's not currently running.

**Expected Output**:
```
[no output indicates success]
```

**Success Indicators**:
- ✅ Service started successfully
- ✅ No error messages

---

## 🔹 Step 4: Enable Firewalld Service (If Not Enabled)

```bash
sudo systemctl enable firewalld
```

**Purpose**: Enable firewalld to start automatically on system boot.

**Expected Output**:
```
Created symlink /etc/systemd/system/dbus-org.fedoraproject.FirewallD1.service → /usr/lib/systemd/system/firewalld.service.
Created symlink /etc/systemd/system/multi-user.target.wants/firewalld.service → /usr/lib/systemd/system/firewalld.service.
```

**Success Indicators**:
- ✅ Symlinks created
- ✅ Service will start on boot
- ✅ Firewalld is now enabled

---

## 🔹 Step 5: Verify Firewalld is Running

```bash
sudo systemctl status firewalld
```

**Purpose**: Confirm firewalld service is active and running.

**Expected Output**:
```
● firewalld.service - firewalld - dynamic firewall daemon
   Loaded: loaded (/usr/lib/systemd/system/firewalld.service; enabled; vendor preset: enabled)
   Active: active (running) since Sat 2026-02-08 15:00:00 EST; 1m ago
```

**Success Indicators**:
- ✅ Status shows `active (running)`
- ✅ Service is enabled
- ✅ Ready to add firewall rules

---

## 🔹 Step 6: Check Current Firewall Rules (Optional)

```bash
sudo firewall-cmd --zone=public --list-all
```

**Purpose**: Display current firewall configuration in public zone before making changes.

**Expected Output** (sample):
```
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: eth0
  sources: 
  services: dhcpv6-client ssh
  ports: 
  protocols: 
  masquerade: no
  forward-ports: 
  source-ports: 
  icmp-blocks: 
  rich rules:
```

**Success Indicators**:
- ✅ Public zone is active
- ✅ Current configuration displayed
- ✅ Port 8086 not yet in ports list

---

## 🔹 Step 7: Add Port 8086/tcp to Public Zone (Permanent)

```bash
sudo firewall-cmd --zone=public --add-port=8086/tcp --permanent
```

**Purpose**: Add firewall rule to allow incoming connections on port 8086/tcp permanently.

**Command Breakdown**:
- `sudo`: Execute with administrative privileges
- `firewall-cmd`: Firewall management command
- `--zone=public`: Specify public zone
- `--add-port=8086/tcp`: Add port 8086 with TCP protocol
- `--permanent`: Make rule persistent across reboots

**Expected Output**:
```
success
```

**Success Indicators**:
- ✅ Output shows `success`
- ✅ Rule added to permanent configuration
- ✅ Rule will persist after reboot

**⚠️ Important**: The `--permanent` flag saves the rule to disk but doesn't apply it immediately. A reload is required.

---

## 🔹 Step 8: Reload Firewall to Apply Changes

```bash
sudo firewall-cmd --reload
```

**Purpose**: Reload firewall configuration to apply the permanent rule to the running configuration.

**Command Breakdown**:
- `sudo`: Execute with administrative privileges
- `firewall-cmd`: Firewall management command
- `--reload`: Reload firewall rules from permanent configuration

**Expected Output**:
```
success
```

**Success Indicators**:
- ✅ Output shows `success`
- ✅ Permanent rules now active
- ✅ Port 8086 is now open

---

## 🔹 Step 9: Verify Port 8086 is in Firewall Rules

```bash
sudo firewall-cmd --zone=public --list-ports
```

**Purpose**: Confirm port 8086/tcp is now in the firewall rules.

**Expected Output**:
```
8086/tcp
```

**Success Indicators**:
- ✅ Port 8086/tcp is listed
- ✅ Rule is active
- ✅ Port is open for incoming connections

---

## 🔹 Step 10: Verify Complete Firewall Configuration

```bash
sudo firewall-cmd --list-all
```

**Purpose**: Display complete firewall configuration to verify all settings.

**Expected Output**:
```
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: eth0
  sources: 
  services: dhcpv6-client ssh
  ports: 8086/tcp
  protocols: 
  masquerade: no
  forward-ports: 
  source-ports: 
  icmp-blocks: 
  rich rules:
```

**Success Indicators**:
- ✅ Port 8086/tcp appears in ports list
- ✅ Zone is public
- ✅ Configuration is complete

---

## 🔹 Step 11: Test Port Accessibility (Optional)

```bash
sudo ss -tuln | grep 8086
```

**Purpose**: Verify if the application is listening on port 8086.

**Expected Output** (if application is running):
```
tcp   LISTEN 0      128          0.0.0.0:8086       0.0.0.0:*
```

**Success Indicators**:
- ✅ Application is listening on port 8086
- ✅ Port is bound to all interfaces (0.0.0.0)
- ✅ Ready to accept connections

**Note**: If no output, the application may not be running yet.

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **Backup Server**:

```bash
# Connect to Backup Server
ssh clint@stbkp01

# Check firewalld status
sudo systemctl status firewalld

# Start firewalld (if not running)
sudo systemctl start firewalld

# Enable firewalld (if not enabled)
sudo systemctl enable firewalld

# Verify firewalld is running
sudo systemctl status firewalld

# Check current firewall rules (optional)
sudo firewall-cmd --zone=public --list-all

# Add port 8086/tcp permanently
sudo firewall-cmd --zone=public --add-port=8086/tcp --permanent

# Reload firewall
sudo firewall-cmd --reload

# Verify port is added
sudo firewall-cmd --zone=public --list-ports

# Verify complete configuration
sudo firewall-cmd --list-all

# Test port accessibility (optional)
sudo ss -tuln | grep 8086
```

---

## 💡 Common Firewall Configuration Issues & Fixes

### **Issue 1: Rule Not Persisting After Reboot**
**Problem**: Firewall rule disappears after server reboot.
**Fix**: Always use `--permanent` flag when adding rules.
```bash
# WRONG - Rule lost after reboot
sudo firewall-cmd --zone=public --add-port=8086/tcp

# CORRECT - Rule persists
sudo firewall-cmd --zone=public --add-port=8086/tcp --permanent
sudo firewall-cmd --reload
```

### **Issue 2: Rule Added But Not Active**
**Problem**: Rule added with `--permanent` but not working.
**Fix**: Reload firewall after adding permanent rules.
```bash
# Add permanent rule
sudo firewall-cmd --zone=public --add-port=8086/tcp --permanent

# Must reload to activate
sudo firewall-cmd --reload

# Verify
sudo firewall-cmd --zone=public --list-ports
```

### **Issue 3: Wrong Zone**
**Problem**: Rule added to wrong zone.
**Fix**: Specify correct zone or remove from wrong zone.
```bash
# Check which zone is active
sudo firewall-cmd --get-active-zones

# Remove from wrong zone
sudo firewall-cmd --zone=wrong-zone --remove-port=8086/tcp --permanent

# Add to correct zone
sudo firewall-cmd --zone=public --add-port=8086/tcp --permanent
sudo firewall-cmd --reload
```

### **Issue 4: Firewalld Not Running**
**Problem**: Cannot add rules because firewalld is not running.
**Fix**: Start and enable firewalld service.
```bash
# Start firewalld
sudo systemctl start firewalld

# Enable firewalld
sudo systemctl enable firewalld

# Verify status
sudo systemctl status firewalld

# Add rule
sudo firewall-cmd --zone=public --add-port=8086/tcp --permanent
sudo firewall-cmd --reload
```

### **Issue 5: Port Still Not Accessible**
**Problem**: Port open in firewall but still not accessible.
**Fix**: Verify application is listening and check other firewalls.
```bash
# Check if application is listening
sudo ss -tuln | grep 8086
sudo netstat -tuln | grep 8086

# Check SELinux (if enabled)
sudo semanage port -l | grep 8086

# Check iptables (if running alongside firewalld)
sudo iptables -L -n | grep 8086
```

---

## 🔧 Troubleshooting Failures

### **Error: FirewallD is Not Running**
**Symptoms**: `FirewallD is not running`.
**Solution**: Start and enable firewalld service.
```bash
# Start firewalld
sudo systemctl start firewalld

# Enable firewalld
sudo systemctl enable firewalld

# Verify
sudo systemctl status firewalld
```

### **Error: Unknown Zone**
**Symptoms**: `Error: INVALID_ZONE: public`.
**Solution**: Verify zone name and list available zones.
```bash
# List available zones
sudo firewall-cmd --get-zones

# Use correct zone name (usually 'public')
sudo firewall-cmd --zone=public --add-port=8086/tcp --permanent
```

### **Error: Permission Denied**
**Symptoms**: `Authorization failed`.
**Solution**: Use sudo for firewall operations.
```bash
# Always use sudo
sudo firewall-cmd --zone=public --add-port=8086/tcp --permanent

# Verify sudo access
sudo -l
```

### **Error: Invalid Port**
**Symptoms**: `Error: INVALID_PORT`.
**Solution**: Verify port syntax includes protocol.
```bash
# WRONG - Missing protocol
sudo firewall-cmd --zone=public --add-port=8086 --permanent

# CORRECT - Include protocol
sudo firewall-cmd --zone=public --add-port=8086/tcp --permanent
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The primary challenge was configuring firewalld to allow incoming connections on port 8086/tcp for the backup utility's web UI. Understanding the difference between runtime and permanent firewall rules, and the need to reload after adding permanent rules, is crucial for proper firewall management.

**💡 Solution Approach**:
1. **Service Verification**: Checked if firewalld is running and enabled.
2. **Service Start**: Started and enabled firewalld if necessary.
3. **Permanent Rule**: Added port 8086/tcp to public zone with `--permanent` flag.
4. **Reload**: Reloaded firewall to apply permanent rule to runtime configuration.
5. **Verification**: Confirmed port is open with `--list-ports` and `--list-all`.

**🎯 Key Success Factors**:
- **Permanent Flag**: Used `--permanent` to ensure rule persists across reboots.
- **Reload Required**: Reloaded firewall after adding permanent rule.
- **Correct Zone**: Specified `public` zone as required.
- **Protocol Specification**: Included `/tcp` in port specification.
- **Verification**: Confirmed rule is active with multiple checks.

**⚠️ Critical Fixes**:
- Always use `--permanent` flag for persistent rules.
- Must reload firewall after adding permanent rules.
- Include protocol (tcp/udp) when specifying ports.
- Verify firewalld service is running before adding rules.
- Check both permanent and runtime configurations.

**🔒 Linux Firewall Best Practices Applied**:
- **Persistent Configuration**: Used `--permanent` for rules that survive reboots.
- **Zone-Based**: Applied rule to appropriate zone (public).
- **Verification**: Confirmed configuration with multiple commands.
- **Service Management**: Ensured firewalld is enabled for automatic start.
- **Documentation**: Clear understanding of runtime vs permanent rules.

**⚠️ Important Firewalld Concepts**:
- **Runtime vs Permanent**: Runtime rules are temporary, permanent rules persist.
- **Reload Required**: Permanent rules need reload to become active.
- **Zones**: Firewalld uses zones to group rules (public, internal, etc.).
- **Protocol Specification**: Must specify tcp or udp with port numbers.
- **Default Zone**: Usually `public` for internet-facing interfaces.

---

## ⚠️ Important Production Notes

🔧 **Permanent Rules**: Always use `--permanent` flag for production firewall rules.
🔐 **Reload After Changes**: Must reload firewall after adding permanent rules.
📊 **Zone Selection**: Use appropriate zone based on network trust level.
🛡️ **Port Ranges**: Can specify port ranges like `8000-9000/tcp`.
🗄️ **Service vs Port**: Prefer predefined services over custom ports when available.
📝 **Documentation**: Document all firewall changes in change logs.
🔍 **Regular Audits**: Review firewall rules regularly for security compliance.
⚙️ **Automation**: Use configuration management for consistent firewall settings.

**Firewall-cmd Command Reference**:
```bash
# Zone management
firewall-cmd --get-zones                    # List all zones
firewall-cmd --get-active-zones             # Show active zones
firewall-cmd --get-default-zone             # Show default zone
firewall-cmd --set-default-zone=public      # Set default zone

# Port management
firewall-cmd --zone=public --add-port=8086/tcp --permanent
firewall-cmd --zone=public --remove-port=8086/tcp --permanent
firewall-cmd --zone=public --list-ports

# Port range
firewall-cmd --zone=public --add-port=8000-9000/tcp --permanent

# Service management
firewall-cmd --zone=public --add-service=http --permanent
firewall-cmd --zone=public --remove-service=http --permanent
firewall-cmd --zone=public --list-services

# Reload and runtime
firewall-cmd --reload                       # Reload permanent to runtime
firewall-cmd --runtime-to-permanent         # Save runtime to permanent

# Complete configuration
firewall-cmd --list-all                     # Show all in default zone
firewall-cmd --zone=public --list-all       # Show all in specific zone
```

**Runtime vs Permanent Rules**:
```bash
# Runtime only (lost on reload/reboot)
sudo firewall-cmd --zone=public --add-port=8086/tcp

# Permanent only (not active until reload)
sudo firewall-cmd --zone=public --add-port=8086/tcp --permanent

# Both runtime and permanent (immediate + persistent)
sudo firewall-cmd --zone=public --add-port=8086/tcp --permanent
sudo firewall-cmd --reload

# Or add to both in one command
sudo firewall-cmd --zone=public --add-port=8086/tcp
sudo firewall-cmd --runtime-to-permanent
```

**Security Recommendations**:
- Only open ports that are absolutely necessary
- Use specific zones based on network trust level
- Regularly audit open ports and services
- Remove unused firewall rules
- Use rich rules for complex access control
- Implement source IP restrictions when possible
- Monitor firewall logs for suspicious activity
- Test firewall rules before applying to production

**Advanced Firewall Rules**:
```bash
# Rich rules (complex access control)
sudo firewall-cmd --zone=public --add-rich-rule='rule family="ipv4" source address="192.168.1.0/24" port port="8086" protocol="tcp" accept' --permanent

# Source IP restriction
sudo firewall-cmd --zone=public --add-rich-rule='rule family="ipv4" source address="10.0.0.5" port port="8086" protocol="tcp" accept' --permanent

# Port forwarding
sudo firewall-cmd --zone=public --add-forward-port=port=80:proto=tcp:toport=8086 --permanent

# Masquerading (NAT)
sudo firewall-cmd --zone=public --add-masquerade --permanent
```

**Firewall Zones Explained**:
```
drop     - Drop all incoming, allow outgoing
block    - Reject all incoming, allow outgoing
public   - Public networks (default for internet-facing)
external - External networks with masquerading
dmz      - DMZ (limited access to internal)
work     - Work networks (more trusted)
home     - Home networks (more trusted)
internal - Internal networks (trusted)
trusted  - All connections allowed
```

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Configured firewall to allow incoming connections on port 8086/tcp
- Rule added to public zone as required
- Configuration made permanent (persists across reboots)
- Firewalld service running and enabled
- Verified port is open in firewall rules

**🔍 Verification Completed**:
- Firewalld service: Active and running
- Firewalld enabled: Yes (starts on boot)
- Port 8086/tcp: Added to public zone
- Permanent rule: Yes (persists across reboots)
- Rule active: Yes (reload completed)
- Verification: Port listed in `--list-ports` and `--list-all`

**🚀 Next Steps** (Optional):
- Test web UI accessibility from external network
- Configure SSL/TLS for secure access
- Implement authentication for web UI
- Set up monitoring for port 8086
- Document web UI access procedures
- Configure backup schedules via web UI

**🔒 Security Validation**:
- ✅ Firewall rule configured correctly
- ✅ Only port 8086/tcp opened (minimal exposure)
- ✅ Rule applied to public zone (appropriate for web UI)
- ✅ Permanent configuration ensures persistence
- ✅ Firewalld service enabled for automatic protection
- ✅ Configuration verified with multiple commands

**📊 Configuration Summary**:
- **Server**: Backup Server (stbkp01)
- **Port**: 8086/tcp
- **Zone**: public
- **Rule Type**: Permanent
- **Protocol**: TCP
- **Direction**: Incoming
- **Status**: Active and verified

**🔍 Key Learning Points**:
- `--permanent` flag makes rules persist across reboots
- Must reload firewall after adding permanent rules
- Port specification requires protocol (tcp/udp)
- Firewalld uses zones for organizing rules
- Runtime and permanent configurations are separate
- `firewall-cmd --reload` applies permanent to runtime
