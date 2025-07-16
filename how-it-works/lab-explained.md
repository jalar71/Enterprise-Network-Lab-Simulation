#  How the Enterprise Network Lab Simulation Works

This lab simulates a small enterprise network where a firewall controls traffic, a server manages users and authentication, and clients (Windows and Linux) operate within that controlled environment. 

---

## 🔌 Virtual Network Overview

All machines are running on **VMware Workstation** using a **custom host-only network** (`VMnet2`). This network is completely isolated from the internet unless routed through **pfSense**, which acts as the firewall and gateway.

---

## 🌐 Network Setup and Traffic Flow

1. **pfSense** is the central router and DHCP server.
   - LAN: `10.109.25.1`, WAN: `192.168.x.x` (host NAT network)
   - It provides IP addresses to all VMs using static DHCP mappings.
   - It routes all LAN traffic to the WAN and enables internet access for clients.
   - Firewall rules can be created to allow, block, or log traffic.

2. **Windows Server 2019** functions as the **Active Directory Domain Controller** and **DNS server**.
   - Domain: `rjlab.local`
   - Authenticates users, manages group policies, and controls DNS.
   - Configured to forward external DNS queries to `8.8.8.8` Google DNS resolver.

3. **Windows 11 Client** is joined to the domain.
   - Authenticates via the domain controller.
   - Resolves DNS through the Windows Server.
   - Receives IP from pfSense (static mapping).

4. **Ubuntu Client** is also domain-joined.
   - Integrated into Active Directory using `sssd` and `realm`.
   - Also receives IP from pfSense and uses Windows Server for DNS.

---

## 🛠️ IP Address Assignments

| Device         | Role                         | IP Address      |
|----------------|------------------------------|-----------------|
| pfSense (LAN)  | DHCP + Gateway + Firewall    | `10.109.25.1`   |
| Windows Server | AD + DNS                     | `10.109.25.100` |
| Win11 Client   | Domain-joined workstation    | `10.109.25.110` |
| Ubuntu Client  | Domain-joined Linux client   | `10.109.25.111` |

---

##  Key Concepts Explored

- **Static DHCP with MAC mapping** for consistent IPs
- **Active Directory** as a central identity and access platform
- **DNS hierarchy** with local resolution + external forwarding
- **Routing and firewall use** using pfSense
- **Cross-platform domain integration** with both Windows and Linux clients

---

##  Summary

This lab simulates how a real enterprise network operates:

- Centralized user authentication and policy management
- Controlled internet access via a firewall/gateway
- DNS and DHCP services managed internally
- Linux and Windows machines working under one domain
- Full traffic flow routed and controlled
