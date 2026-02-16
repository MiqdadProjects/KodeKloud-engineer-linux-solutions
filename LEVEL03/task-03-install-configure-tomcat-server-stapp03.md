# 🔐 Task 03 - Install and Configure Tomcat Server on stapp03

**📌 Task Description**

The **Nautilus** application development team has finalized the beta version of a Java-based application. Following the technical roadmap, the team has decided to deploy this application using the **Apache Tomcat** application server on **App Server 3 (stapp03)** in the **Stratos Datacenter**. This task involves the installation of the Tomcat environment, reconfiguration of its default listening port to **8083**, and the deployment of a specialized `ROOT.war` file to ensure the application is accessible directly via the server's base URL.

**👉 Task Requirements**:
- **Target Server**: App Server 3 (`stapp03`)
- **Application Server**: Apache Tomcat
- **Custom Port**: `8083` (Default is 8080)
- **Deployment Artifact**: `/tmp/ROOT.war` (From Jump Host)
- **Operation Objectives**:
  - Install Tomcat package using the system package manager.
  - Modify `server.xml` to update the HTTP connector port.
  - Deploy the `ROOT.war` file to the Tomcat webapps directory.
  - Ensure correct ownership/permissions for the deployed artifact.
  - Enable and start the Tomcat service.
- Maintain base URL accessibility (no sub-context required in URL).
- Verify successful deployment via `curl` on the specified port.

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to App Server 3

```bash
ssh banner@stapp03
```

**Purpose**: Establish an administrative session to prepare the application environment.

**Expected Output**:
```
banner@stapp03's password: 
Welcome to App Server 3 - Stratos Datacenter
[banner@stapp03 ~]$
```

**Success Indicators**:
- ✅ Successfully authenticated as user `banner`
- ✅ Switched to root using `sudo su -`

---

## 🔹 Step 2: Install Tomcat Server

```bash
sudo yum install -y tomcat
```

**Purpose**: Provision the Java Servlet container and associated libraries.

**Success Indicators**:
- ✅ Package installed without dependency errors.
- ✅ Directory `/var/lib/tomcat/webapps` created.

---

## 🔹 Step 3: Configure Custom Listening Port

```bash
sudo vi /etc/tomcat/server.xml
```

**Purpose**: Change the default HTTP port to 8083 as per the project requirements.

**Workflow**:
1. Search for the `Connector` tag with `port="8080"`.
2. Update the port value:
```xml
<Connector port="8083" protocol="HTTP/1.1"
           connectionTimeout="20000"
           redirectPort="8443" />
```

**Success Indicators**:
- ✅ `server.xml` updated with `port="8083"`.

---

## 🔹 Step 4: Transfer Deployment Artifact

Perform this step from the **Jump Host**:

```bash
scp /tmp/ROOT.war banner@stapp03:/tmp/
```

**Purpose**: Move the prepared application archive from the centralized distribution point to the target server's temporary storage.

---

## 🔹 Step 5: Deploy the Application

Switch back to the **stapp03** session:

```bash
# Move the WAR file to the Tomcat deployment directory
sudo mv /tmp/ROOT.war /var/lib/tomcat/webapps/

# Ensure the tomcat service can read/process the file
sudo chown tomcat:tomcat /var/lib/tomcat/webapps/ROOT.war
```

**Purpose**: Placing the file in `webapps/` as `ROOT.war` ensures that Tomcat deploys it as the primary application for the root context (/).

**Success Indicators**:
- ✅ `ROOT.war` placed in `/var/lib/tomcat/webapps/`.
- ✅ Ownership assigned to the `tomcat` system user.

---

## 🔹 Step 6: Activate and Persist Service

```bash
sudo systemctl enable tomcat
sudo systemctl start tomcat
```

**Purpose**: Initialize the Java process and ensure it starts automatically on system boot.

**Success Indicators**:
- ✅ Service status shows `active (running)`.
- ✅ Standard boot persistence enabled.

