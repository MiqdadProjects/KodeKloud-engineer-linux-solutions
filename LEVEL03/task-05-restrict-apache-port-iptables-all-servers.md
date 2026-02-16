# 🔐 Task 05 - Restrict Apache Port 5002 Using Iptables on All App Servers

**📌 Task Description**

The security team at **xFusionCorp Industries** has identified a vulnerability in the **Nautilus** infrastructure within the **Stratos Datacenter**. Currently, **Apache's port (5002)** is exposed to all incoming traffic because no host-level firewall is implemented on the application servers. To harden the infrastructure, we have decided to deploy **iptables** across all application servers. The primary requirement is to restrict access to port 5002 so that only the **Load Balancer (LBR)** host can communicate with it, while blocking all other external entities. These security rules must be persistent across system reboots.

**👉 Task Requirements**:
- **Target Servers**: All Application Servers (stapp01, stapp02, stapp03)
- **Firewall Tool**: `iptables`
- **Protected Port**: `5002` (Apache)
- **Allowed Source**: LBR Host (`172.16.238.14`)
- **Operation Objectives**:
  - Install `iptables-services` and dependencies on all application hosts.
  - Implement a specific ALLOW rule for the LBR IP on port 5002.
  - Implement a DROP rule for all other sources on port 5002.
  - Ensure administrative access (SSH) and local loopback are preserved.
  - Configure rules to be permanent using the `iptables-services` framework.
- Verify rule alignment and order (Accept before Drop).
- Validate persistence after service restarts.

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to Application Servers

Perform these steps on **stapp01**, **stapp02**, and **stapp03**:

```bash
ssh tony@stapp01 # (Repeat for steve@stapp02 and banner@stapp03)
```

**Purpose**: Establish administrative sessions on each member of the application tier.

**Success Indicators**:
- ✅ Successfully authenticated on all nodes.
- ✅ Switched to root using `sudo -i`.

---

## 🔹 Step 2: Install Iptables Services

```bash
sudo yum install -y iptables-services
```

**Purpose**: Install the package required to manage iptables rules as a system service with persistence capabilities.

**Success Indicators**:
- ✅ Package installed without errors.
- ✅ Command `service iptables save` becomes available.

---

## 🔹 Step 3: Configure Firewall Rules

### **A. Flush Existing Rules and Set Basic Policies**
```bash
# Flush all rules
sudo iptables -F

# Allow loopback (Internal communication)
sudo iptables -A INPUT -i lo -j ACCEPT

# Allow established/related connections
sudo iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

# Allow SSH access (Critical for management)
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT
```

### **B. Implement Port 5002 Security**
```bash
# 1. Allow Port 5002 ONLY from LBR (172.16.238.14)
sudo iptables -A INPUT -p tcp -s 172.16.238.14 --dport 5002 -j ACCEPT

# 2. Block Port 5002 for all other sources
sudo iptables -A INPUT -p tcp --dport 5002 -j DROP
```

### **C. Set Default Reject Policy**
```bash
# Reject all other unknown incoming traffic
sudo iptables -A INPUT -j REJECT --reject-with icmp-host-prohibited
```

**Purpose**: Establish a "Least Privilege" posture where port 5002 is only reachable by the trusted Load Balancer.

---

## 🔹 Step 4: Verify Rule Stack

```bash
sudo iptables -L -n --line-numbers
```

**Purpose**: Confirm the sequence of rules. `iptables` processes rules from top to bottom; the LBR allow rule must exist above the general port 5002 drop rule.

**Success Indicators**:
- ✅ Rule 1: `ACCEPT` for `lo`
- ✅ Rule ...: `ACCEPT` for `-s 172.16.238.14` on `tcp dpt:5002`
- ✅ Rule ...: `DROP` for `tcp dpt:5002`

---

## 🔹 Step 5: Implement Persistence

```bash
# Save current rules to persistent storage
sudo service iptables save

# Enable and start the service
sudo systemctl enable iptables
sudo systemctl restart iptables
```

**Purpose**: Ensure the security posture survives a reboot or service interruption.

**Success Indicators**:
- ✅ Output: `iptables: Saving firewall rules to /etc/sysconfig/iptables: [  OK  ]`
- ✅ Service status is `active (exited)`.

