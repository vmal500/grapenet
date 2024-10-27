# Kali Linux (Corporate WAN)

This time we will focus on simulating a fake external network, where our external threats are coming from. That network will contain a Kali Linux machine which at some point in this guidance, we will use to simulate some attacks and detect them with our SIEM. So let's get started.

## Initial Setup 

First, download the Kali Linux VM that suits your need. In my case, it is a VMware machine.
Once downloaded you can simply import the VM into your VMware or VirtualBox and leave the configuration settings default. It should be for a start in either NAT or Bridge mode when it comes to the network. Once done, power up the machine and log in. We will first perform some basic updates and upgrades with the commands:

```bash
sudo apt update
sudo apt upgrade
```

## Network Configuration

Edit the network interfaces file:
```bash
sudo nano /etc/network/interfaces
```

Add the following configuration:
```
auto eth0
iface eth0 inet static
 address 172.16.10.100
 netmask 255.255.255.0
 gateway 172.16.10.254
 dns-nameservers 8.8.8.8
```

Restart networking:
```bash
sudo systemctl restart networking
```

## Firewall Configuration

In theory, with the current network setup, our Bandit is coming from an "internal" network or sort of. In reality, there would be many different IP addresses, firewalls, routers, and other devices in between. But as we are just trying to create a "simple" lab, we are simulating and imagining that malicious actor is coming from the external network.

So now we have our Kali Linux machine ready for some hacking. Now let's make some additional changes to a firewall, to allow access to internal resources from the outside, like HTTP, SSH, and MySQL.

Navigate to *Firewall > NAT > Port Forward* menu. Then you need to create the rules like in the picture below. So we have any traffic coming on our WAN VLAN 10 interface on ports 80, 3306, and 22, redirect/NAT toward our DVWA server IP, 172.16.10.100 on ports 3306, 80, and 22, respectively.

![Kali Linux Port Forward](/Images/KL1.png)

## Testing Connectivity

Now for the final part let's test connectivity from Kali Linux toward the internal network.
Try to open the website from the browser, by opening `http://172.16.10.254`. You should be able to see DVWA home page.

![Kali Linux Test Connectivity](/Images/KL2.png)
Now we have a prepared malicious machine for attacking our Racoon network and testing internal defense systems.

## Navigation

[← Back to Web Server (DVWA)](Web%20Server%20(DVWA).md) | [Next: Security Onion (SIEM Part 1) →](Security%20Onion%20(SIEM%20Part%201).md)