---

## 🔹 Step 7: Final Verification

```bash
curl http://stapp03:8083
```

**Expected Output**:
```html
Welcome to xFusionCorp Industries! This is a Java-based application.
```

**Success Indicators**:
- ✅ Application responds correctly on port 8083.
- ✅ Webpage content is visible without a sub-context path (e.g., /ROOT or /app).

---

## 📋 Quick Command Reference

Tomcat Rollout for **stapp03**:

```bash
# 1. Install
sudo yum install -y tomcat

# 2. Port Adjustment
sudo sed -i 's/port="8080"/port="8083"/g' /etc/tomcat/server.xml

# 3. Deployment (Assuming file is in /tmp)
sudo mv /tmp/ROOT.war /var/lib/tomcat/webapps/
sudo chown tomcat:tomcat /var/lib/tomcat/webapps/ROOT.war

# 4. Service Control
sudo systemctl enable --now tomcat

# 5. Local Audit
curl -I localhost:8083
```

---

## 💡 Common Tomcat Issues & Fixes

### **Issue 1: Port Already in Use**
**Problem**: Another service is using port 8083.
**Fix**: Check for conflicting processes.
```bash
sudo ss -tulpn | grep :8083
```

### **Issue 2: WAR File Not Exploding**
**Problem**: Tomcat is running but doesn't extract the `ROOT.war` file.
**Fix**: Check permissions on the `webapps` directory and ensure the `tomcat` user has write access. Also, check for Java version mismatches in `/var/log/tomcat/catalina.out`.

### **Issue 3: Memory Exhaustion**
**Problem**: Tomcat fails to start due to low JVM memory.
**Fix**: Adjust memory settings in `/etc/tomcat/tomcat.conf` (e.g., `JAVA_OPTS="-Xms512m -Xmx1024m"`).

---

## 🔧 Troubleshooting Failures

### **Error: Failed to bind to server socket: [::]:8083**
**Symptoms**: Service crashes on startup.
**Solution**: Verify that no other `Connector` in `server.xml` is using the same port and that the firewall allows the bind operation.

### **Error: HTTP status 404 (Not Found)**
**Symptoms**: Tomcat starts but the URL returns a 404.
**Solution**: Check that the WAR file is named *exactly* `ROOT.war` (case-sensitive). If named differently, the application will be served at `/:filename/`.

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
Deploying a Java application as the "Root" application requires specific naming conventions in the Tomcat `webapps` directory. If the file were named `app-beta.war`, it would be accessible at `/:8083/app-beta/`, which would fail the requirement to work on the base URL.

**💡 Solution Approach**:
1. **Context Mapping**: Renamed the artifact to `ROOT.war` during deployment.
2. **Standardization**: Used the official `yum` package for Tomcat to ensure logs and configuration paths follow the standard RHEL/CentOS layout.
3. **Connectivity Validation**: Performed the audit from both the jump host and locally to ensure no internal firewalls were intercepting the custom port 8083.

---

## ⚠️ Important Production Notes

🔧 **Logging**: Monitor `/var/log/tomcat/catalina.out` for detailed application-level errors.
🔐 **Security**: In production, disable the Tomcat Manager and Host Manager apps if not required to reduce the attack surface.
📊 **Monitoring**: Add checks for the JVM heap usage and thread counts.
🛡️ **Firewall**: Ensure port 8083 is explicitly allowed if `firewalld` is active.

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Installed **Apache Tomcat** on App Server 3.
- Standardized the HTTP connector to port **8083**.
- Deployed the `ROOT.war` application archive to the primary context.
- Verified service persistence and functional deployment.

**🔍 Verification Completed**:
- **stapp03**: `systemctl status` confirmed as **ACTIVE**.
- **URL Check**: Application reachable at `http://stapp03:8083`.

**🔒 Configuration Summary**:
- **Port**: 8083
- **Context**: Root (/)
- **Artifact**: ROOT.war
- **Service**: tomcat