---

## 🔹 Step 6: Final Validation

```bash
# From the Jump Host or another node (NOT the LBR):
telnet stapp01 5002
# Expected: Connection timeout/dropped

# Verification of current ruleset
sudo iptables -S
```

**Success Indicators**:
- ✅ Port 5002 unreachable from non-LBR sources.
- ✅ Rules saved in `/etc/sysconfig/iptables`.

---

## 📋 Quick Command Reference

Automation sequence for **all app servers**:

```bash
# Installation
sudo yum install -y iptables-services

# Rule Stack
sudo iptables -F
sudo iptables -A INPUT -i lo -j ACCEPT
sudo iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT
sudo iptables -A INPUT -p tcp -s 172.16.238.14 --dport 5002 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 5002 -j DROP
sudo iptables -A INPUT -j REJECT --reject-with icmp-host-prohibited

# Persistence
sudo service iptables save
sudo systemctl enable --now iptables
```

---

## 💡 Common Iptables Issues & Fixes

### **Issue 1: Locked out of SSH**
**Problem**: The ruleset was applied without allowing port 22 first or flushing was performed with a DROP default policy.
**Fix**: Always include the port 22 ALLOW rule before applying generic REJECT/DROP rules.

### **Issue 2: Rules Lost on Reboot**
**Problem**: Rules were applied but `service iptables save` was not executed.
**Fix**: The `iptables-services` package must be installed and the save command triggered to write the in-memory state to physical storage.

### **Issue 3: Loopback Communication Fails**
**Problem**: Applications fail to talk to themselves on localhost because the `lo` interface rule is missing.
**Fix**: Ensure `iptables -A INPUT -i lo -j ACCEPT` is present at the top of the chain.

---

## 🔧 Troubleshooting Failures

### **Error: service: command not found**
**Symptoms**: Unable to save rules.
**Solution**: Ensure the `iptables-services` package is installed. On newer systems, use `iptables-save > /etc/sysconfig/iptables`.

### **Error: iptables: No chain/target/match by that name**
**Symptoms**: Commands fail on entry.
**Solution**: Check for typos in flags (e.g., `-j DROP` vs `-j drop`). Iptables is case-sensitive for targets.

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The challenge is implementing specific port restrictions without breaking general connectivity (SSH) or existing application performance. Ordering is the critical factor in `iptables`.

**💡 Solution Approach**:
1. **Explicit Source Tagging**: Used the `-s` flag to specifically whitelist the LBR IP. This is the most surgical way to handle the request.
2. **Stateful Filtering**: Included the `ESTABLISHED,RELATED` rule. This ensures that outbound requests from the server (e.g., `yum update`) can receive response traffic without needing explicit inbound rules for every dynamic port.
3. **Fail-Safe Ordering**: Nested the LBR `ACCEPT` rule directly above the general `DROP` rule for port 5002 to ensure immediate processing.

---

## ⚠️ Important Production Notes

🔧 **Order of Operations**: Always apply "Accept" rules before "Reject/Drop" rules for the same port/protocol.
🔐 **Least Privilege**: Avoid using `-j DROP` as a default policy if you are unfamiliar with iptables, as it can lead to immediate lockouts. Use `REJECT` at the end of specific chains instead.
📊 **Logging**: You can add `-j LOG` rules to monitor who is attempting to reach port 5002.
🛡️ **Audit**: Regularly rotate and review the `/etc/sysconfig/iptables` file.

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Deployed **iptables-services** on all application servers.
- Whitelisted the **LBR host (172.16.238.14)** for port **5002**.
- Restricted all other traffic to port **5002** via the `DROP` target.
- Ensured system-wide persistence for firewall rules.
- Maintained administrative SSH access across the environment.

**🔍 Verification Completed**:
- **App Servers**: Success on stapp01, stapp02, and stapp03.
- **Persistence Audit**: Rules verified in `/etc/sysconfig/iptables`.
- **Logic Check**: `iptables -L` confirmed correct rule order.

**🔒 Configuration Summary**:
- **Protected Port**: 5002
- **Trusted Source**: 172.16.238.14
- **Persistence**: iptables-services
- **Scope**: All App Hosts
