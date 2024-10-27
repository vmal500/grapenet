# Domain Controller (Corporate LAN)

We will start building a corporate network for our home lab, which will contain a few workstations presenting endpoints, and a few servers like AD, mail server, database, etc.

## Windows Server 2019 as a Domain Controller

We will use Microsoft’s Windows Server 2019 Evaluation server for the lab. Microsoft gives 180 days of Windows Server 2019 and 90 days for Windows 10, totally free. Once installed, you create a snapshot and you can always revert it once it expires.

1. Install it with the default values (except the user password of course).
2. Start the machine and create a vanilla snapshot.

## Configuration

### Network and Hostname

First, we need to set the VM network adapter in VLAN 20 and set a static IP address.

![Network Configuration](/Images/DC1.png)

The second thing that we are going to do is to change the server name. This can be done in System Properties and in my case, I named this server GDC01 (GrapeNet Domain Controller 01).

![Change Server Name](/Images/DC2.png)

After saving the config, you will need to restart the VM so changes can be applied.

### Roles and Features

Go to Server Manager > Manage > Add Roles and Features and accept all default settings until Server Roles. In Server Roles, select the following roles and click the Next button:

1. Active Directory Domain Service
2. DHCP Server
3. DNS Server
4. File and Storage Services
5. Remote Access

After selecting these roles, just go with the default settings till the end and the installation will begin. After installation is completed and you close the installation window, you will notice in Server Manager a few notifications that warn you to complete the configuration of installed services.

![Server Manager Notifications](/Images/DC3.png)

## Active Directory Domain

We will need to complete the role of AD by clicking on Promote your server to a domain controller. Add a new forest and enter your domain.

![Add New Forest](/Images/DC4.png)

Next, enter the password of the user Administrator and then continue with the default settings till the end. During the configuration, the VM will restart by itself and it will take a few minutes to configure. If everything went well, you will get a prompt to log in with the user Administrator on your domain.

![Login Prompt](/Images/DC5.png)

## DHCP

Let’s now configure the DHCP server. Open Server Manager and go to Tools > DHCP. Right-click on IPv4 and click New Scope.

![New Scope](/Images/DC6.png)

Enter the name of your network and description.

![Network Name and Description](/Images/DC7.png)

Next, we are going to configure a specific range of available IP addresses, because in a real environment you always have some IP address range reserved for the servers and other devices that require a static IP address, of course.

![IP Address Range](/Images/DC8.png)

Exclusion, Inclusion, and Lease can be left as is.

Then we configure our domain controller as a default gateway, DNS server, and a WINS server.

![Default Gateway](/Images/DC9.png)

![DNS Server](/Images/DC10.png)

![WINS Server](/Images/DC11.png)

Then just go next with the default things and once completed, you should see your pool in Address Pool.

![Address Pool](/Images/DC12.png)

## DNS

First, we are going to set up a reverse zone. From Server Manager, open Tools > DNS. Then right-click on the Reverse Lookup Zones and click on Create new zone. Just click on the Next button until you come to the part to enter the network ID. Enter 172.16.20 and go further until the end. And then you will see your reverse zone created.

![Reverse Zone](/Images/DC13.png)

Then we are going to add a DNS entry for our firewall by right-clicking on the Forward Lookup Zones > racoon.bandito and then clicking on New Host (A or AAAA)….

![New Host](/Images/DC14.png)

Enter the name and the IP address. Also, leave Create associated pointer (PTR record) marked and click on the Add Host button.

Creating an associated pointer (PTR) record in DNS is a way to map an IP address back to a hostname, effectively allowing reverse DNS lookups.

![PTR Record](/Images/DC15.png)

Now if we do a simple nslookup command we can see our firewall is getting resolved correctly.

![nslookup](/Images/DC16.png)

## Static Routing

We need to make our DC a routing point for the endpoint, otherwise, it won’t be able to access SIEM on specific ports to ship the logs, once we come to that part. From Server Manager, open Tools > Routing and Remote Access and right-click on your DC (local) and then select Configure and Enable Routing and Remote Access. Follow instructions by selecting Custom and then LAN.

Once completed, go to GDC01 (local) > IPv4 > Static Routes and add a static route toward the future SIEM IP address and save the configuration.

![Static Routes](/Images/DC17.png)

## Users and Groups

Before we start with the users, as we are creating a Blue Team Lab, we want to simulate poor designs and security practices in an IT environment that are present all the time. One of those is the weak practice of strong passwords. Now Microsoft does enforce some practices by default, but there are always cases where people complain it is too complex for them to make up some strong password, so these requirements are often changed. We will disable the default password policy so we can apply for some of our users, weak passwords. To do this, please follow guides from this link as I find it very easy and straightforward.

Now let’s create a few users and groups to have a real working environment. From Server Manager, go to Tools > Active Directory Users and Computers.

Then right-click on the organizational unit Users and then click on New > User. We will use their last name as a login name. For the password, I suggest using a few randomly generated strong passwords, and a few from the famous rockyou.txt list so you can practice maybe a brute-forcing password of the users. Also, set passwords to never expire.

![New User](/Images/DC18.png)

Now create a few more users, so we can have a few administrators, some in the HR department, some in IT, and so on. Now we are going to create an organizational unit (OU) and name it Groups and inside of it, we are going to create 3 groups (or more if you want): IT Operations, Sales, and Winemakers.

![Groups](/Images/DC19.png)

Now go for each created user and assign them to the group(s) so you can have in each group at least one user and also add some to the Administrator group.

![Assign Groups](/Images/DC20.png)

## Local Admin Policy

We will also create a local admin policy so we can have some sort of a local admin account that can manage host devices. From Server Manager, open Group Policy Management and create a new policy named Local Admin GPO.

![Local Admin GPO](/Images/DC21.png)

Then edit it and go to Computer Configuration > Policies > Windows Settings > Security Settings > Restricted Groups and create a new group inside of it and name it Local Administrators - Workstations (or something different if you like).

![Restricted Groups](/Images/DC22.png)

Then you need to add either a few users that you would like to be local admins, or a group that contains users and save the changes.

## Shared Folder

And for the end, we are going to create a shared folder that will support SMBv1 which is, as you may or may not know, an obsolete version of the SMB protocol that is quite insecure and should be avoided. Of course, we are going to enable it so we could practice SMB detection and mitigation of its vulnerabilities. 

![SMBv1](/Images/DC23.png)

On the C: drive, create the following folder structure:

```
C:
└── GrapeNetShared
    ├── All
    ├── IT Operations
    ├── Sales
    └── Winemakers
```
Now for each of those folders (including GrapeNetShared), we are going to share them with the previously created groups and set up the correct permissions.

## Navigation

[← Back to Malware Analysis (Linux Box)](Malware%20Analysis.md) | [Next: End Devices (Windows 10) →](Corporate%20LAN%20End%20Devices.md)
