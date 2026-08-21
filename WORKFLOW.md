# VM Workflow Setup Guide

This guide covers the complete setup for the Windows and Ubuntu virtual machines used in the LAN environment.

---

# Overall Tasks

1. Enable file sharing
2. Restore the AdventureWorks database and make it accessible to everyone
3. Configure SQL Server Authentication
   - Create an application user that can connect over the Local Area Network
4. Configure an Ubuntu Mail Server
   - Create email accounts
   - Send and receive emails

---

# Windows VM Setup

## 1. Configure Network

### Check the current IP address

```powershell
ipconfig
```

- Verify that the machine received an IP address from DHCP.
- Once confirmed, change it to a **static IP** using the same network configuration received from DHCP.

---

## 2. Test Connectivity

### Ping other machines on the LAN

```powershell
ping <IP Address>
```

If ping fails:

- Check Windows Firewall.
- Ensure the **ICMP Echo Request** firewall rule is enabled.

### Verify File Sharing

Open File Explorer and browse to:

```
\\SKANLOGWIN11
```

The shared folders should be visible.

---

## 3. Configure IIS

Enable IIS through Windows Features:

```
Control Panel
    → Programs and Features
        → Turn Windows features on or off
            → Internet Information Services
```

Enable the firewall rule:

```
Windows Defender Firewall
    → Advanced Settings
        → Inbound Rules
            → World Wide Web Services (HTTP Traffic-In)
                → Enable Rule
```

---

## 4. Configure SQL Server

Verify the following services are running:

- SQL Server
- SQL Server Agent

Open SQL Server Management Studio (SSMS).

Server Name:

```
10.98.2.168
```

Authentication:

```
SQL Server Authentication
```

Credentials:

```
Username:
SKAN-SQL

Password:
Skanlogserver2026
```

Verify that the AdventureWorks database has been restored successfully.

---

# Ubuntu VM Setup

## 1. Configure Networking

### Check the assigned IP

```bash
ip addr
```

Verify the Ethernet interface.

---

### Locate the Netplan YAML file

```bash
ls /etc/netplan
```

Example:

```
00-installer-config.yaml
```

Edit the file:

```bash
sudo nano /etc/netplan/<yaml-file>
```

Example configuration:

```yaml
network:
  version: 2
  renderer: networkd

  ethernets:
    eth0:
      dhcp4: false
      addresses:
        - 10.98.2.50/24

      routes:
        - to: default
          via: 10.98.2.1

      nameservers:
        addresses:
          - 8.8.8.8
```

Save the file:

```
Ctrl + O
Enter
Ctrl + X
```

Apply the configuration:

```bash
sudo netplan apply
```

Verify:

```bash
ip addr
```

Ensure the IP is now static (not dynamic).

---

## 2. Verify Connectivity

Display neighboring devices:

```bash
ip neigh
```

Test connectivity:

```bash
ping <IP Address>
```

Test both directions (Ubuntu ↔ Windows).

If connectivity fails:

Hyper-V Settings

```
Ubuntu VM
    → Settings
        → Network Adapter
            → Advanced Features
                → Enable MAC Address Spoofing
```

---

# Ubuntu Mail Server

Software Used:

- Postfix (SMTP)
- Dovecot (IMAP / POP3)

---

## 1. Update Ubuntu

```bash
sudo apt update && sudo apt upgrade -y
```

---

## 2. Install Postfix

```bash
sudo apt install postfix -y
```

Configuration:

```
Internet Site
```

System Mail Name:

```
mail.local
```

Email format:

```
username@mail.local
```

---

## 3. Install Dovecot

```bash
sudo apt install dovecot-imapd dovecot-pop3d -y
```

---

## 4. Enable Services

```bash
sudo systemctl enable postfix
sudo systemctl enable dovecot

sudo systemctl start postfix
sudo systemctl start dovecot
```

Verify:

```bash
sudo systemctl status postfix
sudo systemctl status dovecot
```

Both services should show:

```
Active: active (running)
```

---

## 5. Create Mail Accounts

Create a Linux user:

```bash
sudo adduser spacb
```

Email address:

```
spacb@mail.local
```

---

## 6. Configure Firewall

Allow SMTP:

```bash
sudo ufw allow 25
```

Allow IMAP:

```bash
sudo ufw allow 143
```

Allow POP3:

```bash
sudo ufw allow 110
```

Allow Secure IMAP:

```bash
sudo ufw allow 993
```

Allow Secure POP3:

```bash
sudo ufw allow 995
```

---

## 7. Install Mail Utilities

```bash
sudo apt install mailutils -y
```

---

## 8. Send a Test Email

```bash
echo "Hello" | mail -s "Test Email" spacb
```

---

## 9. Check Received Mail

Switch to the user:

```bash
su - spacb
```

Open the mailbox:

```bash
mail
```

Select the message number to read it.

---

## 10. Monitor Mail Logs

```bash
sudo tail -f /var/log/mail.log
```

A successful send will display:

```
status=sent
```

---

## 11. View Existing Mail Accounts

```bash
cat /etc/passwd
```

---

# Configuration Summary

## Windows

- Configure static IP
- Verify LAN connectivity
- Enable File Sharing
- Install and configure IIS
- Enable HTTP Firewall Rule
- Start SQL Server services
- Restore AdventureWorks database
- Configure SQL Server Authentication

---

## Ubuntu

- Configure static IP using Netplan
- Verify LAN connectivity
- Enable MAC Address Spoofing if required
- Install Postfix
- Install Dovecot
- Create mail users
- Configure firewall ports
- Test sending and receiving mail
- Verify mail logs

---

# Sample Credentials

```
SQL Server

Username:
SKAN-SQL

Password:
Skanlogserver2026
```

```
Mail Domain

mail.local
```

Example email:

```
spacb@mail.local
```

Reference Contact:

```
09756745363
```
