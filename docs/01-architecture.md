# 01 Architecture

Values are **[ACTUAL]** where the deployed environment differs from the workbook plan.
Diagram: `../diagrams/architecture.svg` · Resource IDs: `../inventory/resource-inventory.md`

## Components

| Component | Name tag | Placement | [PLANNED] address | [ACTUAL] address |
|---|---|---|---|---|
| VPC A | ewb-vpc-a | Region `us-east-1` | 10.10.0.0/16 | 10.10.0.0/16 |
| Public subnet A | ewb-a-public | VPC A | 10.10.1.0/24 | 10.10.1.0/24 |
| Private subnet A | ewb-a-private | VPC A | 10.10.2.0/24 | 10.10.2.0/24 |
| VPC B | ewb-vpc-b | Region `us-east-1` | 10.20.0.0/16 | 10.20.0.0/16 |
| Private subnet B | ewb-b-private | VPC B | 10.20.1.0/24 | 10.20.1.0/24 |
| Internet gateway | ewb-igw-a | Attached to VPC A only | n/a | n/a |
| Bastion | ewb-bastion | ewb-a-public | 10.10.1.10 + public IPv4 | 10.10.1.196 + Elastic IP 52.54.130.241 |
| App-A | ewb-app-a | ewb-a-private | 10.10.2.10, no public IP | 10.10.2.72, no public IP |
| App-B | ewb-app-b | ewb-b-private | 10.20.1.10, no public IP | 10.20.1.75, no public IP |
| Peering | ewb-a-b-peer | Requester VPC A, accepter VPC B | n/a | n/a |
| Key pair | ewb-lab | RSA, private key on laptop only | n/a | Deployed name: ewb-lab-key-us |

Tag on every resource: `Project = EWB-VPC-Peering`.

VPC B has no internet gateway. No NAT gateway is used.

## Why these CIDRs

10.10.0.0/16 and 10.20.0.0/16 do not overlap, which peering requires. Each /24 subnet sits
inside its own VPC range. Different subnet ranges alone would not fix overlapping VPC CIDRs.

## Traffic paths [ACTUAL addresses]

**Administration:** laptop (WSL) > bastion (public IPv4, TCP 22) > App-A or App-B using SSH
ProxyJump. The bastion opens a separate SSH connection to the destination, so App-B sees the
bastion's private IP (10.10.1.196) as the source.

**Forward path (App-A to App-B):**
App-A 10.10.2.72 > `ewb-rt-a-private` route 10.20.0.0/16 > pcx > VPC B > `ewb-sg-app-b`
allows All ICMP IPv4 from 10.10.2.72/32 > App-B 10.20.1.75.

**Return path (App-B to App-A):**
App-B 10.20.1.75 > `ewb-rt-b-private` route 10.10.0.0/16 > pcx > VPC A > App-A.
Security groups are stateful, so replies to allowed traffic return automatically, but the
route back must exist in VPC B's route table.

**Bastion to App-B:** `ewb-rt-a-public` needs 10.20.0.0/16 > pcx. The more specific /16 route
wins over 0.0.0.0/0 > igw.

**Not provided:** peering does not give VPC B internet access through VPC A's IGW. Peering is
not transitive.
