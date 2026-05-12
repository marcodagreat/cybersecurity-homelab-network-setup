# Cybersecurity Homelab: Firewall Segmentation & Network Isolation

## Overview

This phase of my cybersecurity homelab focused on improving network segmentation, firewall control, and isolation between my virtual lab environment and my real home network.

The goal was to allow controlled VM-to-VM communication while preventing lab systems from reaching my host machines or home LAN.

This setup uses a **MikroTik CHR virtual router** inside VirtualBox to handle:

- Routing between lab subnets
- NAT for internet access
- Firewall filtering
- Network segmentation
- Controlled inter-VM communication

---

## Lab Design Goals

The main objectives for this phase were:

- Isolate lab VMs from the home LAN
- Prevent VMs from communicating with Host A and Host B operating systems
- Allow required VM-to-VM communication
- Preserve internet access through NAT
- Create a safer environment for attack simulation and detection engineering
- Build an enterprise-style segmented network inside a homelab

---

## Network Architecture

| Network | Subnet | Purpose | Location |
|---|---|---|---|
| Home LAN | `192.168.1.0/24` | Real home network | Home router / Wi-Fi |
| Transit Network | `10.10.10.0/24` | Inter-host VM communication | Dedicated physical switch |
| Blue Team Network | `10.20.20.0/24` | Monitoring, logging, web server, IDS/IPS | Host A internal network |
| Red Team Network | `10.30.30.0/24` | Attack simulation | Host A internal network |

---

## Host A Configuration

Host A runs VirtualBox and contains the main routing and lab systems.

### Host A VMs

| VM | Network | IP Address | Adapter Type |
|---|---|---|---|
| MikroTik CHR Router | Multiple networks | Multiple gateway IPs | NAT, Bridged, Internal |
| Kali Linux | Red Team | `10.30.30.10` | Internal Adapter |
| Ubuntu Web Server | Blue Team | `10.20.20.20` | Internal Adapter |
| Ubuntu IDS/IPS | Blue Team | `10.20.20.30` | Internal Adapter |
| Splunk Server | Blue Team | `10.20.20.10` | Internal Adapter |

### MikroTik Router Interfaces

| MikroTik Interface | Purpose | IP Address |
|---|---|---|
| `ether9` | Internet / NAT | DHCP from home LAN |
| `ether5` | Transit Network / Dedicated Switch | `10.10.10.1/24` |
| `ether7` | Blue Team Network | `10.20.20.1/24` |
| `ether8` | Red Team Network | `10.30.30.1/24` |

---

## Host B Configuration

Host B also runs VirtualBox, but it is configured differently to improve isolation.

### Host B Network Design

| Interface | Purpose |
|---|---|
| Wi-Fi | Host B internet access only |
| Ethernet to dedicated switch | VirtualBox VM transit traffic only |

Host B uses Wi-Fi for normal host internet access.  
The Ethernet adapter connected to the dedicated physical switch is used only for VirtualBox bridged VM traffic.

This helps prevent Host B’s operating system from mixing normal home network traffic with lab VM traffic.

### Host B VMs

| VM | Network | IP Address | Adapter Type |
|---|---|---|---|
| Windows Server / Active Directory | Transit Network | `10.10.10.10` | Bridged Adapter |
| Windows 11 Client | Transit Network | `10.10.10.20` | Bridged Adapter |

---

## Dedicated Physical Switch

A dedicated physical switch is used to connect Host A and Host B for lab VM communication.

The dedicated switch carries the **Transit Network**:

10.10.10.0/24

---

## Firewall Policy Design

The firewall policy for this homelab was designed to enforce segmentation between the lab networks, host operating systems, and the home LAN.

The MikroTik CHR router is responsible for routing and filtering traffic between the segmented networks.

The main security goal was to allow only required lab traffic while blocking unauthorized access to the home network and host machines.

### Firewall Strategy

The firewall rules were designed around three main concepts:

1. **Explicit allow rules**
   - Allow only required traffic such as internet access, Splunk forwarding, Active Directory communication, and VM-to-VM lab traffic.

2. **Explicit deny rules**
   - Intentionally block traffic from lab VMs to the home LAN and host operating systems.

3. **Default drop behavior**
   - Any traffic that is not specifically allowed should be dropped.

---

## Explicit vs Implicit Firewall Rules

### Explicit Deny

An explicit deny rule blocks traffic using a specific firewall rule.

### Implicit Deny

An implicit deny happens when traffic is blocked becaused no rule exists to allow it.

In this lab, i used explicit deny rules for isolation boundaries so that the tarffic policy is clear and intentional.

---

## Allowed  Traffic

### The following traffic is intentionally allowed in the lab:

| Source Network                    | Destination                       | Purpose                                              |
| --------------------------------- | --------------------------------- | ---------------------------------------------------- |
| Blue Team Network `10.20.20.0/24` | Internet                          | System updates, package downloads, tool installation |
| Red Team Network `10.30.30.0/24`  | Internet                          | Kali updates, tool downloads, testing                |
| Transit Network `10.10.10.0/24`   | Internet                          | Windows updates and endpoint access if required      |
| Red Team Network `10.30.30.0/24`  | Blue Team Network `10.20.20.0/24` | Attack simulation and detection testing              |
| Blue Team Network `10.20.20.0/24` | Red Team Network `10.30.30.0/24`  | Response traffic and monitoring                      |
| Blue Team Network `10.20.20.0/24` | Transit Network `10.10.10.0/24`   | Splunk, logging, AD-related communication            |
| Transit Network `10.10.10.0/24`   | Blue Team Network `10.20.20.0/24` | Endpoint logging and communication with Splunk       |
| Windows 11 Endpoint               | Active Directory Server           | Domain communication and authentication              |
| Endpoint VMs                      | Splunk Server                     | Log forwarding and telemetry collection              |

