# IP addressing plan

## 1. HQ / Main Site Infrastructure (Casablanca)

### A. Security Zones & Addressing
* **LAN Zone (`10.1.0.0/16`):**
  * **VLAN 10 (HR):** `10.1.10.0/24` (Gateway on FortiGate sub-interface: `10.1.10.254`). DHCP server configured directly on the FortiGate sub-interface (`10.1.10.1 - 10.1.10.100`).
  * **VLAN 20 (Sales):** `10.1.20.0/24` (Gateway on FortiGate sub-interface: `10.1.20.254`). DHCP server configured directly on the FortiGate sub-interface (`10.1.20.1 - 10.1.20.100`).
  * **VLAN 30 (IT):** `10.1.30.0/24` (Gateway on FortiGate sub-interface: `10.1.30.254`). DHCP server configured directly on the FortiGate sub-interface (`10.1.30.1 - 10.1.30.100`).
  * **VLAN 40 (CONTROL):** `10.1.40.0/24` (Gateway on FortiGate sub-interface: `10.1.40.254`). DHCP server configured directly on the FortiGate sub-interface (`10.1.40.1 - 10.1.40.100`).
  * **Endhosts:** HR1, HR2, Sales1, Sales2, IT1, and IT2.
* **MGMT Zone (`192.168.10.0/24`):**
  * **DSW1 Management:** `192.168.10.11` (routed interface connected to MGMT switch)
  * **DSW2 Management:** `192.168.10.12` (routed interface connected to MGMT switch)
  * **DMZ Switch Management:** `192.168.10.2`
  * **Management Workstation:** `192.168.10.60`
  * **FortiGate-HQ Active MGMT:** `192.168.10.251`
  * **FortiGate-HQ Passive MGMT:** `192.168.10.252`
* **DMZ Zone (`172.16.10.0/24`):**
  * **FortiGate-HQ DMZ Gateway:** `172.16.10.254`
  * **Web-Server:** `172.16.10.1`
  * **FTP-Server:** `172.16.10.20` (*this was not implemented*)
  * **Mail-Server:** `172.16.10.30` (*this was not implemented*)

### B. HQ Edge & Provider Interconnects
* **ISP1 Edge Link (`196.200.10.16/30`):** FortiGate `WAN1` (Port 5) IP `.17` $\leftrightarrow$ `ISP1-casa` IP `.18`.
* **ISP2 Edge Link (`213.150.20.16/30`):** FortiGate `WAN2` (Port 4) IP `.17` $\leftrightarrow$ `ISP2-casa` IP `.18`.
---
### Internet Access & Cloud NAT Pipeline
* `ISP1-casa` and `ISP2-casa` connect to simulated Internet Clouds over network `192.168.36.0/24` (ISP1-casa IP: `.10`, ISP2-casa IP: ``.20``, Cloud IP: `.1`).
* **Outbound NAT:** 
  1. ISP routers translate outbound traffic to `192.168.36.0/24` (EVE-NG `pnet1`).
  2. The EVE-NG host bridges `pnet1` to `pnet0`.
  3. Google Cloud internal NAT translates the address to the GCP internal network.
  4. Traffic exits to the public Internet through the GCP VM's public IP.

---

## 2. Remote Branch Office Sites (Spokes)

Each branch consists of a single **FortiGate Firewall**, a **Branch Switch**, local endhosts (PCs), and dual WAN links connected to dedicated branch-level provider routers.

### A. Rabat Branch
* **Local Subnet:** `10.2.0.0/24`
* **FortiGate Device:** `FortiGate-Rabat`
* **ISP1 WAN Subnet (`196.200.11.0/30`):** FortiGate IP `.1` $\leftrightarrow$ `ISP1-rabat` IP `.2`.
* **ISP2 WAN Subnet (`213.150.21.0/30`):** FortiGate IP `.1` $\leftrightarrow$ `ISP2-rabat` IP `.2`.
* **Transit Backhaul to Hub:**
  * ISP1 Link (`196.200.10.0/30`): `ISP1-rabat` IP `.1` $\leftrightarrow$ `ISP1-casa` IP `.2`.
  * ISP2 Link (`213.150.20.0/30`): `ISP2-rabat` IP `.1` $\leftrightarrow$ `ISP2-casa` IP `.2`.

### B. Tangier Branch
* **Local Subnet:** `10.3.0.0/24`
* **FortiGate Device:** `FortiGate-Tangier`
* **ISP1 WAN Subnet (`196.200.12.0/30`):** FortiGate IP `.1` $\leftrightarrow$ `ISP1-tangier` IP `.2`.
* **ISP2 WAN Subnet (`213.150.22.0/30`):** FortiGate IP `.1` $\leftrightarrow$ `ISP2-tangier` IP `.2`.
* **Transit Backhaul to Hub:**
  * ISP1 Link (`196.200.10.4/30`): `ISP1-tangier` IP `.5` $\leftrightarrow$ `ISP1-casa` IP `.6`.
  * ISP2 Link (`213.150.20.4/30`): `ISP2-tangier` IP `.5` $\leftrightarrow$ `ISP2-casa` IP `.6`.
