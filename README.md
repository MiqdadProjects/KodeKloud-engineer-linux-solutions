# 🌟 KodeKloud Engineer – Linux Solutions

**📌 Project Overview**

This repository contains solutions for Linux administration tasks from KodeKloud Engineer, organized by difficulty levels (Level 1 to Level 4). Each task is documented in a detailed Markdown file with step-by-step instructions, essential commands, verification steps, and troubleshooting tips. These solutions are designed to assist the Nautilus DevOps team in managing server infrastructure, user access, and system security efficiently across the Stratos Datacenter. Starting from basic user management to advanced system hardening and server configuration, this repository serves as a comprehensive record of my journey through the KodeKloud Engineer challenge. Note: While the core objectives and challenges remain consistent, the specific values (e.g., server names, IP addresses, or usernames) may differ based on your specific lab environment. However, the underlying concepts and technical approaches are broadly applicable to real-world Linux environments.

**🔍 Purpose**: Provide clear, executable solutions for Linux troubleshooting and configuration tasks, ensuring best practices in system administration.

**🛠️ Tools Used**:
- **SSH**: Secure access to app, db, and storage servers.
- **Package Managers**: `yum`, `apt`, `pip3` for software installation.
- **System Utilities**: `cron`, `firewalld`, `setfacl`, `systemd`, `tar`, `scp`.
- **Environment**: Stratos Datacenter lab environment with Jump Host access.

