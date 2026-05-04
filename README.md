# cybersecurity-homelab-network-setup
# Enterprise Cybersecurity Homelab Network Setup

## Project Overview
This project documents the design and configuration of an enterprise-style cybersecurity homelab using VirtualBox and MikroTik CHR.

The goal was to build a segmented virtual lab environment for security monitoring, attack simulations, Active Directory testing, SIEM integration, and incident response practice.

## Lab Objectives
- Build a multi-VM cybersecurity lab environment
- Configure MikroTik CHR as the central virtual router
- Create segmented subnets for Blue Team, Red Team, and server infrastructure
- Configure VirtualBox NAT, bridged, and internal networking
- Validate routing and inter-subnet communication
- Prepare the environment for Splunk SIEM monitoring and future attack detection

## Tools and Technologies
- VirtualBox
- MikroTik CHR / RouterOS
- Windows Server 2022
- Active Directory
- Windows 11
- Kali Linux
- Ubuntu Server
- Splunk
- Sysmon
- TCP/IP Networking
- NAT, Routing, and Firewall Rules

## Network Topology
![Network Topology](network-topology/topology-diagram.png)

## IP Addressing Plan

| Network | Purpose | Subnet | Gateway |
|---|---|---|---|
| Blue Team | Windows endpoint / AD systems | 10.10.10.0/24 | 10.10.10.1 |
| Server Network | Ubuntu, Splunk, DNS/Web | 10.20.20.0/24 | 10.20.20.1 |
| Red Team | Kali Linux attacker VM | 10.30.30.0/24 | 10.30.30.1 |
| NAT/WAN | Internet access | 10.0.2.0/24 | VirtualBox NAT |

## Project Stages

### Stage 1: Physical and Virtual Lab Setup
- Set up two physical host machines for virtualization
- Connected hosts using a dedicated physical switch
- Installed VirtualBox on lab systems
- Created virtual machines for Windows, Linux, Active Directory, Kali, and Splunk

### Stage 2: VirtualBox Network Configuration
- Configured NAT adapter for internet access
- Configured bridged adapter for cross-host communication
- Created internal networks for isolated lab segmentation
- Assigned VMs to the correct network segments

### Stage 3: MikroTik CHR Router Deployment
- Deployed MikroTik CHR as the central router
- Assigned interfaces to different network segments
- Configured subnet gateways:
  - 10.10.10.1
  - 10.20.20.1
  - 10.30.30.1
- Configured NAT masquerading for outbound internet access
- Added firewall rules to manage traffic flow

### Stage 4: Active Directory and Server Configuration
- Installed Windows Server 2022
- Configured Active Directory Domain Services
- Created domain environment for endpoint authentication
- Configured DNS services
- Assigned static IP addressing to server systems

### Stage 5: Endpoint and Linux Configuration
- Configured Windows endpoint with static IP addressing
- Configured Kali Linux on the Red Team subnet
- Configured Ubuntu server on the server subnet
- Verified gateway and subnet assignments

### Stage 6: Connectivity Validation
- Tested connectivity to MikroTik gateways
- Verified inter-subnet routing using ping
- Confirmed communication between Windows, Linux, and server systems
- Troubleshot packet loss, routing, and adapter configuration issues

### Stage 7: SIEM Preparation
- Prepared the lab for Splunk SIEM integration
- Installed/configured endpoint logging components
- Planned future detection engineering and attack simulation labs

## Skills Demonstrated
- Network segmentation
- Virtualization
- MikroTik RouterOS configuration
- Routing and NAT
- Firewall rule management
- Active Directory deployment
- Linux and Windows administration
- SIEM lab design
- Troubleshooting and validation
- Cybersecurity documentation

## Future Improvements
- Add Snort IDS/IPS
- Build Splunk dashboards
- Simulate Active Directory attacks
- Document detection rules
- Create incident response reports
- Add vulnerability scanning and remediation workflow
