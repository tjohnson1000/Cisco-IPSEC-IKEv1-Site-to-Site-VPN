# Cisco-IPSEC-IKEv1-Site-to-Site-VPN
Site-to-Site IPSec VPN Tunnels are used to allow the secure transmission of data, voice and
video between two sites (e.g offices or branches). The VPN tunnel is created over the Internet public
network and encrypted using a number of advanced encryption algorithms to provide confidentiality
of the data transmitted between the two sites.
# Cisco IPSEC IKEv1 Site-to-Site VPN Configuration
<img src="https://i.imgur.com/f83cYyX.png" height="80%" alt="Topology" />

## Overview
This project demonstrates how to configure an IPSEC IKEv1 Site-to-Site VPN between two Cisco routers. The VPN tunnel is established using EVE-NG for simulation and testing. The objective is to securely transmit data between two remote networks over an encrypted connection.

## Lab Setup
The topology consists of the following components:
- **Router A (R-A)** - Represents the local site.
- **Router B (R-B)** - Represents the remote site.
- **Host-A** - A client in the local network.
- **Host-B** - A client in the remote network.

### **Image Versions Used**
- vIOS: `vios-adventerprisek9-m.SPA.158-3.M2`
- VPCS: Integrated host device

### **Objective**
- Configure an **IPSEC IKEv1 Site-to-Site VPN** tunnel between Router A and Router B.
- Ensure **Host-A can communicate with Host-B** securely.

## Configuration Steps

### 1. Configure ISAKMP (IKE) - ISAKMP Phase 1
#### **Router A (R-A):**
```shell
crypto isakmp policy 10
encr aes
authentication pre-share
group 2
lifetime 86400
```

#### **Router B (R-B):**
```shell
crypto isakmp policy 10
encr aes
authentication pre-share
group 2
lifetime 86400
```

### 2. Create Extended Access List
#### **Router A (R-A):**
```shell
ip access-list extended vpn-acl
permit ip 10.1.1.0 0.0.0.255 10.2.2.0 0.0.0.255
```

#### **Router B (R-B):**
```shell
ip access-list extended vpn-acl
permit ip 10.2.2.0 0.0.0.255 10.1.1.0 0.0.0.255
```

### 3. Configure IPSec Transform Set (ISAKMP Phase 2)
#### **Router A (R-A):**
```shell
crypto ipsec transform-set TS esp-aes esp-md5-hmac
mode tunnel
```

#### **Router B (R-B):**
```shell
crypto ipsec transform-set TS esp-aes esp-md5-hmac
mode tunnel
```

### 4. Create Crypto Map
#### **Router A (R-A):**
```shell
crypto map VPN 10 ipsec-isakmp
set peer 40.0.0.2
set transform-set TS
match address vpn-acl
reverse-route static
```

#### **Router B (R-B):**
```shell
crypto isakmp key eve address 30.0.0.2
crypto map VPN 10 ipsec-isakmp
set peer 30.0.0.2
set transform-set TS
match address vpn-acl
reverse-route static
```

### 5. Apply Crypto Map to Interfaces
#### **Router A (R-A):**
```shell
interface GigabitEthernet0/0
crypto map VPN
```

#### **Router B (R-B):**
```shell
interface GigabitEthernet0/0
crypto map VPN
```

## Verification

### 1. Ping from Host-A to Host-B
```shell
ping 10.2.2.1
```
### 2. Verify Traffic Encryption
#### **Check IPSec Security Associations (SA)**
```shell
show crypto ipsec sa
```
#### **Check ISAKMP SA**
```shell
show crypto isakmp sa
```

## Repository Structure
```
├── configs/
│   ├── RA_config.txt   # Configuration for Router A
│   ├── RB_config.txt   # Configuration for Router B
├── README.md          # Documentation
├── screenshots/       # Screenshots of verification outputs
└── topology.png       # Network diagram
```

## Conclusion
This project showcases the process of configuring a secure **Site-to-Site VPN using Cisco IPSEC IKEv1**. It is useful for IT professionals looking to understand VPN deployments and security engineers aiming to reinforce network security.

---
**Author:** Travis Johnson  
**Company:** 10Digit Solutions LLC  
**GitHub Repository:** [Add link when available]