---

## Block Traffic 

### The following traffic is intentionally blocked: 

| Source Network                    | Destination                       | Reason                                              |
| --------------------------------- | --------------------------------- | --------------------------------------------------- |
| Blue Team Network `10.20.20.0/24` | Home LAN `192.168.1.0/24`         | Prevent lab systems from reaching home devices      |
| Red Team Network `10.30.30.0/24`  | Home LAN `192.168.1.0/24`         | Prevent attacker VM from reaching home devices      |
| Transit Network `10.10.10.0/24`   | Home LAN `192.168.1.0/24`         | Prevent AD and endpoint VMs from reaching home LAN  |
| Home LAN `192.168.1.0/24`         | Blue Team Network `10.20.20.0/24` | Prevent home devices from accessing lab systems     |
| Home LAN `192.168.1.0/24`         | Red Team Network `10.30.30.0/24`  | Prevent home devices from accessing attacker subnet |
| Home LAN `192.168.1.0/24`         | Transit Network `10.10.10.0/24`   | Prevent home devices from accessing lab endpoints   |
| Lab VMs                           | Host A Operating System           | Protect host machine from lab traffic               |
| Lab VMs                           | Host B Operating System           | Protect host machine from lab traffic               |
| Unauthorized subnet traffic       | Any destination                   | Enforce segmentation and reduce attack surface      |

---

## Validation Testing

After applying the firewall rules, I tested communication before and after segmentation to verify that the rules worked as intended.

### Before Firewall Rules

Before applying the firewall rules, some VMs were able to reach unintended systems.

Examples of successful connectivity before segmentation:

<img width="1920" height="1080" alt="Ubuntu_ 10 20 20 10 - Successful ping to LAN" src="https://github.com/user-attachments/assets/21bd103a-2b12-47b8-8092-671472bf3943" />

---

<img width="1280" height="720" alt="Windows 11 (Transit Network) _ 10 10 10 20 - Successful ping to LAN" src="https://github.com/user-attachments/assets/b769cf15-3da7-48b3-b1dc-d6dba58135dc" />

---

<img width="1920" height="1080" alt="kali_10 30 30 5 - Ping success to LAN" src="https://github.com/user-attachments/assets/4c894d35-cdaf-43d1-8712-c1099732db25" />

*Figure: Successful ping from VMs to Host before Firewall rules*

This showed that the lab was not fully isolated from the host systems and home network.

---

### After Firewall Rules

After applying the MikroTik firewall rules, traffic from lab VMs to host machines and the home LAN was blocked.

Expected results after segmentation:

| Test                           |  Expected Results   |
| -------------------------      |  ----------------   |
| Kali Linux to Host A OS.       |    Blocked          |
| Kali Linux to Host B OS.       |    Blocked          |
| Kali Linux to Home LAN .       |    Blocked          |
| Blue Team VM to Home LAN.      |    Blocked          |
| Transit VM to Home LAN.        |    Blocked          |
| Home LAN to Lab Networks.      |    Blocked          |
| Kali Linux to Windows Server AD|    Allowed          |
| Windows 11 to Active Directory |    Allowed          |
| Endpoint VMs to splunk         |    Allowed          | 
| Lab VMs to internet            |    Allowed          | 

This confirmed that the lab networks were isolated from the home network while still allowing controlled communication for cybersecurity testing, logging, and detection engineering.

---

## Validation Commands

The following commands were used to test isolation and connectivity.
### Ping Test

<img width="1920" height="1080" alt="kali_10 30 30 5 - Ping failed  to LAN" src="https://github.com/user-attachments/assets/7b0d511f-cfa8-48e4-b613-8dc9970a2257" />

---

<img width="1920" height="1080" alt="Ubuntu_ 10 20 20 10 - failed ping to LAN" src="https://github.com/user-attachments/assets/9285a72c-c232-463c-a530-d16a4c06ae22" />

---

<img width="1280" height="720" alt="Windows 11 (Transit Network)_ 10 10 10 20  -Failed ping" src="https://github.com/user-attachments/assets/7a758296-3e81-4dd9-8b1b-882157720dd1" />

*Figure: Verified Vms failed to ping Host or any other device on Home LAN*

---

## MikroTik Firewall Rule Verification


<img width="720" height="400" alt="MikroTik firewall Rules" src="https://github.com/user-attachments/assets/a6c965e9-9129-443e-9b15-a23eb3ba83ab" />

---

<img width="720" height="400" alt="MikroTik firewall Rules_page2" src="https://github.com/user-attachments/assets/06ff31a6-0701-423c-a21f-17c089864d4d" />


<img width="720" height="400" alt="MikroTik firewall Rules_page3" src="https://github.com/user-attachments/assets/e31ba077-d4bf-4bcd-8184-15c304f82424" />

*Figure: Mikrotik Firewall Rules listed* 

---




