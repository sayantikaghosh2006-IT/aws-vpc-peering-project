# 03 Test Plan

The **[EXPECTED]** column holds workbook acceptance criteria. They are not results.

Fill **[ACTUAL]** only from real terminal output saved in `../evidence/terminal-output/`.
Evidence file names below are in `../evidence/terminal-output/`.

The pass/fail summary lives in `../evidence/test-results.md`.

Before each ping, identify the host inside each private instance:

    hostname
    hostname -I
    ping -c 4 PEER_PRIVATE_IP

If the first run loses a packet while the host becomes ready, wait briefly and repeat the four-packet test. Every test must use private destination IPs.

## Actual addresses [ACTUAL]

The deployed instances use these AWS-assigned IPs:

- Bastion: `10.10.1.196`
- App-A: `10.10.2.72`
- App-B: `10.20.1.75`

For controlled-failure tests F1 and F3, the App-B ICMP rule source is `10.10.2.72/32`.

| Test | When / where | Command | [EXPECTED] | Evidence file |
|---|---|---|---|---|
| 01 Baseline | App-A, before peering and routes | `ping -c 4 10.20.1.75` | No replies | Not recorded |
| 02 Forward path | App-A, after routes | `ping -c 4 10.20.1.75` | 4 replies, 0% packet loss | `ewb-test-03.txt` (file name swapped, see test results note 1) |
| 03 Reverse path | App-B, after routes | `ping -c 4 10.10.2.72` | 4 replies, 0% packet loss | `ewb-test-02.txt` (file name swapped, see test results note 1) |
| 04 Private SSH | Laptop (WSL) | `ssh ewb-app-b` | Shell on App-B through bastion | `ewb-test-04.txt` |
| 05 Isolation | EC2 console, instance details | App-A and App-B | No public IPv4 on either host | `../evidence/screenshots/infrastructure/07-ec2-app-a-details-no-public-ip.png`, `08-ec2-app-b-details-no-public-ip.png` |

## Controlled failure

| Step | Action | [EXPECTED] | Evidence file |
|---|---|---|---|
| F1 | Remove only the App-B inbound ICMP rule from `10.10.2.72/32` in `ewb-sg-app-b`. Keep SSH. | n/a | `../evidence/screenshots/tests/test-f1-app-b-icmp-rule-removed.png` |
| F2 | From App-A: `ping -c 4 10.20.1.75` | Replies stop | `ewb-f1.txt`; `../evidence/screenshots/tests/test-f2-ping-blocked-after-rule-removal.png` |
| F3 | Restore the rule with source `10.10.2.72/32`. The workbook specifies Custom ICMP IPv4 Echo request; this run recorded All ICMP IPv4. | n/a | No screenshot recorded |
| F4 | From App-A: `ping -c 4 10.20.1.75` | Replies resume | `ewb-f4.txt` |

## Short explanation after testing [ACTUAL]

- Test 01: not recorded. The baseline ping was not captured before the peering routes were added, so no result is claimed. Expected behaviour: without a route to 10.20.0.0/16 via the peering connection, App-A cannot reach App-B.
- Why tests 02 and 03 succeeded: After configuring VPC peering, route tables, and the required security group rules, both instances successfully exchanged ICMP packets through their private IP addresses.
- Why F2 failed and F4 recovered: Removing the ICMP inbound rule from App-B blocked ping requests from App-A. Restoring the rule allowed communication again, with 4 packets received and 0% packet loss.

**Controlled failure results:**
- F2: 4 packets transmitted, 0 received, 100% packet loss.
- F4: 4 packets transmitted, 4 received, 0% packet loss.
