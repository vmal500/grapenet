# GRAPENET Project (Blue Team Home Lab)

For quite some time, I wanted to create a homelab to prove that I can build it—or at least follow a guide, tweak it to my liking, and demonstrate solid troubleshooting skills without just following a copy-paste recipe from GitHub.

I stumbled upon [FACyber's Building a Blue Team Home Lab series](https://facyber.me/), which I found really interesting. I decided to challenge myself to follow the guide and document the process on my GitHub. This way, I can look back, understand what I've done, rebuild it from scratch, and maybe inspire others.

With a rainy weekend on the horizon, I set myself the challenge to build this lab and expand my knowledge on various topics like network topology, firewalls, DFIR, malware analysis, building a corporate network, vulnerability scanning, Security Onion (SIEM), Wazuh Agents (HIDS) and more.

## Lab Setup Guide
1. [Part 1: Network Topology (Starting Point)](Network%20Topology.md)
2. [Part 2: pfSense Deployment (Firewall)](pfSense%20Deployment.md)
3. [Part 3: Digital Forensics and Incident Response (DFIR)](Digital%20Forensics%20and%20Incident%20Response%20(DFIR).md)
4. [Part 4: Malware Analysis (Linux Box)](Malware%20Analysis.md)
5. [Part 5: Domain Controller (Windows Server)](Corporate%20LAN%20Domain%20Controller.md)
6. [Part 6: End Devices (Windows 10)](Corporate%20LAN%20End%20Devices.md)
7. [Part 7: Web Server (DVWA)](Web%20Server%20(DVWA).md)
8. [Part 8: Kali Linux (Attacker)](Kali%20Linux%20(Attacker).md)
9. [Part 9: Security Onion (SIEM Part 1)](Security%20Onion%20(SIEM%20Part%201).md)
10. [Part 10: Wazuh HIDS (SIEM Part 2)](Wazuh%20HIDS%20(SIEM%20Part%202).md)

![Network Topology Diagram](/Images/NetworkTopology.png)

## Hardware Requirements

For hardware requirements, not all virtual machines (VMs) will run at the same time. You'll work on specific things over time (though if you want everything running simultaneously, you might want to invest in a physical server). 

Currently, I'm using an HP EliteDesk G1 Desktop that a bought for 100$ with the following specs:
* CPU: i7-4770 (4 cores, 8 threads)
* RAM: 32GB
* Storage: 2 x 3TB HDDs with a 256GB SSD for the hypervisor OS
* OS: Windows 10 Pro with VMware Workstation Pro. 

> **Note:** While I usually prefer Proxmox for virtualization (since my "server" is underpowered), I'm using VMware for academics because most teachers provide prebuilt VMs for VMware or VirtualBox. Nested virtualization in Proxmox can also be tricky, and I don't want to waste time troubleshooting it with assignments due.

## Tools

Here's a list of the main tools I'll be using:
* VMware Workstation – For managing VMs. I have a Pro license and am happy with it. Alternatives like VirtualBox can also work.
* Draw.io – An open-source tool for creating network topologies and diagrams, complete with many icon libraries (Cisco, AWS, Microsoft, Google, etc.).
* OneNote – A note-taking app for tracking what's needed, completed tasks, VM information, services, etc.
* Cursor – For quickly and efficiently making Markdown files, like the one you're reading.
* Virtual Machines – This lab will include various VMs, such as pfSense, SIFT, REMnux, and others.
