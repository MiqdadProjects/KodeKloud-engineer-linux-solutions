# 🔐 Task 1 - Configure Apache URL Redirects on App Server 3

**📌 Task Description**

The **Nautilus DevOps** team has received updated requirements for the **Apache (httpd)** configuration on **App Server 3 (stapp03)** within the **Stratos Datacenter**. These changes are focused on optimizing web navigation and ensuring SEO best practices through specific URL redirection strategies. The task involves pivoting the web server to a non-standard port (**3000**) and implementing both permanent (301) and temporary (302) redirects to standardize the domain access and handle content migration paths.

**👉 Task Requirements**:
- **Target Server**: App Server 3 (`stapp03`)
- **Service**: Apache (`httpd`)
- **Port**: `3000` (Listening Port)
- **Specific Redirects**:
  - **Redirect A (Permanent - 301)**: 
    - From: `http://stapp03.stratos.xfusioncorp.com:3000/` (Non-www)
    - To: `http://www.stapp03.stratos.xfusioncorp.com:3000/` (www-prefixed)
  - **Redirect B (Temporary - 302)**:
    - From: `http://www.stapp03.stratos.xfusioncorp.com:3000/blog/`
    - To: `http://www.stapp03.stratos.xfusioncorp.com:3000/news/`
- **Operation Objectives**:
  - Modify `httpd.conf` to listen on the specified port.
  - Define the `ServerName` for the host.
  - Create a custom redirection configuration in `conf.d`.
  - Validate syntax using `apachectl`.
  - Restart and enable the service for persistent application.
- Maintain standard document root association for the target content.
- Verify redirect behaviors through iterative testing.

**💡 Note**: Server credentials and infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to App Server 3

```bash
ssh banner@stapp03
```

**Purpose**: Establish an administrative SSH session to the application server.

**Expected Output**:
```
banner@stapp03's password: 
Welcome to App Server 3 - Stratos Datacenter
[banner@stapp03 ~]$
```

**Success Indicators**:
- ✅ Successfully authenticated as user `banner`
- ✅ Switched to root using `sudo -i`

---

## 🔹 Step 2: Configure Custom Listening Port

```bash
sudo vi /etc/httpd/conf/httpd.conf
```

**Purpose**: Reconfigure Apache to bind to the required port.

**Changes Required**:
1. Find the `Listen` directive:
   ```apache
   # Change from:
   Listen 80
   # Change to:
   Listen 3000
   ```
2. Update the `ServerName` identity (often at the end of the global config):
   ```apache
   ServerName stapp03.stratos.xfusioncorp.com
   ```

**Success Indicators**:
- ✅ `httpd.conf` successfully updated.
- ✅ Port 80 reference removed or commented out.

---

## 🔹 Step 3: Implement Redirection Logic

```bash
sudo vi /etc/httpd/conf.d/redirect.conf
```

**Purpose**: Create a modular configuration file to handle the specific redirection rules using VirtualHost blocks.

**Configuration Content**:
```apache
# -----------------------------
# 1) Non-www to www (Permanent 301)
# -----------------------------
<VirtualHost *:3000>
    ServerName stapp03.stratos.xfusioncorp.com

    # Standardize domain for SEO (301 Permanent)
    Redirect 301 / http://www.stapp03.stratos.xfusioncorp.com:3000/
</VirtualHost>


# -----------------------------
# 2) Main www VirtualHost
# -----------------------------
<VirtualHost *:3000>
    ServerName www.stapp03.stratos.xfusioncorp.com
    DocumentRoot /var/www/html

    # Path migration (302 Temporary)
    Redirect 302 /blog/ http://www.stapp03.stratos.xfusioncorp.com:3000/news/
</VirtualHost>
```

**Redirection Details**:
- **301 (Moved Permanently)**: Tells browsers and search engines that the URL has moved for good.
- **302 (Found/Moved Temporarily)**: Indicates the content is elsewhere for now, useful for ongoing site updates or temporary maintenance paths.

---

## 🔹 Step 4: Validate Configuration Syntax

```bash
sudo httpd -t
```
*Or:*
```bash
sudo apachectl configtest
```

**Expected Output**:
```
Syntax OK
```

**Success Indicators**:
- ✅ Configuration validation passed without errors.
- ✅ No conflicts between the main config and the `redirect.conf`.

---

## 🔹 Step 5: Activate and Persist Changes

```bash
sudo systemctl enable httpd
sudo systemctl restart httpd
```

**Purpose**: Apply the new port and redirect rules and ensure the service survives reboots.

**Success Indicators**:
- ✅ Service status shows `active (running)`.
- ✅ Apache listening on port `3000` (verify with `ss -tulpn | grep 3000`).

