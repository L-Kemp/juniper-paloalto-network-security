# Switch and Firewall Configuration Notes

Here's how I set up the Juniper switch and Palo Alto firewall for this project. These are the settings I recorded and tested, **not a complete backup of either device's configuration**.

## Keeping the networks separate

I divided the network into three VLANs so I could manage the equipment separately and control traffic between client and server machines.

| VLAN | Name | Network | Gateway |
| --- | --- | --- | --- |
| 100 | `LAB-MGMT` | `10.100.100.0/24` | `10.100.100.1` |
| 110 | `LAB-CLIENTS` | `10.100.110.0/24` | `10.100.110.1` |
| 120 | `LAB-SERVERS` | `10.100.120.0/24` | `10.100.120.1` |

The **Juniper EX2300-48P** handles the VLANs. Its `ge-0/0/0` trunk connects to `ethernet1/2` on the **Palo Alto PA-820** and carries all three VLANs. The firewall uses tagged subinterfaces (`ethernet1/2.100`, `.110`, and `.120`) as their gateways.

For testing, I used these VMware virtual machines:

- **Kali Linux:** `10.100.110.20` on VLAN 110
- **Ubuntu Server:** `10.100.120.20` on VLAN 120

## The firewall rule I tested

I created a Palo Alto rule called **`ALLOW-KALI-TO-UBUNTU-ICMP`**. It was meant to allow a specific ping test between the two virtual machines, rather than opening up all traffic between the VLANs.

| Setting | Value |
| --- | --- |
| From | `LAB-CLIENTS` |
| To | `LAB-SERVERS` |
| Source | `10.100.110.20` |
| Destination | `10.100.120.20` |
| Application | `ping` |
| Service | `application-default` |
| Action | Allow |
| Logging | At session end |

## What I confirmed

On **November 9, 2026**, Kali successfully pinged Ubuntu across VLANs 110 and 120 with **0% packet loss**. I repeated the test on **November 18, 2026**, with another 4 of 4 replies and 0% loss. The Palo Alto rule had **265 cumulative hits** when I checked it on November 18.

I also verified these Juniper switch ports without changing their configurations:

| Port | Mode | VLANs / use |
| --- | --- | --- |
| `ge-0/0/0` | Trunk | VLANs 100, 110, 120 — Palo Alto connection |
| `ge-0/0/1` | Trunk | VLANs 100, 110, 120 — native VLAN 100 |
| `ge-0/0/2` | Access | VLAN 110 — clients |
| `ge-0/0/3` | Access | VLAN 120 — servers |

For the VMware side, I used **VMnet5 bridged to the USB Ethernet adapter**, with Kali on `eth2.110` and Ubuntu on `ens38.120`. I added temporary routes through the Palo Alto gateways for the inter-VLAN test while keeping the existing NAT connections.

Not every earlier test was consistent: I also saw occasional packet loss, an SSH timeout, and dropped packets in the opposite direction. Those issues are part of the troubleshooting work and **aren't being listed as fixed**.

## Evidence already added

The repository now includes screenshots of the [Juniper VLAN and trunk configuration](juniper-vlans-trunk-configuration.png), [Palo Alto VLAN interfaces](paloalto-vlan-interfaces.png), [ICMP security rule](paloalto-icmp-security-rule.png), [rule usage](paloalto-security-rule-usage.png), and [successful Kali-to-Ubuntu ping](kali-to-ubuntu-inter-vlan-ping.png).

I'll continue adding details as I investigate the remaining connectivity issues.

[Back to the project overview](README.md) · [View the network diagram](NETWORK_TOPOLOGY.md)
