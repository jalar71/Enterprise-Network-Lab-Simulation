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
![pfSense DHCP Config](https://raw.githubusercontent.com/jalar71/Enterprise-Network-Lab-Simulation/main/screenshots/pf_sense_dhcp_config.png)
![pfSense CLI Interface](https://raw.githubusercontent.com/jalar71/Enterprise-Network-Lab-Simulation/main/screenshots/pf_sense_cli_interface.png)

![pfSense DHCP Static Mapping](https://raw.githubusercontent.com/jalar71/Enterprise-Network-Lab-Simulation/main/screenshots/pf_sense_dhcp_static_mapping.png)

![pfSense Firewall Rules](https://raw.githubusercontent.com/jalar71/Enterprise-Network-Lab-Simulation/main/screenshots/pf_sense_firewall_rules.png)


2. **Windows Server 2019** functions as the **Active Directory Domain Controller** and **DNS server**.
   - Domain: `rjlab.local`
   - Authenticates users, manages group policies, and controls DNS.
   - Configured to forward external DNS queries to `8.8.8.8` Google DNS resolver.
![Windows Server DNS Forwarding](https://github.com/jalar71/Enterprise-Network-Lab-Simulation/blob/main/screenshots/Win-Server-DNS-Forwarding.png?raw=true)


3. **Windows 11 Client** is joined to the domain.
   - Authenticates via the domain controller.
   - Resolves DNS through the Windows Server.
   - Receives IP from pfSense (static mapping).

![Windows 11 AD Join Step 1](https://github.com/jalar71/Enterprise-Network-Lab-Simulation/blob/main/screenshots/win-11-AD-joing%20(1).png?raw=true)

![Windows 11 AD Join Step 2](https://github.com/jalar71/Enterprise-Network-Lab-Simulation/blob/main/screenshots/win-11-AD-joing%20(2).png?raw=true)

![Windows 11 AD Join Step 3](https://github.com/jalar71/Enterprise-Network-Lab-Simulation/blob/main/screenshots/win-11-AD-joing%20(3).png?raw=true)


4. **Ubuntu Client** is also domain-joined.
   - Integrated into Active Directory using `sssd` and `realm`.
   - Also receives IP from pfSense and uses Windows Server for DNS.
![Ubuntu AD CLI Command](https://github.com/jalar71/Enterprise-Network-Lab-Simulation/blob/main/screenshots/ubuntu-AD-cli-command.png?raw=true)

![Ubuntu AD Joining Success](https://github.com/jalar71/Enterprise-Network-Lab-Simulation/blob/main/screenshots/ubuntu-AD-joining-success.png?raw=true)

![AD Windows & Linux Client Overview](https://github.com/jalar71/Enterprise-Network-Lab-Simulation/blob/main/screenshots/AD_win_linux_client.png?raw=true)

5.  Batch Software Installation via Active Directory (GPO)

To simulate real-world enterprise management, I configured **Group Policy Objects (GPOs)** on the Windows Server to perform **batch software installation** on Windows domain-joined clients.

####  Objective:
Automate software deployment to client machines during startup or user login, using centralized domain control.

#### 🛠️ Tools Used:
- Windows Server 2022 (Domain Controller)
- `.msi` installer packages (e.g., Wireshark, VLC)
- Group Policy Management Console (GPMC)
- Shared network folder (`\\rjlab.local\software`)

####  Steps:
1. **Created a shared folder** on the Windows Server with read access for `Authenticated Users`.
2. **Placed `.msi` installers** (e.g., `Wireshark.msi`) into this shared folder.
3. In GPMC:
   - Created a new GPO linked to the target OU (Organizational Unit).
   - Navigated to:  
     `Computer Configuration → Policies → Software Settings → Software Installation`
   - Added a new package using the UNC path (e.g., `\\rjlab.local\software\7zip.msi`).
4. **Rebooted the client machine** to trigger the installation.

#### ✅ Result:
Upon reboot, domain-joined Windows clients automatically installed the specified software without user interaction, demonstrating centralized control similar to enterprise environments.

![VLC and Wireshark GPO Deployment](https://github.com/jalar71/Enterprise-Network-Lab-Simulation/blob/main/screenshots/vlc-wireshark-install-gpo.png?raw=true)

![VLC and Wireshark Installation Success](https://github.com/jalar71/Enterprise-Network-Lab-Simulation/blob/main/screenshots/wireshark-vlc-success.png?raw=true)

![Software Installation Directory on Client](https://github.com/jalar71/Enterprise-Network-Lab-Simulation/blob/main/screenshots/folder-software-install-directory.png?raw=true)

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
- **Batch Installation** of software packages on Windows clients.
