
# Enterprise Cybersecurity Homelab Network Setup

## Network Topology

<img width="915" height="600" alt="My Cybersecurity Home Lab Network Topology" src="https://github.com/user-attachments/assets/3a0c5632-07eb-414f-82b1-d780bc541d96" />

## Project Overview
This project documents the design and configuration of an enterprise-style cybersecurity homelab using VirtualBox and MikroTik CHR.

The goal was to build a segmented virtual lab environment for security monitoring, attack simulations, Active Directory testing, SIEM integration, and incident response practice.

## Lab Architecture 

**Host A (Laptop)**
- MikroTik CHR Router
- Windows 10 Endpoint
- Kali Linux (Red Team)
- Ubuntu Server (Web/DNS)

**Host B (Desktop)**
- Windows Server 2022 (Active Directory)
- Ubuntu Server (Splunk SIEM)
- Windows 11 Endpoint
  
**Networking Design**
- Dedicated physical switch connecting both hosts
- NAT adapter for internet access
- Bridged adapter for cross-host communication
- Internal VirtualBox networks for segmentation


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

  <img width="3024" height="4032" alt="IMG_2523" src="https://github.com/user-attachments/assets/3203a38d-876f-4c18-835a-84fb7956d015" />

*Figure: Physical lab setup with multi-host virtualization and dedicated network switch*


---

## Stage 2: VirtualBox Network Configuration
- Configured NAT adapter for internet access
- Configured bridged adapter for cross-host communication
- Created internal networks for isolated lab segmentation
- Assigned VMs to the correct network segments
- Configured and organized multiple virtual machines in VirtualBox to support Active Directory, SIEM monitoring, Linux administration, and offensive security testing.

 <img width="1920" height="1116" alt="Screenshot 2026-05-03 092058" src="https://github.com/user-attachments/assets/e4c0d067-7216-44f3-90a8-8488c12be7a1" />

*Figure: VirualBox environment with multiple VMs configured*

---
- Configured MikroTik CHR WAN interface using VirtualBox NAT adapter to provide internet access and outbound connectivity for the lab environment.
<img width="1367" height="905" alt="Screenshot 2026-05-03 093334" src="https://github.com/user-attachments/assets/cbb22b96-b1a5-4cab-a436-74fbf36acbdf" />


*Figure: Network interface for NAT adapter on MikroTik CHR*

---

- Configured bridged networking on MikroTik CHR to enable communication between physical hosts through a dedicated switch infrastructure.
<img width="1367" height="905" alt="Screenshot 2026-05-03 093353" src="https://github.com/user-attachments/assets/eb4e01b8-3cef-417f-a798-cc5a54205605" />

*Figure: Network interface for Bridge adapter on MikroTik CHR*

---

- Created isolated Blue Team internal network within VirtualBox to support defensive systems, monitoring infrastructure, and secure segmentation.
<img width="1367" height="905" alt="Screenshot 2026-05-03 093401" src="https://github.com/user-attachments/assets/d0a2d5e7-eeae-4ba9-8e89-78ae487f1b5b" />

*Figure: Network interface for internal adapter (Blue Team) on MikroTik CHR*

---

- Configured isolated Red Team subnet for attack simulation, penetration testing, and controlled offensive security exercises.
<img width="1367" height="905" alt="Screenshot 2026-05-03 093417" src="https://github.com/user-attachments/assets/34e6bc0d-8c60-4f4e-84e7-30ed36b3b7d2" />

*Figure: Network interface for internal adapter (Red Team) on Mikrotik CHR*

---

## Stage 3: MikroTik CHR Router Deployment
- Deployed MikroTik CHR as the central router
- Assigned interfaces to different network segments:
- Configured subnet gateways:
  - /ip address add address= 10.10.10.1/24
  - /ip address add address= 10.20.20.1/24
  - /ip address add address= 10.30.30.1/24
- Verified MikroTik interface assignments and subnet mappings for internal routing and network segmentation:
  - /interface print
  - /ip address print
   
   <img width="720" height="400" alt="VirtualBox_Mikrotik Router _03_05_2026_09_26_12" src="https://github.com/user-attachments/assets/82cf814e-eeea-4711-b538-83dc33034a2d" />

*Figure: MikroTik interface configuration*

---

- NAT masquerading for internet access
- Firewall rules for traffic control
- Configured NAT masquerading for outbound internet access:
   - /ip firewall nat add chain=srcnat out-interface=ether9 action=masquerade
