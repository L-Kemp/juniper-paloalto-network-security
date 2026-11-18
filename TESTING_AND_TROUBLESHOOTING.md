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

I also checked the Palo Alto policy and its hit activity. Together, those checks showed that the intended Kali-to-Ubuntu ICMP path was working during that test.

**Verified result:** Kali (VLAN 110) → Ubuntu (VLAN 120), ICMP/ping, **4 of 4 replies**.

## What needed troubleshooting

Not every connection worked consistently in later tests:

| Check | What I observed | Status |
| --- | --- | --- |
| Kali → Ubuntu ping, November 9 | 4 of 4 replies; 0% packet loss | **Successful** |
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

## What I'll capture next

When I power the equipment back on, I'll save screenshots or sanitized command output for the successful ping, the firewall policy and logs, relevant switch settings, and follow-up troubleshooting. I'll retest the unresolved connections separately and update their status only after confirming the results.

[Back to the project overview](README.md) · [Configuration notes](CONFIGURATION_NOTES.md) · [Network diagram](NETWORK_TOPOLOGY.md)
