# 04 - BGP underlay (simulated ISPs)

[Previous: 03-ha-sdwan](../03-ha-sdwan/README.md) | [Main README](../README.md) | [Next: 05-ipsec-vpn-sdwan](../05-ipsec-vpn-sdwan/README.md)

The hub and both branches peer with simulated ISP routers using eBGP, while the ISP routers exchange routes over iBGP. This underlay carries the IPsec tunnels built in the next phase.

## BGP configuration

### ISPs Routers config

On **ISP1-casa**:

```bash
# Join ISP1's AS BGP config
router bgp 64200

  # Set up BGP router-id
  bgp router-id 196.200.10.18

  # Set up ISP1's branches as iBGP neighbors + desc
  neighbor 196.200.10.1 remote-as 64200
  neighbor 196.200.10.1 description Transit to ISP1-rabat
  neighbor 196.200.10.5 remote-as 64200
  neighbor 196.200.10.5 description Transit to ISP1-tangier

  # Set up hub firewall as eBGP neighbor + desc
  neighbor 196.200.10.17 remote-as 64100
  neighbor 196.200.10.17 description Peering to Hub FortiGate WAN1

  # Advertise the "ISP1-casa to hub-firewall" subnet
  network 196.200.10.16 mask 255.255.255.252

  # Set up next-hop-self for ISP1's branches
  neighbor 196.200.10.1 next-hop-self
  neighbor 196.200.10.5 next-hop-self

  # Advertise default route to ISP1's branches
  neighbor 196.200.10.1 default-originate
  neighbor 196.200.10.5 default-originate
```

[ISP1-casa BGP configuration](img/config-isp1-casa-bgp.png)



On **ISP2-casa**:

```bash
# Join ISP2's AS BGP config
router bgp 64300

  # Set up BGP router-id
  bgp router-id 213.150.20.18

  # Set up ISP2's branches as iBGP neighbors + desc
  neighbor 213.150.20.1 remote-as 64300
  neighbor 213.150.20.1 description Transit to ISP2-rabat
  neighbor 213.150.20.5 remote-as 64300
  neighbor 213.150.20.5 description Transit to ISP2-tangier

  # Set up hub firewall as eBGP neighbor + desc
  neighbor 213.150.20.17 remote-as 64100
  neighbor 213.150.20.17 description Peering to Hub FortiGate WAN2

  # Advertise the "ISP2-casa to hub-firewall" subnet
  network 213.150.20.16 mask 255.255.255.252

  # Set up next-hop-self with ISP2's branches
  neighbor 213.150.20.1 next-hop-self
  neighbor 213.150.20.5 next-hop-self

  # Advertise default route to ISP2's branches
  neighbor 213.150.20.1 default-originate
  neighbor 213.150.20.5 default-originate
```

[ISP2-casa BGP configuration](img/config-isp2-casa-bgp.png)



On **ISP1-rabat**:

```bash
# Join ISP1's AS BGP config
router bgp 64200

  # Set up BGP router-id
  bgp router-id 196.200.11.2

  # Set up ISP1-casa as iBGP neighbor + desc
  neighbor 196.200.10.2 remote-as 64200
  neighbor 196.200.10.2 description iBGP to ISP1-casa

  # Set up next-hop-self for neighbor: ISP1-casa
  neighbor 196.200.10.2 next-hop-self

  # Set up Fortigate-Rabat as eBGP neighbor + desc
  neighbor 196.200.11.1 remote-as 64100
  neighbor 196.200.11.1 description eBGP to FortiGate-Rabat

  # Advertise the "ISP1-rabat to Fortigate-Rabat" subnet
  network 196.200.11.0 mask 255.255.255.252
```

[ISP1-rabat BGP configuration](img/config-isp1-rabat-bgp.png)



On **ISP2-rabat**:

```bash
# Join ISP2's AS BGP config
router bgp 64300

  # Set up BGP router-id
  bgp router-id 213.150.21.2

  # Set up ISP2-casa as iBGP neighbor + desc
  neighbor 213.150.20.2 remote-as 64300
  neighbor 213.150.20.2 description iBGP to ISP2-casa

  # Set up next-hop-self for neighbor: ISP2-casa
  neighbor 213.150.20.2 next-hop-self

  # Set up Fortigate-Rabat as eBGP neighbor + desc
  neighbor 213.150.21.1 remote-as 64100
  neighbor 213.150.21.1 description eBGP to FortiGate-Rabat

  # Advertise the "ISP2-rabat to Fortigate-Rabat" subnet
  network 213.150.21.0 mask 255.255.255.252
```

