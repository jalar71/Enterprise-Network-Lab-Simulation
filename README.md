# Enterprise-Network-Lab-Simulation
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
