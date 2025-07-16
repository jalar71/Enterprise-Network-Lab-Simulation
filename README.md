# Enterprise-Network-Lab-Simulation
***Skip to how-it-works.md to read how the whole setup works.*
This simulation project was inspired by a few questions I had and decided to find the answers by setting up my own lab environment:
- How do Linux systems integrate with Active Directory?
- How do enterprise environments route and control client traffic?
- How can software be batch-installed across multiple Windows clients?

So here is the GitHub documentation about my learning and how it went.
---

## Lab Setup
Everything was built using VMware Workstation on a custom virtual network (`10.109.25.0/24`), routed through pfSense.

| Machine         | IP Address      | Role                             |
|----------------|------------------|----------------------------------|
| pfSense         | 10.109.25.1      | Firewall, DHCP, gateway          |
| Windows Server  | 10.109.25.100    | Active Directory + DNS server    |
| Windows 11      | 10.109.25.110    | Domain-joined client             |
| Ubuntu Client   | 10.109.25.111    | Domain-joined Linux client       |

All machines are on the same subnet and receive their IPs through static DHCP reservations via MAC address mapping in pfSense.

---
While I was working on this lab there a few errors I ran into and I am documenting that as well for anyone and myself to help troubleshooting in the future.

## ✅ What’s Working

- 🔧 pfSense is handling routing between LAN and WAN(Internet)
- 📡 Static DHCP mappings based on MAC addresses
- 🏢 Active Directory and DNS services running on Windows Server
- 🖥️ Both Windows and Ubuntu clients successfully joined to the domain
- 🌐 Internet access is working on all machines
- 🧠 Windows Server DNS forwards external queries to 8.8.8.8(To resolve DNS requests not solvable by the Windows Server)
## 🐞 What Didn’t Work (Troubleshooting Highlights)

See [`what-went-wrong.md`](./what-went-wrong.md) for more details, but here are a few key issues I ran into:

- Win11 wasn’t getting the right IP because it was set to static instead of DHCP
- Extra network adapters (from VMware NAT) were causing routing issues
- Windows Server couldn’t reach the internet — its DNS pointed to pfSense instead of itself
- DNS forwarding wasn’t configured initially on the server, so domain lookups failed

All of these helped me learn and sharpen my troubleshooting skills using tools like `ping`, `ipconfig`, `tracert`, and `nslookup`.

---

## Directory Guide

```plaintext
📁 architecture/     → Network diagram and VM configuration notes
📁 configs/          → pfSense and Windows Server DNS settings
📁 troubleshooting/  → Fix logs for specific issues
📁 screenshots/      → Visual references from the l