[ISP2-rabat BGP configuration](img/config-isp2-rabat-bgp.png)



On **ISP1-tangier**:

```bash
# Join ISP1's AS BGP config
router bgp 64200

  # Set up BGP router-id
  bgp router-id 196.200.12.2

  # Set up ISP1-casa as iBGP neighbor + desc
  neighbor 196.200.10.6 remote-as 64200
  neighbor 196.200.10.6 description iBGP to ISP1-casa

  # Set up next-hop-self for neighbor: ISP1-casa
  neighbor 196.200.10.6 next-hop-self

  # Set up Fortigate-Tangier as eBGP neighbor + desc
  neighbor 196.200.12.1 remote-as 64100
  neighbor 196.200.12.1 description eBGP to FortiGate-Tangier

  # Advertise the "ISP1-tangier to Fortigate-Tangier" subnet
  network 196.200.12.0 mask 255.255.255.252
```

[ISP1-tangier BGP configuration](img/config-isp1-tangier-bgp.png)



On **ISP2-tangier**:

```bash
# Join ISP2's AS BGP config
router bgp 64300

  # Set up BGP router-id
  bgp router-id 213.150.22.2

  # Set up ISP2-casa as iBGP neighbor + desc
  neighbor 213.150.20.6 remote-as 64300
  neighbor 213.150.20.6 description iBGP to ISP2-casa

  # Set up next-hop-self for neighbor: ISP2-casa
  neighbor 213.150.20.6 next-hop-self

  # Set up Fortigate-Tangier as eBGP neighbor + desc
  neighbor 213.150.22.1 remote-as 64100
  neighbor 213.150.22.1 description eBGP to FortiGate-Tangier

  # Advertise the "ISP2-tangier to Fortigate-Tangier" subnet
  network 213.150.22.0 mask 255.255.255.252
```

[ISP2-tangier BGP configuration](img/config-isp2-tangier-bgp.png)

### Firewalls config

#### FortiGate-HQ-Active

* Access the GUI, go to **Network** > **BGP**, use `64100` as **AS**, and `196.200.10.17` as **Router-ID**
* From the same page, under the **Neighbors** section, add the following addresses:

  * **IP**: `196.200.10.18` / **Remote AS**: `64200` (*ISP1-casa*)
  * **IP**: `213.150.20.18` / **Remote AS**: `64300` (*ISP2-casa*)

[Hub FortiGate BGP configuration](img/config-fw-bgp-hq.png)



##### Fortigate-Rabat

* Access the GUI, go to **Network** > **BGP**, use `64100` as **AS**, and `196.200.11.1` as **Router-ID**
* From the same page, under the **Neighbors** section, add the following addresses:

  * **IP**: `196.200.11.2` / **Remote AS**: `64200` (*ISP1-rabat*)
  * **IP**: `213.150.21.2` / **Remote AS**: `64300` (*ISP2-rabat*)

[Rabat FortiGate BGP configuration](img/config-fw-bgp-rabat.png)



##### Fortigate-Tangier

* Access the GUI, go to **Network** > **BGP**, use `64100` as **AS**, and `196.200.12.1` as **Router-ID**
* From the same page, under the **Neighbors** section, add the following addresses:

  * **IP**: `196.200.12.2` / **Remote AS**: `64200` (*ISP1-tangier*)
  * **IP**: `213.150.22.2` / **Remote AS**: `64300` (*ISP2-tangier*)

\---

[Tangier FortiGate BGP configuration](img/config-fw-bgp-tangier.png)

### BGP clearing config (**must do!!!**)

After configuring all BGP settings on the routers and firewalls. You have to run a "*BGP clear command*" on each of them so that routes get re-learned again the right way.

Run:

```bash
# On Firewalls
execute router clear bgp all

# On routers
clear ip bgp \\\\\\\*
```

* After this, the BGP TCP sessions will be cleared and all firewalls \& routers will loose connectivity to their BGP neighbors.
* Then, they will all re-form BGP neighborships and learn BGP routes from each other the right way.

\---

### Concept

The AS number for each corporate network is set up as the following:

* `64100` for **CBI**'s corporate network (*hub + branch firewalls BGP*).
* `64200` for **ISP1**'s corporate network (*ISP1's hub + branch routers BGP*).
* `64300` for **ISP2**'s corporate network (*ISP2's hub + branch routers BGP*).