**📂 Repository Structure**:
```
kodekloud-engineer-linux-solutions/
├── LEVEL01/
│   ├── task-01-create-custom-apache-user-mark-stapp02.md
│   ├── task-02-create-group-add-user-nautilus-developers.md
│   ├── task-03-create-non-interactive-user-yousuf-stapp01.md
│   ├── task-04-create-service-user-ravi-no-home-stapp02.md
│   ├── task-05-create-temporary-user-john-expiry-stapp02.md
│   ├── task-06-copy-user-files-preserve-structure-stapp03.md
│   ├── task-07-disable-ssh-root-login-all-servers.md
│   ├── task-08-create-compressed-archive-storage-server.md
│   ├── task-09-grant-executable-permissions-script-stapp02.md
│   ├── task-10-configure-file-acls-permissions-stapp03.md
│   ├── task-11-replace-string-xml-file-backup-server.md
│   ├── task-12-copy-encrypted-file-scp-jump-to-stapp02.md
│   ├── task-13-configure-crontab-access-control-stapp02.md
│   ├── task-14-set-default-runlevel-graphical-all-servers.md
│   ├── task-15-synchronize-timezone-america-jamaica-all-servers.md
│   ├── task-16-configure-firewall-allow-port-8086-backup-server.md
│   ├── task-17-set-process-limits-nfsuser-storage-server.md
│   ├── task-18-install-selinux-packages-disable-stapp01.md
├── LEVEL02/
│   ├── task-01-install-cronie-configure-cron-job-all-servers.md
│   ├── task-02-update-motd-banner-all-servers.md
│   ├── task-03-setup-collaborative-directory-dbadmin-stapp03.md
│   ├── task-04-text-manipulation-grep-sed-stapp03.md
│   ├── task-05-setup-passwordless-ssh-jump-to-all-servers.md
│   ├── task-06-find-copy-php-files-preserve-structure-stapp02.md
│   ├── task-07-install-samba-package-all-servers.md
│   ├── task-08-install-ansible-4.9.0-jump-host-pip3.md
│   ├── task-09-configure-local-yum-repository-backup-server.md
│   ├── task-10-install-squid-all-app-servers.md
│   ├── task-11-allow-passwordless-sudo-rose-all-app-servers.md
│   ├── task-12-configure-dns-nameservers-stapp02.md
│   ├── task-13-configure-firewalld-all-app-servers.md
│   ├── task-14-setup-postfix-dovecot-mail-server-stmail01.md
│   ├── task-15-troubleshoot-postfix-service-stmail01.md
│   ├── task-16-install-configure-haproxy-stlb01.md
│   ├── task-17-troubleshoot-haproxy-service-stlb01.md
│   ├── task-18-troubleshoot-mariadb-service-stdb01.md
│   ├── task-19-create-website-backup-script-stapp01.md
│   ├── task-20-hardening-apache-response-headers-stapp01.md
│   ├── task-21-troubleshoot-apache-all-servers.md
│   ├── task-22-gpg-data-encryption-decryption-ststor01.md
│   ├── task-23-configure-logrotate-httpd-all-servers.md
│   ├── task-24-configure-iptables-backup-server.md
├── LEVEL03/
│   ├── task-01-configure-apache-url-redirects-stapp03.md
│   ├── task-02-configure-sftp-server-stapp02.md
│   ├── task-03-install-configure-tomcat-server-stapp03.md
│   ├── task-04-troubleshoot-apache-service-stapp01.md
│   ├── task-05-restrict-apache-port-iptables-all-servers.md
│   ├── task-06-setup-nginx-reverse-proxy-apache-stbkp01.md
│   ├── task-07-configure-apache-basic-auth-stapp03.md
│   ├── task-08-troubleshoot-apache-service-unavailability-stapp01.md
│   ├── task-09-configure-apache-pam-auth-stapp03.md
│   ├── task-10-setup-nginx-ssl-stapp02.md
├── LEVEL04/
│   🚧 **Coming Soon**
```
└── README.md
```

---

## 🚀 Level 1 Tasks

| Task # | Title/Link | Status | Difficulty | Description |
|--------|------------|--------|------------|-------------|
| 1 | [**Create Apache User Mark**](./LEVEL01/task-01-create-custom-apache-user-mark-stapp02.md) | ✅ **Done** | 🟢 Basic | Create a non-login user for Apache service on stapp02 |
| 2 | [**Create Group & Add User**](./LEVEL01/task-02-create-group-add-user-nautilus-developers.md) | ✅ **Done** | 🟢 Basic | Setup nautilus_developers group and add specific users |
| 3 | [**Create Non-Interactive User**](./LEVEL01/task-03-create-non-interactive-user-yousuf-stapp01.md) | ✅ **Done** | 🟢 Basic | Create a user without shell access for security on stapp01 |
| 4 | [**Create Service User**](./LEVEL01/task-04-create-service-user-ravi-no-home-stapp02.md) | ✅ **Done** | 🟢 Basic | Create a user without a home directory on stapp02 |
| 5 | [**Create Temporary User**](./LEVEL01/task-05-create-temporary-user-john-expiry-stapp02.md) | ✅ **Done** | 🟢 Basic | Create a user with a specific account expiration date |
| 6 | [**Copy User Files**](./LEVEL01/task-06-copy-user-files-preserve-structure-stapp03.md) | ✅ **Done** | 🟢 Basic | Copy files while preserving directory structure on stapp03 |
| 7 | [**Disable SSH Root Login**](./LEVEL01/task-07-disable-ssh-root-login-all-servers.md) | ✅ **Done** | 🟡 Intermediate | Harden SSH security by disabling root login on all servers |
| 8 | [**Create Compressed Archive**](./LEVEL01/task-08-create-compressed-archive-storage-server.md) | ✅ **Done** | 🟢 Basic | Create a .tar.gz archive of data on the storage server |
| 9 | [**Grant Executable Permissions**](./LEVEL01/task-09-grant-executable-permissions-script-stapp02.md) | ✅ **Done** | 🟢 Basic | Grant execute permissions to a shell script on stapp02 |
| 10 | [**Configure File ACLs**](./LEVEL01/task-10-configure-file-acls-permissions-stapp03.md) | ✅ **Done** | 🟡 Intermediate | Set specific user/group permissions using ACLs on stapp03 |
| 11 | [**Replace String in XML**](./LEVEL01/task-11-replace-string-xml-file-backup-server.md) | ✅ **Done** | 🟢 Basic | Use sed to replace strings in configuration files on backup server |
| 12 | [**Copy Encrypted File**](./LEVEL01/task-12-copy-encrypted-file-scp-jump-to-stapp02.md) | ✅ **Done** | 🟢 Basic | Use SCP to transfer files from Jump Host to App Server 2 |
| 13 | [**Configure Crontab Access**](./LEVEL01/task-13-configure-crontab-access-control-stapp02.md) | ✅ **Done** | 🟡 Intermediate | Manage cron.allow and cron.deny for user access control |
| 14 | [**Set Default Runlevel**](./LEVEL01/task-14-set-default-runlevel-graphical-all-servers.md) | ✅ **Done** | 🟢 Basic | Change systemd default target to graphical on all servers |
| 15 | [**Synchronize Timezone**](./LEVEL01/task-15-synchronize-timezone-america-jamaica-all-servers.md) | ✅ **Done** | 🟢 Basic | Set the correct system timezone across all infrastructure |
| 16 | [**Configure Firewall Rule**](./LEVEL01/task-16-configure-firewall-allow-port-8086-backup-server.md) | ✅ **Done** | 🟡 Intermediate | Allow specific traffic through iptables/firewalld on backup server |
| 17 | [**Set Process Limits**](./LEVEL01/task-17-set-process-limits-nfsuser-storage-server.md) | ✅ **Done** | 🟡 Intermediate | Configure security limits (ulimit) for users on storage server |
| 18 | [**Install & Disable SELinux**](./LEVEL01/task-18-install-selinux-packages-disable-stapp01.md) | ✅ **Done** | 🟡 Intermediate | Manage SELinux packages and operational states on stapp01 |

---

## 🚀 Level 2 Tasks

| Task # | Title/Link | Status | Difficulty | Description |
|--------|------------|--------|------------|-------------|
| 1 | [**Install Cronie & Cron Job**](./LEVEL02/task-01-install-cronie-configure-cron-job-all-servers.md) | ✅ **Done** | 🟡 Intermediate | Install cronie and schedule recurring system tasks |
| 2 | [**Update MOTD Banner**](./LEVEL02/task-02-update-motd-banner-all-servers.md) | ✅ **Done** | 🟢 Basic | Update the message of the day banner on all app servers |
| 3 | [**Setup Collaborative Directory**](./LEVEL02/task-03-setup-collaborative-directory-dbadmin-stapp03.md) | ✅ **Done** | 🟡 Intermediate | Configure group-shared directories with SGID bit on stapp03 |
| 4 | [**Text Manipulation (Grep/Sed)**](./LEVEL02/task-04-text-manipulation-grep-sed-stapp03.md) | ✅ **Done** | 🟡 Intermediate | Advanced log filtering and text processing on stapp03 |
| 5 | [**Setup Passwordless SSH**](./LEVEL02/task-05-setup-passwordless-ssh-jump-to-all-servers.md) | ✅ **Done** | 🟡 Intermediate | Configure SSH key-based authentication from Jump Host |
| 6 | [**Find & Copy PHP Files**](./LEVEL02/task-06-find-copy-php-files-preserve-structure-stapp02.md) | ✅ **Done** | 🟡 Intermediate | Find specific file types and migrate them to new directories |
| 7 | [**Install Samba Package**](./LEVEL02/task-07-install-samba-package-all-servers.md) | ✅ **Done** | 🟡 Intermediate | Deploy Samba for file sharing across the Nautilus network |
| 8 | [**Install Ansible (Pip3)**](./LEVEL02/task-08-install-ansible-4.9.0-jump-host-pip3.md) | ✅ **Done** | 🟡 Intermediate | Install specific Ansible version using python-pip on Jump Host |
| 9 | [**Configure Local Yum Repo**](./LEVEL02/task-09-configure-local-yum-repository-backup-server.md) | ✅ **Done** | 🟡 Intermediate | Setup local package repository for offline updates on backup server |
| 10 | [**Install Squid Proxy**](./LEVEL02/task-10-install-squid-all-app-servers.md) | ✅ **Done** | 🟡 Intermediate | Deploy Squid proxy server for managed internet access |
| 11 | [**Allow Passwordless Sudo**](./LEVEL02/task-11-allow-passwordless-sudo-rose-all-app-servers.md) | ✅ **Done** | 🟡 Intermediate | Grant sudo privileges without password prompts to specific users |
| 12 | [**Configure DNS Nameservers**](./LEVEL02/task-12-configure-dns-nameservers-stapp02.md) | ✅ **Done** | 🟢 Basic | Update resolver configuration for proper name resolution |
| 13 | [**Configure Firewalld**](./LEVEL02/task-13-configure-firewalld-all-app-servers.md) | ✅ **Done** | 🟡 Intermediate | Setup persistent firewalld rules for application traffic |
| 14 | [**Setup Postfix & Dovecot**](./LEVEL02/task-14-setup-postfix-dovecot-mail-server-stmail01.md) | ✅ **Done** | 🟡 Intermediate | Deploy Postfix and Dovecot mail servers on stmail01 |
| 15 | [**Troubleshoot Postfix**](./LEVEL02/task-15-troubleshoot-postfix-service-stmail01.md) | ✅ **Done** | 🟡 Intermediate | Resolve issues with Postfix mail service on stmail01 |
| 16 | [**Install & Configure HAProxy**](./LEVEL02/task-16-install-configure-haproxy-stlb01.md) | ✅ **Done** | 🟡 Intermediate | Deploy and configure HAProxy load balancer on stlb01 |
| 17 | [**Troubleshoot HAProxy**](./LEVEL02/task-17-troubleshoot-haproxy-service-stlb01.md) | ✅ **Done** | 🟡 Intermediate | Resolve issues with HAProxy service on stlb01 |
| 18 | [**Troubleshoot MariaDB**](./LEVEL02/task-18-troubleshoot-mariadb-service-stdb01.md) | ✅ **Done** | 🟡 Intermediate | Resolve issues with MariaDB database service on stdb01 |
| 19 | [**Create Website Backup Script**](./LEVEL02/task-19-create-website-backup-script-stapp01.md) | ✅ **Done** | 🟡 Intermediate | Create a shell script for automated website backups on stapp01 |
| 20 | [**Hardening Apache Headers**](./LEVEL02/task-20-hardening-apache-response-headers-stapp01.md) | ✅ **Done** | 🟡 Intermediate | Secure Apache by modifying response headers on stapp01 |
| 21 | [**Troubleshoot Apache**](./LEVEL02/task-21-troubleshoot-apache-all-servers.md) | ✅ **Done** | 🟡 Intermediate | Resolve Apache service issues across all app servers |
| 22 | [**GPG Data Encryption**](./LEVEL02/task-22-gpg-data-encryption-decryption-ststor01.md) | ✅ **Done** | 🟡 Intermediate | Encrypt and decrypt data using GPG on storage server |
| 23 | [**Configure Logrotate**](./LEVEL02/task-23-configure-logrotate-httpd-all-servers.md) | ✅ **Done** | 🟡 Intermediate | Configure log rotation for Apache HTTPD on all servers |
| 24 | [**Configure Iptables**](./LEVEL02/task-24-configure-iptables-backup-server.md) | ✅ **Done** | 🟡 Intermediate | Setup persistent iptables rules on the backup server |

---

## 🚀 Level 3 Tasks

| Task # | Title/Link | Status | Difficulty | Description |
|--------|------------|--------|------------|-------------|
| 1 | [**Apache URL Redirects**](./LEVEL03/task-01-configure-apache-url-redirects-stapp03.md) | ✅ **Done** | � Intermediate | Configure URL redirection in Apache on stapp03 |
| 2 | [**Configure SFTP Server**](./LEVEL03/task-02-configure-sftp-server-stapp02.md) | ✅ **Done** | � Intermediate | Setup secure file transfer server on stapp02 |
| 3 | [**Install & Configure Tomcat**](./LEVEL03/task-03-install-configure-tomcat-server-stapp03.md) | ✅ **Done** | 🟡 Intermediate | Deploy Apache Tomcat server on stapp03 |
| 4 | [**Troubleshoot Apache Service**](./LEVEL03/task-04-troubleshoot-apache-service-stapp01.md) | ✅ **Done** | 🟠 Advanced | Identify and resolve Apache service errors on stapp01 |
| 5 | [**Restrict Apache Port**](./LEVEL03/task-05-restrict-apache-port-iptables-all-servers.md) | ✅ **Done** | � Intermediate | Use iptables to restrict access to specific ports |
| 6 | [**Nginx Reverse Proxy**](./LEVEL03/task-06-setup-nginx-reverse-proxy-apache-stbkp01.md) | ✅ **Done** | 🟠 Advanced | Setup Nginx as a reverse proxy for Apache on stbkp01 |
| 7 | [**Apache Basic Auth**](./LEVEL03/task-07-configure-apache-basic-auth-stapp03.md) | ✅ **Done** | 🟡 Intermediate | Implement basic HTTP authentication for Apache |
| 8 | [**Troubleshoot Apache Unavailability**](./LEVEL03/task-08-troubleshoot-apache-service-unavailability-stapp01.md) | ✅ **Done** | 🟠 Advanced | Troubleshoot why Apache service is not responding |
| 9 | [**Apache PAM Auth**](./LEVEL03/task-09-configure-apache-pam-auth-stapp03.md) | ✅ **Done** | 🟠 Advanced | Configure Apache to use PAM for user authentication |
| 10 | [**Setup Nginx SSL**](./LEVEL03/task-10-setup-nginx-ssl-stapp02.md) | ✅ **Done** | 🟠 Advanced | Configure SSL/TLS for Nginx web server on stapp02 |

---

## 🚀 Level 4 Tasks

| Task # | Title/Link | Status | Difficulty | Description |
|--------|------------|--------|------------|-------------|
| 1 | [**Task Placeholder...**](#) | 🚧 **Coming Soon** | 🔴 Critical | Tasks in Level 4 are being prepared... |
| 2 | [**Task Placeholder...**](#) | 🚧 **Coming Soon** | 🔴 Critical | Tasks in Level 4 are being prepared... |
| 3 | [**Task Placeholder...**](#) | 🚧 **Coming Soon** | 🔴 Critical | Tasks in Level 4 are being prepared... |

---

## 💡 How to Use This Repository

1. **Clone the Repository**:
   ```bash
   git clone https://github.com/MiqdadProjects/KodeKloud-engineer-linux-solutions.git
   cd KodeKloud-engineer-linux-solutions
   ```

2. **Navigate to Task Files**:
   - Level 1 tasks: `LEVEL01/`
   - Level 2 tasks: `LEVEL02/`
   - Level 3 tasks: `LEVEL03/`

3. **Explore Solutions**:
   - Read the Markdown files to understand the requirements and the solution.
   - Each file contains the commands used during the challenge to complete the task.

4. **Troubleshooting**:
   - Refer to the verification sections in each task to validate your own environment.
   - Use standard Linux debugging tools like `journalctl`, `systemctl status`, and `tail -f`.

5. **Test in Lab Environment**:
   - Use the provided context to replicate these setups in your own virtualized environments or KodeKloud labs.

---

## 🔧 Prerequisites

- **Linux Knowledge**: Basic understanding of the Linux command line (Bash).
- **Environment**: Access to a Linux terminal (CentOS/Ubuntu preferred as per labs).
- **Text Editor**: Familiarity with `vi`, `vim`, or `nano` for file editing.
- **Root/Sudo Access**: Required for most configuration and installation tasks.

---

## 📋 Detailed Setup Guide

1. **Access the Servers**:
   ```bash
   ssh [username]@[server-name]
   # Example: ssh steve@stapp02
   ```

2. **Follow Task Instructions**:
   - Locate the relevant `.md` file in the `LEVEL` folders.
   - Execute commands sequentially as documented.

3. **Verify Configuration**:
   ```bash
   # Verify user creation
   id [username]
   # Verify service status
   systemctl status [service-name]
   # Verify permissions
   getfacl [file-path]
   ```

4. **Common Troubleshooting**:
   - Ensure you have the correct permissions (use `sudo`).
   - Double-check firewall rules if services are unreachable.
   - Verify package availability in your configured repositories.

---

## ⚠️ Important Notes

- **Task Files**: Structured as `task-XX-description.md` for consistent navigation and search.
- **Environment Specifics**: Always replace placeholder values like server names or IP addresses with your actual lab credentials.
- **Permanence**: Use the `-P` or `--permanent` flag with `firewall-cmd` to ensure rules survive reboots.
- **Level Completion**: Levels 1, 2, and 3 are fully documented.

---

## 🚀 Contributing

Contributions are welcome! If you find an error or want to improve a solution:
1. Fork the repository.
2. Create a new branch: `git checkout -b fix-task-XX`.
3. Update the relevant Markdown file.
4. Commit your changes: `git commit -m "Improve Task XX documentation"`.
5. Push and open a Pull Request.

---

## 📜 License

This project is licensed under the MIT License. See the [LICENSE](./LICENSE) file for details.

---

## 📞 Contact

For questions regarding these solutions or the CodeCloud Engineer challenge, please reach out via the repository’s issue tracker.

**Happy Linuxing!** 🚀
