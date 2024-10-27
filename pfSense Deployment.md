# pfSense Deployment

For our firewall, we will use pfSense, an open-source firewall that is a great tool not just for your labs, but also can be quite good for production/company environments. Below is a VM specification that we will use for our firewall:

1. **CPU**: 1
2. **RAM**: 512MB
3. **HDD**: 30GB
4. **NICs**: 6x
    - Bridged (make sure that the NIC bridged is the one that is currently connecting the host to your network)
    - vmnet1
    - vnet10
    - vnet20
    - vnet30
    - vnet40

We have 5 networks that we’ve created for our lab environment: management, corporate LAN, corporate WAN, security, and isolation LAN. For each network, we’ve assigned a specific network interface to the firewall. The sixth one (or the first one on the list) is a bridged interface, an interface that sits in the LAN network of our physical laptop. This interface will be used to provide outbound traffic toward the real internet, for devices in the security network.

## Installation

Installation is pretty straightforward. Just go with all default options and continue until the end of the installation wizard. Once installed, you will need to reboot it. Once rebooted, you will see it booted with two configured interfaces, WAN and LAN.

![pfSense installation showing WAN and LAN interfaces](/Images/pfSense1.png)

Once you configure WAN and LAN interfaces, you should be able to access the pfSense default credentials on the LAN interfaces (e.g., `https://172.16.1.128/`) (username: admin, password: pfsense). Make sure to change it afterward as we know we must never leave default credentials, especially on our firewall appliance :)

## Configuration

### Interfaces

First, you will need to assign all interfaces to the pfSense. This can be done by going to **Interfaces > Interface Assignments** and then clicking the **Add** button for each interface in the list and then clicking the **Save** button. This will add all our interfaces and add generic names to each of them (OPT1, OPT2…).

The next step is to enable and configure each interface with the correct description and IP address. Open each interface according to the instructions below:

1. **Enable**: Checked
2. **Description**: $NETWORK_NAME_VLAN<VLAN_NUMBER>
3. **IPv4 Configuration Type**: Static IPv4
4. **IPv6 Configuration Type**: None
5. **MAC Address**: leave empty
6. **MTU**: leave empty
7. **MSS**: leave empty
8. **Speed and Duplex**: Default
9. **IPv4 Address**: The IP address of the interface from the previous table. Mask set to /24
10. **IPv4 Upstream gateway**: None (only WAN interface should have this field configured)
11. **Block private networks**: unchecked (this field should be checked for WAN interface only)
12. **Block bogon networks**: unchecked (this field should be checked for WAN interface only)

Your configuration should look something like the picture below:

![pfSense interface configuration](/Images/pfSense2.png)

And after configuring all interfaces you should see something like:

![pfSense interface assignments](/Images/pfSense3.png)

### Basic Configuration

Let’s configure some basic settings like DNS servers, domain name, timezone, etc. Go to **System > General Setup** and configure the following fields:

1. **Hostname**: Set whatever you would like to call your firewall. In my case, it is **firewall**
2. **Domain**: grapenet.mylab (this is my lab, you can choose whatever domain name you want)
3. **DNS Servers**: `8.8.8.8` (you can use the one you want, it doesn't really matter)
4. **Timezone**: America/Toronto

Click on the **Save** button at the bottom of the screen.

Then go to **System > Advanced > Admin Access**:

1. **Protocol**: HTTPS (SSL/TLS)
2. **Secure Shell Server**: Enable

This config will be enough for the lab.

## Firewall Rules

We will now configure firewall rules to allow/restrict specific traffic between the networks. For the beginning, we will allow all traffic, but at some point, we might restrict the access and allow only specific traffic/ports/services to pass.

### WAN

Navigate to **Firewall > Rules > WAN**. This interface should have default rules that come with enabling fields **Block private networks** and **Block bogon networks** during initial configuration.

- **Block Private Networks**:
    - This option blocks IP addresses from private IP ranges (e.g., `10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`) on interfaces intended to face the public internet (such as the WAN).
- **Block Bogon Networks**:
    - Bogon networks refer to IP addresses that are invalid for public internet use. This includes IP ranges that haven’t been allocated by the Internet Assigned Numbers Authority (IANA) or those explicitly reserved for specific uses.
    - Enabling "Block bogon networks" prevents traffic from these unassigned or reserved IP ranges, which are often used in malicious activities like IP spoofing. This adds an extra layer of protection, as legitimate traffic should never originate from bogon IP addresses.

These default settings are especially useful on your WAN interface to avoid potential security issues from unusual or unauthorized traffic sources.

![pfSense WAN firewall rules](/Images/pfSense4.png)

### MANAGEMENT

As this is our management interface, by default it has some rules configured for passing traffic and an anti-lockout rule. We will disable the IPv6 rule (IPv6 can introduce additional attack surfaces if not properly configured and increase complexity) and the rest should be like in the picture below:

![pfSense management interface firewall rules](/Images/pfSense5.png)

### CORPORATE WAN

Here we will allow all traffic toward the Corporate LAN network and Corporate WAN network. Everything else will be blocked by default as there is a default rule “deny all” that denies all traffic.

![pfSense corporate WAN firewall rules](/Images/pfSense7.png)

### CORPORATE LAN

This interface should have the same rules as the CORPORATE_WAN interface. This is because we want to simulate its access to the “internet”.

![pfSense corporate LAN firewall rules](/Images/pfSense6.png)

### SECURITY

The security network should not have access to WAN, Management, and Corporate WAN networks, but should have access to everything else.

![pfSense security network firewall rules](/Images/pfSense8.png)

### ISOLATION LAN

At last, the isolation network should not have access to anything except the local network. By default, there is a rule to deny all that denies all traffic that comes to the interface, but just to be sure we will create an explicit rule for that. This will also ensure that we see the rule when looking at the firewall.

![pfSense isolation LAN firewall rules](/Images/pfSense9.png)

### Outbound

In order to allow other networks’ access to the internet through the fake WAN, we need to set up the rules as shown in the picture below, in **Firewall > NAT > Outbound**:
> **Note:** This configuration was specified in the guide, but I am not entirely sure about its necessity.


![pfSense outbound NAT rules](/Images/pfSense10.png)


## Navigation

[← Back to Network Topology](Network%20Topology.md) | [Next: Digital Forensics and Incident Response (DFIR) →](Digital%20Forensics%20and%20Incident%20Response%20(DFIR).md)
