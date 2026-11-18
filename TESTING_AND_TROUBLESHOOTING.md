# Connectivity Testing and Troubleshooting

Once the Juniper switch, Palo Alto firewall, and VMware virtual machines were connected, I tested whether traffic could move between the client and server VLANs. I also used the tests that didn't work to narrow down where the problem might be.

## The test setup

- **Kali Linux:** `10.100.110.20` on VLAN 110 (`LAB-CLIENTS`)
- **Ubuntu Server:** `10.100.120.20` on VLAN 120 (`LAB-SERVERS`)
- **Firewall:** Palo Alto PA-820, connected to the Juniper EX2300-48P through a VLAN trunk
- **Rule being tested:** `ALLOW-KALI-TO-UBUNTU-ICMP`, allowing ping from the Kali address to the Ubuntu address

The idea was to allow this one type of traffic through the firewall and confirm that two separate VLANs could communicate as intended.

## What worked

On **November 9, 2026**, I sent four ping requests from Kali to Ubuntu. All four received replies: **0% packet loss**.

I repeated the test on **November 18, 2026**, and again received **4 of 4 replies with 0% packet loss**. Before testing, I confirmed Kali and Ubuntu could each reach their respective Palo Alto VLAN gateways. I also checked the routes to make sure traffic between the two VLANs went through the firewall instead of VMware's NAT gateway.

The Palo Alto ICMP security rule showed **265 cumulative hits** when checked on November 18, with its last hit recorded that day. The counter includes traffic beyond the individual four-packet tests.

**Verified result:** Kali (`10.100.110.20`, VLAN 110) → Ubuntu (`10.100.120.20`, VLAN 120), ICMP/ping, **4 of 4 replies on both November 9 and November 18**.

## What needed troubleshooting

Not every connection worked consistently in later tests:

| Check | What I observed | Status |
| --- | --- | --- |
| Kali → Ubuntu ping, November 9 | 4 of 4 replies; 0% packet loss | **Successful** |
| Kali → Ubuntu ping, November 18 | 4 of 4 replies; 0% packet loss | **Successful retest** |
| Follow-up ping tests | Some runs returned 4 of 4 replies; another returned 3 of 4 | **Intermittent loss observed** |
| SSH from Kali to Ubuntu | Connection timed out, although Ubuntu's SSH service was running and listening on port 22 | **Not resolved** |
| Reverse-direction packet capture | Palo Alto capture counters showed four packets at both the receive and drop stages for the filtered traffic | **Cause not confirmed** |

A successful ping doesn't mean every service or traffic direction is working. The SSH timeout and the reverse-direction capture still need their own checks.

## How I investigated

I worked through the setup instead of immediately changing firewall rules:

1. Checked the VLAN setup and switch-to-firewall connection.
2. Reviewed the Palo Alto security rule, addresses, zones, and policy hit activity.
3. Repeated ping tests to compare successful and unsuccessful results.
4. Checked that the SSH service was running on Ubuntu.
5. Used Palo Alto packet-capture diagnostics to examine the traffic that wasn't behaving as expected.

The packet captures helped identify where to investigate next, but **I haven't confirmed a final root cause** for the later connection issues.

## Evidence captured

I've added the [successful inter-VLAN ping screenshot](kali-to-ubuntu-inter-vlan-ping.png), [Juniper VLAN and trunk output](juniper-vlans-trunk-configuration.png), [Palo Alto interfaces](paloalto-vlan-interfaces.png), [ICMP security rule](paloalto-icmp-security-rule.png), and [rule usage](paloalto-security-rule-usage.png).

The November 18 retest confirms the ping path worked again. It doesn't by itself resolve the earlier SSH timeout or explain the reverse-direction packet drops, so those remain open for follow-up testing.

[Back to the project overview](README.md) · [Configuration notes](CONFIGURATION_NOTES.md) · [Network diagram](NETWORK_TOPOLOGY.md)