---

## 🔹 Step 6: Final Verification

Perform testing to ensure both redirect types function as intended.

```bash
# 1. Test 301 Redirect (Non-www to www)
curl -I http://stapp03.stratos.xfusioncorp.com:3000/

# 2. Test 302 Redirect (Blog to News)
curl -I http://www.stapp03.stratos.xfusioncorp.com:3000/blog/
```

**Expected Results**:
- **Test 1 Output**: `HTTP/1.1 301 Moved Permanently` | `Location: http://www.stapp03...`
- **Test 2 Output**: `HTTP/1.1 302 Found` | `Location: http://www.stapp03.../news/`

**Success Indicators**:
- ✅ Correct HTTP status codes received.
- ✅ Target locations properly formatted with port 3000.

---

## 📋 Quick Command Reference

Execute these steps on **stapp03**:

```bash
# Set Port
sudo sed -i 's/Listen 80/Listen 3000/g' /etc/httpd/conf/httpd.conf

# Deploy Redirects
cat <<EOF | sudo tee /etc/httpd/conf.d/redirect.conf
<VirtualHost *:3000>
    ServerName stapp03.stratos.xfusioncorp.com
    Redirect 301 / http://www.stapp03.stratos.xfusioncorp.com:3000/
</VirtualHost>

<VirtualHost *:3000>
    ServerName www.stapp03.stratos.xfusioncorp.com
    DocumentRoot /var/www/html
    Redirect 302 /blog/ http://www.stapp03.stratos.xfusioncorp.com:3000/news/
</VirtualHost>
EOF

# Restart
sudo systemctl enable --now httpd
```

---

## 💡 Common Redirection Issues & Fixes

### **Issue 1: Infinite Redirect Loops**
**Problem**: The redirect points back to itself.
**Fix**: Ensure your `VirtualHost` blocks have distinct `ServerName` directives so they don't trigger each other.

### **Issue 2: Browser Cache Interference**
**Problem**: After changing a 301 redirect, the browser still goes to the old location.
**Fix**: Use `curl` or Private/Incognito mode to verify changes, as 301 redirects are heavily cached by browsers.

### **Issue 3: Port Mismatch in URL**
**Problem**: The redirect works but strips the port number from the URL.
**Fix**: Explicitly include `:3000` in the target URL of the `Redirect` directive.

---

## 🔧 Troubleshooting Failures

### **Error: ... cannot bind to address [::]:3000**
**Symptoms**: Apache fails to start.
**Solution**: Check for other processes using port 3000 or ensure SELinux is permitting the use of this custom port.

### **Error: HTTP status 403 Forbidden**
**Symptoms**: Redirect happens, but the target page is restricted.
**Solution**: Check the permissions of the `DocumentRoot` and ensure `index.html` exists in the target directory (e.g., `/var/www/html/news/`).

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
Differentiating between permanent and temporary redirects requires understanding the impact on both user experience and search engine optimization. A 301 redirect passes SEO authority to the new URL, whereas a 302 does not.

**💡 Solution Approach**:
1. **Modular Configuration**: Placed the logic in `conf.d/redirect.conf` instead of the main `httpd.conf` for easier management and cleaner audits.
2. **Explicit Port Handling**: Included the port in all redirect targets to ensure the browser remains on the non-standard port throughout the session.
3. **Hierarchy Verification**: Used `VirtualHost` blocks to cleanly separate the behavior of the non-www and www hostnames.

---

## ⚠️ Important Production Notes

🔧 **SEO Hygiene**: Only use 301 redirects for content that has moved for good. 302 is for temporary measures only.
🔐 **Security**: Ensure that no other administrative ports (like 80 or 443) remain open if they are not actively serving traffic.
📊 **Traffic Logs**: Check the access logs to verify that the redirect is being hit.
🛡️ **Cleanup**: Remove any legacy `.htaccess` files that might contain competing redirect rules.

---

## 🎯 Task Completion Summary

**✅ Objectives Achieved**:
- Reconfigured **Apache** to listen on port **3000** on `stapp03`.
- Implemented a **301 Permanent Redirect** for non-www domain traffic.
- Implemented a **302 Temporary Redirect** for the `/blog/` path.
- Verified service persistence and operational availability.
- Validated redirection behavior via `curl`.

**🔍 Verification Completed**:
- **stapp03**: HTTP service active on port 3000.
- **Redirects**: Functional logic confirmed via status codes 301 and 302.

**🔒 Configuration Summary**:
- **Port**: 3000
- **301 Map**: non-www -> www
- **302 Map**: /blog/ -> /news/
- **Server**: stapp03