The hub firewall (*config is done only on the active firewall*) and the hub ISPs are configured as eBGP neighbors.

Each hub ISP and its corresponding ISP routers in each branch are configured as iBGP neighbors. Next-hop-self is set up on the hub ISPs for their branch ISP routers. As well as on each branch ISP for its corresponding hub ISP.

* The next-hop-self is configured on both iBGP neighbors so that when an ISP router(*branch or hub ISP*) tries to advertise a route it's learned via eBGP (*from a spoke or hub firewall*), it explicitly changes the next-hop address to itself, so that the iBGP neighbor knows how to reach the advertised subnet (*which is by sending traffic to its iBGP neighbor*).

Each hub \& branch ISP is set up to advertise the subnet from its connection to the company's hub or spoke firewall. This provides the essential BGP routes that will later on link the hub firewall and each branch firewall via an IPsec VPN tunnel. The routes are;

* In the hub firewall, a route to reach the public IPs of each branch firewall (*hub FW > hub ISP > branch ISP > branch FW*).
* In each branch firewall, a route to reach the public IPs of the hub firewall (*branch FW > branch ISP > hub ISP > hub FW*).

Hub ISPs are set up to advertise their default route(*to the internet*) to their iBGP neighbors(*branch ISPs*). After the advertisements converge, each branch ISP will have a default route to the internet (*learned via iBGP*).

* The branch ISPs will keep advertising this default route to the corresponding branch firewall, but it won't be chosen as the active route in the firewalls routing table, since there is already a static default route (*pointing to SD-WAN zone*) configured on each branch FW.
* But, If the SD-WAN members fail the SLA health check, since update default route is enabled on the SLA, this will remove their static routes from the active routing table. In this case, this BGP route will be chosen as the default route.

The last commands that you must run are here: [BGP clearing config (**must do!!!**)](#bgp-clearing-config-must-do)

## Verification and tests

### Test 1 - BGP neighbors on ISP1-casa

* **Device:** ISP1-casa
* **Command:** `show ip bgp summary`
* **Result:** Hub firewall (eBGP) and the other ISP routers (iBGP) are Established.

[BGP neighbors on ISP1-casa](img/test-bgp-summary-isp1-casa.png)

### Test 2 - BGP neighbors on ISP2-casa

* **Device:** ISP2-casa
* **Command:** `show ip bgp summary`
* **Result:** All neighbors Established.

[BGP neighbors on ISP2-casa](img/test-bgp-summary-isp2-casa.png)

### Test 3 - BGP neighbors on the hub firewall

* **Device:** Hub FortiGate
* **Command:** `get router info bgp summary`
* **Result:** Both ISP neighbors Established.

[BGP neighbors on the hub firewall](img/test-bgp-summary-hub-fw.png)

### Test 4 - BGP routes on the hub firewall

* **Device:** Hub FortiGate
* **Command:** `get router info routing-table bgp`
* **Result:** Branch WAN networks are learned via BGP.

[BGP routes on the hub firewall](img/test-hub-fw-bgp-routes.png)

### Test 5 - Rabat firewall routing

* **Device:** Rabat FortiGate
* **Command:**  `get router info routing-table all`
* **Result:** Neighbors Established; hub WAN prefixes present.

[Rabat firewall routing](img/test-rabat-fw-routing.png)

### Test 6 - Tangier firewall routing

* **Device:** Tangier FortiGate
* **Commands:** `get router info routing-table all`
* **Result:** Neighbors Established.

[Tangier firewall routing](img/test-tangier-fw-routing.png)

### Test 7 - Underlay reachability from branches

* **Devices:** Rabat \& Tangier FortiGate
* **Command:** `execute ping 196.200.10.17` and `execute ping 213.150.20.17`
* **Result:** Replies on both ISP paths.

[Underlay reachability from branches](img/test-underlay-branches-to-hub-wan1-rabat.png)



[Underlay reachability from branches](img/test-underlay-branches-to-hub-wan2-rabat.png)



[Underlay reachability from branches](img/test-underlay-branches-to-hub-wan1-tangier.png)



[Underlay reachability from branches](img/test-underlay-branches-to-hub-wan2-tangier.png)

### Test 8 - Branch internet access

* **Device:** Rabat end host
* **Command:** `ping 8.8.8.8`
* **Result:** Replies received through the branch SD-WAN and the ISP chain.

[Branch internet access](img/test-rabat-host-internet.png)

## References

* [*YT - BGP Deep Dive*](https://youtu.be/SVo6cDnQQm0)

