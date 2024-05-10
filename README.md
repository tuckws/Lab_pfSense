## Lab_pfSense
#### Purpose
The purpose of this lab is to develop knowledge and experience in the application of virtualization software, firewall/router software, and networking services and applications across a self-built virtualized network homelab. Tools used for this included VMware and pfSense.
#### Lessons Learned
- pfSense implementation and configuration in virtual environment.
- Firewall rule configurations for different network types. 
- VMware network adapter configuration for multiple types of networks.
- DHCP configuration for subnets.
- Bridging configuration for network to network.
- Leveraging draw.io.
#### Tools Used
- pfSense.
- VMware.
- Active Directory.
- Draw.io.
#### Lab Environment Setup
| Name            | IP              | Subnet Mask   | Description             |
| :-------------- | :-------------- | :------------ | :---------------------- |
| VMNet0 - WAN    | 192.1681.1.0/24 | 255.255.255.0 |                         |
| VMNet11 - LAN01 | 10.0.0.0/24     | 255.255.255.0 | Windows AD Environment. |
| VMNet - LAN02   | 192.168.10.0/24 | 255.255.255.0 | Home Use. Basic Config. |
| VMNet - DMZ     | 172.16.0.0/16   | 255.255.0.0   | Enterprise Environment. |
##### pfSense 2.7.2 Setup
- Guest OS: FreeBSD 14 x64
- CPU: 1P / 2C
- Memory: 2GB
- Storage: 32GB
- Network: Bridge/LAN01/LAN02/DMZ
##### OS Setup
- Windows Server.
- Linux Server.
- Windows 11. 
- Ubuntu.
##### Basic Networking Setup
1. Enable DHCP on LAN02 / DMZ. Note that WAN / LAN01 are already configured with pfSense defaults. 
2. Test LAN01 / LAN02 /DMZ Connections using Diagnostics Ping in pfSense.
	1. Ping google.com
	2. Ping other gateway IPs.
3. Open ports for LAN02 / DMZ.
4. Block networking for LAN01 / LAN02 / DMZ.
5. Test environment:
	1. Setup and test DHCP in Server OS. 
	2. Setup and test Client OS for WAN to DMZ. 
##### pfSense Firewall Configuration
###### Enable DHCP for LAN02 / DMZ
1. pfSense Services Tab.
	1. LAN02
	2. Check Enable DHCP server on LAN02 interface.
	3. Select range for DHCP: 192.168.10.5 - 192.168.10.250.
	4. Save and Restart Service.
2. Repeat for DMZ.
	1. DMZ.
	2. Check Enable DHCP server on DMZ interface.
	3. Select range for DHCP: 172.16.0.5 - 172.16.255.254.
###### Test Connections with Ubuntu Client
1. On Ubuntu Client, change network adapter to LAN02.
2. Ping:
	1. 192.168.10.254.
	2. 8.8.8.8
	3. google.com
	4. 10.0.0.254.
3. Change network adapter to DMZ.
4. Ping:
	1. 172.16.0.1
	2. 8.8.8.8
	3. google.com
	4. 10.0.0.254
###### Firewall Rules for LAN02
1. Allow Internet Access for LAN02. Add Rule.
	1. Action: Pass.
	2. Interface: LAN02.
	3. Protocol: Any.
	4. Source: LAN02 net.
	5. Destination: Any.
	6. Ports: Any.
2. Block Connection to LAN02. Add Rule. 
	1. Action: Block.
	2. Interface: LAN02.
	3. Protocol: Any.
	4. Source: Any. 
	5. Destination: LAN01.
	6. Ports: Any.
3. Block Connection to DMZ. Add Rule.
	1. Action: Block.
	2. Interface: LAN02.
	3. Protocol: Any.
	4. Source: Any.
	5. Destination: DMZ.
	6. Ports: Any.
- **Note**: 
	- The order in which you list rules is critical. "Top-Down" lists start from the list and apply sequentially. Block rules should be added first in order to prevent unwanted traffic. 
	- Block vs Reject is significant. Reject will provide a reason which can give additional intelligence to an attacker. Unnecessary unless I am configuring for a specific reason. 
###### Firewall Rules for DMZ
1. Allow Ping for DMZ. Add Rule.
	1. Action: Pass.
	2. Interface: DMZ.
	3. Protocol: ICMP.
	4. Source: DMZ.
	5. Destination: Any.
	6. Ports: Any.
2. Allow NTP for DMZ. Add Rule.
	1. Action: Pass.
	2. Interface: DMZ.
	3. Protocol: UDP.
	4. Source: DMZ.
	5. Destination: Any.
	6. Ports: 123.
3. Allow HTTPS for DMZ. Add Rule. 
	1. Action: Pass.
	2. Interface: DMZ.
	3. Protocol: TCP.
	4. Source: DMZ.
	5. Destination: Any.
	6. Ports: 443.
4. Allow HTTP for DMZ. Add Rule.
	1. Action: Pass.
	2. Interface: DMZ.
	3. Protocol: TCP.
	4. Source: DMZ.
	5. Destination: Any.
	6. Ports: 80.
5. Allow DNS for DMZ. Add Rule.
	1. Action: Pass.
	2. Interface: DMZ.
	3. Protocol: UDP.
	4. Source: DMZ.
	5. Destination: DMZ.
	6. Ports: 53.
6. Allow SSH for DMZ. Add Rule.
	1. Action: Pass.
	2. Interface: DMZ.
	3. Protocol: TCP.
	4. Source: DMZ.
	5. Destination: DMZ.
	6. Ports: 22.
7. Block Webconfig for DMZ. Add Rule. 
	1. Action: Block.
	2. Interface: DMZ.
	3. Protocol: HTTPS.
	4. Source: DMZ.
	5. Destination: This Firewall.
	6. Ports: 443.
8. Block LAN02 for DMZ. Add Rule.
	1. Action: Block.
	2. Interface: DMZ.
	3. Protocol: Any.
	4. Source: DMZ.
	5. Destination: LAN02.
	6. Ports: Any.
9. Block LAN01 for DMZ. Add Rule.
	1. Action: Block.
	2. Interface: DMZ.
	3. Protocol: Any.
	4. Source: DMZ.
	5. Destination: LAN01.
	6. Ports: Any.
