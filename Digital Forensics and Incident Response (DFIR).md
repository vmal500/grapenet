# Digital Forensics and Incident Response (DFIR)

## Security Team Infrastructure Setup
It's time to establish a security team infrastructure consisting of several specialized machines:
- DFIR
- MA Windows
- MA Linux
- SIEM

## SIFT WORKSTATION
SIFT Workstation (SANS Investigative Forensic Toolkit) is a free, open-source digital forensics and incident response (DFIR) platform developed by the SANS Institute. It's specifically designed for forensic investigators, incident responders, and security professionals to analyze and investigate digital evidence from various sources, including disk images, memory dumps, and network captures.

### Initial Setup
- Download the OVA file for easy VMWare import
- The VM requires access to Corporate LAN network for file and log retrieval
- Important: Corporate LAN network should not have access to the Security Network
- Network adapter should be configured for VLAN 30

### Default Credentials
- Login: `sansforensics`
- Password: `forensics`

## Basic Configuration Steps

### 1. Password Change
First priority is changing the default password using the `passwd` command. After changing, log out and log back in with the new credentials.

### 2. Network Configuration
#### Hostname Setup
Two files need modification for proper hostname configuration:

**/etc/hostname**
```
dfir
```

**/etc/hosts**
```
127.0.0.1        localhost dfir
127.0.1.1        dfir
172.16.30.30     dfir.grapenet.mylab    
# IPv6 configurations
::1              ip6-localhost ip6-loopback
fe00::0          ip6-localnet
ff00::0          ip6-mcastprefix
ff02::1          ip6-allnodes
ff02::2          ip6-allrouters
```

#### Static IP Configuration
Configure static IP using netplan. Create or modify the YAML file in `/etc/netplan/`:

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    ens33:
      dhcp4: no
      addresses: 
        - 172.16.30.30/24
      routes:
        - to: default
          via: 172.16.30.254
      nameservers:
        addresses: [172.16.30.254]
        search: [grapenet.mylab]
```

#### Applying Network Changes
Always validate configuration before applying:
```bash
sudo netplan try    # Tests configuration with automatic rollback
sudo netplan apply  # Applies the configuration
sudo systemctl restart systemd-networkd
```

Important: Reboot the VM to apply hostname changes completely.

### 3. DNS Configuration
> **Note:** A Host Override lets you create a custom DNS entry that maps a specific hostname to a particular IP address within your network. This override makes pfSense's internal DNS resolver return the specified IP address when clients query for that hostname.

Create a host override record on the firewall:
1. Navigate to Services > DNS > Resolver > General > Settings
2. Add a Host Override record
3. This allows custom DNS entry mapping for your hostname to IP address
4. The firewall's internal DNS resolver will return the specified IP for hostname queries

![DNS Configuration Screenshot](images/DFIR1.png)

You can verify DNS configuration using the firewall's Diagnostics > Ping tool.

![Ping Tool Screenshot](images/DFIR2.png)

> **Note:** As the final step, we can create a snapshot of our VM so you can always have a base installed, ready, and prepared in case you break something on your machine. And that’s it, our DFIR machine is ready to be used!

## Security Resources

Here is a bunch of resources that [FACyber](https://facyber.me/) gave us to bookmark in the browser so we can further research on those tools later on.

### Malware Analysis
1. [VirusTotal](https://www.virustotal.com) - Analyzes files and URLs for viruses, worms, trojans, and other kinds of malicious content.
2. [Hybrid-Analysis](https://www.hybrid-analysis.com) - Provides in-depth analysis of malware samples and suspicious files.
3. [Talos Intelligence](https://talosintelligence.com) - Offers threat intelligence and analysis on malware, vulnerabilities, and other security threats.
4. [urlscan.io](https://urlscan.io) - A web service to scan and analyze websites for malicious content and threats.
5. [OTX](https://otx.alienvault.com) - A platform for sharing threat intelligence and discovering emerging threats.

### Network Tools
1. [Whois](https://whois.domaintools.com/) - Provides domain registration information and ownership details.
2. [MXToolbox](https://mxtoolbox.com) - Offers various network tools including DNS lookup, blacklist check, and email diagnostics.
3. [OUI Lookup](https://www.wireshark.org/tools/oui-lookup.html) - Identifies the manufacturer of a network device by its MAC address.
4. [IP Location](https://iplocation.net) - Determines the geographical location of an IP address.
5. [Geolocate](https://www.geolocation.com/) - Provides geolocation data for IP addresses and domain names.

### Web Tools
1. [URL2PNG](https://url2png.com) - Captures screenshots of web pages.
2. [Wannabrowser](https://www.wannabrowser.net/) - A tool to test how websites appear in different browsers.
3. [Wayback Machine](https://archive.org/web) - Archives and provides access to historical snapshots of web pages.

### Miscellaneous
1. [A-Packets](https://apackets.com/) - A tool for analyzing and visualizing network packet captures.
2. [de4js](https://lelinhtinh.github.io/de4js/) - A JavaScript deobfuscator and beautifier.
3. [Yandex](https://yandex.com) - A search engine that also offers various internet services.
4. [Fake Mail Generator](https://www.fakemailgenerator.com/) - Provides temporary email addresses for testing and privacy purposes.




## Navigation

[← Back to pfSense Deployment](pfSense%20Deployment.md) | [Next: Malware Analysis (Linux Box) →](Malware%20Analysis.md)
