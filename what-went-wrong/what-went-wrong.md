# ❌ What Went Wrong (And What I Learned)

In this lab I encountered a few problems while setting it up. While it was really hectic to fix them and find where the error was but it provided me with a lot of learning.. Here's a breakdown of the issues:

---

##  1. Windows 11 wasn’t getting its reserved IP

### Problem:
The Windows 11 client was getting a random IP from the DHCP pool, not the static IP I reserved in pfSense (`10.109.25.110`).

### Root Cause:
The network adapter on the Win11 machine was manually set to use a static IP — so it never asked pfSense for one.

### Fix:
I switched the NIC settings in the client to DHCP to obtain IP addresses automatically.
After that, the static mapping worked perfectly.

---

##  2. Some machines were getting IPs from VMware NAT instead of pfSense

### Problem:
Windows Server and sometimes the clients were getting 192.168.x.x addresses, even though pfSense was supposed to control DHCP.

### Root Cause:
Extra virtual NICs were active — VMware NAT and Host-Only adapters — and the machines were connected to more than one network.

### Fix:
I disabled all unnecessary NICs inside the VMs (`ncpa.cpl`), and in VMware, I made sure each VM was only connected to the custom network (VMnet2) that pfSense controls.

---

##  3. Clients had no internet even though pfSense could ping out

### Problem:
The Windows 11 and Ubuntu clients couldn’t access the internet, even though pfSense itself had internet via its WAN.

### Root Cause:
Clients didn’t have a valid default gateway set. They couldn’t reach anything outside the LAN.

### Fix:
I confirmed that the default gateway provided via DHCP was set to `10.109.25.1` (pfSense’s LAN). After correcting that in the DHCP settings and renewing the leases, clients could access the internet.

---

### 4. DNS wasn’t resolving anything (even though ping worked)

### Problem:
Clients could ping 8.8.8.8 but couldn’t resolve `google.com` or any other domain names.

### Root Cause:
DNS forwarding wasn’t configured on the Windows Server, which was acting as the primary DNS server.

### Fix:
I opened DNS Manager on the Windows Server and added `8.8.8.8` and `1.1.1.1` as forwarders under the server’s properties. After that, domain resolution worked instantly.

---

###  5. Windows Server had no internet access

### Problem:
Ubuntu and Windows 11 had internet, but the Windows Server didn’t — no ping, no DNS, nothing.

### Root Cause:
The server was using pfSense (`10.109.25.1`) as its DNS instead of itself (`127.0.0.1`). Also, the gateway wasn’t set in the static IP config.

### Fix:
I changed the server's DNS to `127.0.0.1` (since it’s running DNS locally) and added `10.109.25.1` as the default gateway. After that, it had full internet access and could resolve external domains.

---

##  Takeaways

- Small config errors (like forgetting to set the gateway or DNS) can completely break communication.
- Troubleshooting tools like `ipconfig`, `ping`, `tracert`, and `nslookup` are your best friends.
- Always double-check which network your VMs are connected to — especially with VMware.
- Documenting problems as I go helped me not only fix them, but understand why they happened.
