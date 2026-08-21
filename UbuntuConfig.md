# UBUNTU CONFIGURATION

### Network Configuration (Netplan & IP)
* `ip addr` - Find the network interface (e.g., eth0)
* `ip route` - Check the routing table
* `ip neigh` - Check the nearby connectivity
* `sudo nano /etc/netplan/<yaml file>` - Edit netplan configuration
* `sudo netplan apply` - Apply netplan configuration

### Firewall Configuration (UFW)
* `ufw` - Uncomplicated firewall
* `sudo ufw status verbose` - Check detailed status of the firewall

### Package Management (APT)
* `apt` - Advance package tool; command line package manager for Debian-based Linux distri
* `sudo apt update` - Checks the latest updates of the repo in ubuntu
* `sudo apt upgrade` - Install the software upgrades
* `-y` - "Yes"; goes straight to upgrade and doesn't ask for confirmation

### Service Management (Systemctl)
* `systemctl` - Manager of the systems
* `systemctl status <service_name>` - Checks status of the certain service (if running or not)
* `systemctl start <service_name>` - Starts the service
* `systemctl stop <service_name>` - Ends the service
* `systemctl restart <service_name>` - Restart the service
* `systemctl reload <service_name>` - Reload its configuration files without dropping active connections

### System Privileges & User Management
* `sudo` - Administrative root access (run as administrator in windows)
* `-s` - Execute commands as root user (you can see "#" after)
* `su - <name>` - Switch to specific user
* `logout` - Logout from the specific user

### Mail Utilities
* `mail -s "<text>" youremail@mail.local` - Mail program and uses the -s flag to set the subject line to "<text>"
* `echo <email body / text>` - One line method to send an email without opening text editor
* `|` - Takes the output of the previous command (echo) and feeds it directly into the next command as the message body
* `d` <number of the email> - deletes an email of the specific number
* `q` - quit

### File Editing & General Operators
* `nano` - Text editor 
* `ls *<file type>` - List of a specific file type
* `&&` - Run multiple configuration at the same time