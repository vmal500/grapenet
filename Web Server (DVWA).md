# DVWA Installation Guide for Ubuntu Server 22.04 LTS

This guide walks through setting up Damn Vulnerable Web Application (DVWA) on Ubuntu Server 22.04 LTS. DVWA is a vulnerable web application designed for security testing practice.

## Prerequisites

Before beginning, ensure you have:
- Linux/Ubuntu system (or VM)
- Internet connection (bridged or NAT mode for setup)
- Root or sudo access

## Installation Steps

### 1. Install LAMP Stack

First, we'll set up the LAMP (Linux, Apache, MySQL, PHP) environment.

```bash
# Update package list
sudo apt update

# Install Apache, MySQL, and PHP
sudo apt install apache2 mysql-server php libapache2-mod-php php-mysql -y

# Enable and start Apache
sudo systemctl enable apache2
sudo systemctl start apache2

# Enable and start MySQL
sudo systemctl enable mysql
sudo systemctl start mysql
```

### 2. Database Configuration

Set up the MySQL database for DVWA:

```bash
# Secure MySQL installation (recommended)
sudo mysql_secure_installation
```

Log in to MySQL and create the database:

```sql
# Log into MySQL
sudo mysql -u root -p

# Create database and user (run these commands in MySQL prompt)
CREATE DATABASE dvwa;
CREATE USER 'dvwauser'@'localhost' IDENTIFIED BY 'dvwapassword';
GRANT ALL PRIVILEGES ON dvwa.* TO 'dvwauser'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

**Note**: Replace `'dvwapassword'` with a secure password.

### 3. DVWA Installation

Install DVWA and set up permissions:

```bash
# Navigate to web root
cd /var/www/html

# Clone DVWA repository
sudo git clone https://github.com/digininja/DVWA.git

# Set permissions
sudo chown -R www-data:www-data DVWA/

# Install required PHP modules
sudo apt install php-gd php-xml php-mbstring -y
sudo systemctl restart apache2
```

### 4. DVWA Configuration

Configure DVWA settings:

```bash
# Navigate to config directory
cd DVWA/config

# Create configuration file
sudo cp config.inc.php.dist config.inc.php

# Edit configuration
sudo nano config.inc.php
```

Update the database credentials in `config.inc.php`:
```php
$_DVWA[ 'db_user' ]     = 'dvwauser';
$_DVWA[ 'db_password' ] = 'dvwapassword';
```

### 5. Set Upload Directory Permissions

```bash
sudo chmod -R 777 /var/www/html/DVWA/hackable/uploads/
```

### 6. Network Configuration

Create a new netplan configuration file:

```bash
sudo nano /etc/netplan/01-network-config-01.yaml
```

Add the following configuration:

```yaml
network:
    version: 2
    renderer: networkd
    ethernets:
        ens33:
            dhcp4: no
            addresses:
                - 172.16.20.210/24
            routes:
                - to: default
                  via: 172.16.20.254
            nameservers:
                addresses: [172.16.20.254]
                search: [grapenet.mylab]
```

Apply network configuration:

```bash
# Test configuration
sudo netplan try

# Apply changes
sudo netplan apply
sudo systemctl restart systemd-networkd
```
> **Note:** From now on, you can put it in the VLAN 20.
We can then see if it's still availble with `172.16.20.210`. from your host machine.

![Access DVWA from Host](/Images/WS1.png)

### 7. Apache Virtual Host Configuration

1. Create and configure virtual host:

```bash
# Create virtual host file
cd /etc/apache2/sites-available
sudo cp 000-default.conf dvwa.grapenet.mylab.conf

# Edit configuration
sudo nano dvwa.grapenet.mylab.conf
```

2. Add virtual host configuration:

```apache
<VirtualHost *:80>
    ServerName dvwa.grapenet.mylab
    ServerAdmin radmin@grapenet
    DocumentRoot /var/www/html/DVWA
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

3. Enable new configuration:

```bash
sudo a2ensite dvwa.grapenet.mylab.conf
sudo a2dissite 000-default.conf
sudo systemctl restart apache2
```

### 8. Local DNS Configuration

Add local DNS entry:

```bash
sudo nano /etc/hosts
```

Add the following line:
```plaintext
127.0.0.1 localhost dvwa.grapenet.mylab
```

## Final Steps

1. Access DVWA setup page:
```
http://dvwa.grapenet.mylab/setup.php
```

2. Click "Create / Reset Database"

3. Log in with default credentials:
- Username: `admin`
- Password: `password`

## Security Notes

- DVWA is intentionally vulnerable - use in isolated environments only
- Consider your network settings carefully if testing in shared environments
- Security levels can be adjusted from "Low" to "Impossible" for different testing scenarios

## Active Directory Integration

Create A records for `dvwa.grapenet.mylab` in your Active Directory DNS to make the application available to other domain machines.

![Adding DVWA to DNS](/Images/WS2.png)

Now it should be available from the Windows 10 machines.

![Access DVWA from Windows](/Images/WS3.png)

## Navigation

[← Back to End Devices (Windows 10)](Corporate%20LAN%20End%20Devices.md) | [Next: Kali Linux (Attacker) →](Kali%20Linux%20(Attacker).md)