- Configured ip firewall rules for established and drop invalid connections:
  - /ip firewall filter add chain=forward connection-state=established,related action=accept
  - /ip firewall filter add chain=forward connection- state=invalid action=drop
 
        
<img width="720" height="400" alt="VirtualBox_Mikrotik Router _03_05_2026_09_29_50" src="https://github.com/user-attachments/assets/0fe5b3e5-b33b-45ab-8442-0ce1e7d1c69a" />

*Figure: MikroTik NAT configuration and firewall rules for traffic control*

---

## Stage 4: Active Directory and Server Configuration
- Installed Windows Server 2022
- Configured Active Directory Domain Services
- Created domain environment for endpoint authentication
- Configured DNS services
- Assigned static IP addressing to server systems

<img width="1462" height="822" alt="VirtualBox_ADDserver _03_05_2026_10_51_02" src="https://github.com/user-attachments/assets/7467009d-1642-4b7b-abe0-ce64b8ec2bfd" />

*Figure: Active Directory enviornment configuration*

---

- Validated static IP addressing, subnet configuration, and default gateway settings for the Active Directory server environment.
<img width="1462" height="822" alt="VirtualBox_ADDserver _03_05_2026_10_52_00" src="https://github.com/user-attachments/assets/b121a548-560d-455d-b363-8ed79d8ccb48" />

*Figure: Active Directory network configuration validation*

---

- Performed network connectivity testing between segmented subnets to validate routing and communication through the MikroTik virtual router.
<img width="1462" height="822" alt="VirtualBox_ADDserver _03_05_2026_10_53_33" src="https://github.com/user-attachments/assets/ad0deff9-18b6-49be-8942-3bbe5a06e264" />

*Figure: Successful inter-subnet connectivity test*

---

## Stage 5: Endpoint and Linux Configuration
- Configured Windows endpoint with static IP addressing
- Configured Kali Linux on the Red Team subnet
- Configured Ubuntu server on the server subnet
- Verified gateway and subnet assignments


 - Configured Windows endpoint with static IP addressing and gateway settings for integration into the Active Directory lab environment.
 <img width="1280" height="720" alt="VirtualBox_windows 11 pro_03_05_2026_10_58_31" src="https://github.com/user-attachments/assets/7f7ffb68-d38a-49d0-985b-771729fe2d1c" />

*Figure: Windows 11 network configurtaions validation*

---

- Configured Ubuntu server with static IP addressing to support web services, DNS functionality, and SIEM integration.
<img width="1919" height="1200" alt="VirtualBox_Ubuntu_03_05_2026_09_38_31" src="https://github.com/user-attachments/assets/15a3751c-5514-42f7-8e62-1493ae552d21" />

*Figure: Ubuntu network configurations validation and Successful inter-subnet connectivity test*

---

- Configured Kali Linux attacker system with static IP addressing for controlled penetration testing and Red Team operations.
<img width="1919" height="1200" alt="VirtualBox_kali _03_05_2026_09_43_51" src="https://github.com/user-attachments/assets/27e80c29-573b-426d-96a8-39518b6570fd" />

*Figure: Kali linux network configurations validation*

--- 

## Stage 6: Connectivity Validation
- Tested connectivity to MikroTik gateways
- Verified inter-subnet routing using ping
- Confirmed communication between Windows, Linux, and server systems
- Troubleshot packet loss, routing, and adapter configuration issues


- Validated Windows endpoint connectivity to both local and remote segmented subnets through ICMP testing, confirming successful routing between internal network environments.
<img width="1280" height="720" alt="VirtualBox_windows 11 pro_03_05_2026_10_59_21" src="https://github.com/user-attachments/assets/afbb4a0a-534f-413a-85ff-59638cbaf370" />

*Figure: Successsful inter-subnet connectivity test*

---

- Validated successful communication between Red Team and server subnets through ICMP testing and inter-network routing verification.
<img width="1919" height="1200" alt="VirtualBox_kali _03_05_2026_09_45_07" src="https://github.com/user-attachments/assets/9910d354-8f81-4b16-8f53-9af3d24a7bd9" />

*Figure: Successsful inter-subnet connectivity test*

---

## Key Challenges and Lessons Learned
- Troubleshooting VirtualBox NAT vs bridged networking behavior
- Resolving inter-subnet routing issues
- Mapping MikroTik interfaces correctly
- Debugging VM connectivity across multiple hosts
- Balancing network isolation with functionality
  
## Stage 7: SIEM Preparation
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

---

🔐 *Learning cybersecurity through building, attacking, and defending systems.*
