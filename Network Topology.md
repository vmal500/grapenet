# Network Topology

In this section, we will outline the network topology for the GRAPENET Project. This includes detailed descriptions of each VLAN and the steps to create them in VMware.

### VLAN Descriptions

- **VLAN 1 (Management)**: 
  - Purpose: Access the firewall web GUI.
  - Subnet: `172.16.1.0/24`
  - Gateway: `172.16.1.1`

- **VLAN 10 (Corporate WAN)**: 
  - Purpose: Simulate a malicious device coming from outside (Fake WAN). Includes Kali Linux.
  - Subnet: `172.16.10.0/24`
  - Gateway: `172.16.10.254`
  - Note: This VLAN will only have access to VLAN 20 and will not reach the real Internet or personal machines to avoid accidental spread of malicious scripts/software.

- **VLAN 20 (Corporate LAN)**: 
  - Purpose: Host servers and end devices (e.g., Active Directory, Windows 10, Web Server).
  - Subnet: `172.16.20.0/24`
  - Gateway: `172.16.20.254`
  - Note: This VLAN will only have access to the fake internet network.

- **VLAN 30 (Security)**: 
  - Purpose: Contain VMs for DFIR, OSINT, and other security tasks. Simulates a security team network.
  - Subnet: `172.16.30.0/24`
  - Gateway: `172.16.30.254`
  - Note: This VLAN will have network permission to reach the corporate network (VLAN 20) to retrieve logs, files, emails, etc., but not vice versa. It will also have access to the real internet for using public tools and performing installations/upgrades.

- **VLAN 40 (ISOLATED LAN)**: 
  - Purpose: Contain VMs for malware analysis.
  - Subnet: `172.16.40.0/24`
  - Gateway: `172.16.40.254`
  - Note: This VLAN is isolated to perform static and dynamic malware analysis without risking infection of other machines.

### VLAN Creation Steps

1. **Open VMware Virtual Network Editor**:
   - Navigate to `Edit > Virtual Network Editor` (admin credentials may be required).

2. **Add Networks**:
   - Click the `Add Network…` button and create four networks named `vnet10`, `vnet20`, `vnet30`, and `vnet40`.
   - Set all network types to host-only.

3. **Configure Networks**:
   - For each network, uncheck `Use local DHCP service to distribute IP addresses to VMs` as the firewall will act as the DHCP server.
   - Assign the subnet IPs as specified in the table below.


![VirtualNetworkEditorvmWare](/Images/VirtualNetworkEditorvmWare.png)

## Navigation

[← Back to README](README.md) | [Next: pfSense Deployment →](pfSense%20Deployment.md)

