# Juniper + Palo Alto Network Security

This project is about putting real networking equipment to work alongside virtual machines. I connected a **Juniper EX2300-48P switch** and a **Palo Alto PA-820 firewall** to a VMware environment to practice setting up separate networks, controlling traffic between them, and figuring out what happens when a connection doesn't work as expected.

The goal was simple: build a small, segmented network and test communication between VLANs using a specific firewall rule.

## What I set up

- **Juniper EX2300-48P:** Configured separate VLANs and the connection to the firewall.
- **Palo Alto PA-820:** Set up the network gateways and firewall rules that control traffic between VLANs.
- **VMware Workstation:** Used Kali Linux and Ubuntu Server virtual machines to test connectivity.

I split the network into three areas:

| VLAN | Purpose | Network |
| --- | --- | --- |
| 100 | Management | `10.100.100.0/24` |
| 110 | Clients (Kali Linux) | `10.100.110.0/24` |
| 120 | Servers (Ubuntu Server) | `10.100.120.0/24` |

The Juniper switch and Palo Alto firewall are connected through a trunk link that carries these VLANs. Kali Linux uses `10.100.110.20`, and Ubuntu Server uses `10.100.120.20`.

## What worked

I created and checked a Palo Alto rule named `ALLOW-KALI-TO-UBUNTU-ICMP` to allow a specific ping test from Kali to Ubuntu.

**On November 9, 2026, the ping test succeeded with 0% packet loss.** That confirmed connectivity between two devices on different VLANs with the intended firewall rule in place. I also checked the firewall policy and its hit activity.

## Troubleshooting along the way

Not every test worked the first time. I checked switch ports, VLAN settings, firewall policies, and packet captures to narrow down connection issues.

A later SSH attempt timed out, and follow-up testing showed occasional connectivity problems and some dropped packets in the reverse direction. Those findings are **still being investigated**, so I'm documenting them rather than presenting them as resolved.


## Network diagram

[View my network topology and setup notes](NETWORK_TOPOLOGY.md)


## Configuration notes

[View my Juniper and Palo Alto configuration notes](CONFIGURATION_NOTES.md)


## Testing and troubleshooting

[View my connectivity tests and troubleshooting notes](TESTING_AND_TROUBLESHOOTING.md)

## Configuration and test screenshots

- [Juniper VLAN and trunk configuration](juniper-vlans-trunk-configuration.png)
- [Successful Kali-to-Ubuntu connectivity test](kali-to-ubuntu-inter-vlan-ping.png)
- [Palo Alto firewall rule usage](paloalto-security-rule-usage.png)
- [Palo Alto VLAN interfaces and security zones](paloalto-vlan-interfaces.png)
- [Palo Alto ICMP security policy](paloalto-icmp-security-rule.png)



## What's coming next

I'll continue documenting configuration details, troubleshooting, and additional tests as I expand the network.


## What this project shows

Practical experience with **managed switching, VLANs, Palo Alto firewall policies, VMware networking, Linux connectivity tests, and network troubleshooting** using physical equipment.

*This is a personal network-security project, not a production deployment.*
