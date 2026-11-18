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

On **November 9, 2026**, Kali successfully pinged Ubuntu across the two VLANs with **0% packet loss**. I also checked the firewall rule's hit activity during the project.

Not every later test was consistent: I also saw occasional packet loss, an SSH timeout, and dropped packets in the opposite direction. Those issues are part of the troubleshooting work and **aren't being listed as fixed**.

## What I'll add later

When I power the equipment back on, I'll capture selected Juniper and Palo Alto configuration output, confirm the access-port assignments and VMware connections, and add screenshots of the policy and test results.

[Back to the project overview](README.md) · [View the network diagram](NETWORK_TOPOLOGY.md)
