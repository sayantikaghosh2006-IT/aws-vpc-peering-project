# 02 Build Runbook (AWS console)

Steps follow workbook pages 5 to 10, in the order they were performed. Each step shows the
workbook value **[PLANNED]** and, where it differs, the deployed value **[ACTUAL]**. Resource
IDs are in `../inventory/resource-inventory.md`. Screenshots are in `../evidence/screenshots/`.

Region for every step: `us-east-1`. One account, one Region.
Tags: `Project = EWB-VPC-Peering`, `Student = Sayantika Ghosh`.

## A. VPC A (workbook page 5)

1. Your VPCs > Create VPC > **VPC only**: `ewb-vpc-a`, `10.10.0.0/16`, no IPv6, tenancy
   Default. Evidence: `infrastructure/01-vpc-a-created.png`.
2. Subnets in VPC A: `ewb-a-public` `10.10.1.0/24` and `ewb-a-private` `10.10.2.0/24`, both in
   `us-east-1a`. Evidence: `infrastructure/02-subnet-a-public-details.png`.
3. Create `ewb-igw-a` and attach it to `ewb-vpc-a`. Evidence: `infrastructure/03-igw-a-attached.png`.
4. `ewb-rt-a-public`: explicitly associated with `ewb-a-public`; route `0.0.0.0/0` >
   `ewb-igw-a`; local route kept.
5. `ewb-rt-a-private`: explicitly associated with `ewb-a-private`; local route only at this
   stage; auto-assign public IPv4 disabled on the private subnet.

Checkpoint 01 (associations and public default route): covered by
`infrastructure/04-rt-a-public-routes-association.png` and
`infrastructure/05-rt-a-private-routes-association.png`, which were captured after the
peering routes were added (step G).

## B. VPC B (workbook page 6)

- `ewb-vpc-b` `10.20.0.0/16`; `ewb-b-private` `10.20.1.0/24` (App-B ran in `us-east-1b`),
  auto-assign public IPv4 disabled.
- `ewb-rt-b-private` explicitly associated with `ewb-b-private`, local route kept.
- No internet gateway for VPC B.

No screenshots of VPC B, its subnet or its route table were captured.

## C. Security groups (workbook page 6), inbound rules

Default outbound allow-all and default network ACLs were kept.

| Security group | Type / port | [PLANNED] source | [ACTUAL] source |
|---|---|---|---|
| ewb-sg-bastion | SSH / TCP 22 | Your public IPv4 /32 | Student public IPv4 /32 (later edited, final rules not captured) |
| ewb-sg-app-a | SSH / TCP 22 | 10.10.1.10/32 (bastion) | Security group `ewb-sg-bastion` |
| ewb-sg-app-a | ICMP | Echo request from 10.20.1.10/32 (App-B) | All ICMP IPv4 from 10.20.1.75/32 |
| ewb-sg-app-b | SSH / TCP 22 | 10.10.1.10/32 (bastion) | 10.10.1.0/24 (public subnet A) |
| ewb-sg-app-b | ICMP | Echo request from 10.10.2.10/32 (App-A) | All ICMP IPv4 from 10.10.2.72/32 (added after the first ping failed) |

Evidence: `security/01-sg-bastion-inbound-rules.png`, `security/02-sg-app-a-inbound-rules.png`,
`security/03-sg-app-b-inbound-rules.png`.

## D. Key pair and instances (workbook page 7)

- RSA key pair `ewb-lab-key-us` (workbook: `ewb-lab`). The private key is kept on the laptop
  in WSL at `~/.ssh/ewb-lab-key-us.pem` with `chmod 400`, outside this project folder.
- Amazon Linux 2023, `t3.micro`. The bastion was launched with auto-assign public IPv4
  enabled and then given an Elastic IP.

| Instance | Subnet | [PLANNED] private IPv4 | [ACTUAL] private IPv4 | Public IPv4 | SG |
|---|---|---|---|---|---|
| ewb-bastion | ewb-a-public | 10.10.1.10 | 10.10.1.196 | Elastic IP 52.54.130.241 | ewb-sg-bastion |
| ewb-app-a | ewb-a-private | 10.10.2.10 | 10.10.2.72 | None | ewb-sg-app-a |
| ewb-app-b | ewb-b-private | 10.20.1.10 | 10.20.1.75 | None | ewb-sg-app-b |

AWS-assigned private IPs were used, so every IP and security group source in this project
uses the actual values. Checkpoint 02 evidence: `infrastructure/07`, `08`, `09` and `10`.

## E. SSH administration (workbook page 8)

- The entries from `../config/ssh-config.example` were written to `~/.ssh/config` in WSL,
  with the bastion's public IPv4 in place of `BASTION_PUBLIC_IP`.
- `ssh ewb-app-a` and `ssh ewb-app-b` (both through ProxyJump) were run from WSL for tests 02
  to 04 and the controlled failure.
- Test 01 baseline (`ping -c 4 10.20.1.75` from App-A before peering) was **not recorded**.

## F. Peering (workbook page 9)

- Peering connection `ewb-a-b-peer`: requester `ewb-vpc-a`, My account, This Region,
  accepter `ewb-vpc-b`. Evidence of the request: `infrastructure/06-peering-request-pending.png`.
- The request was accepted. The pcx routes show as Active in
  `infrastructure/05-rt-a-private-routes-association.png`. A Checkpoint 03 screenshot of the
  connection in Active state was not captured.

## G. Routes (workbook page 10)

| Route table | Added destination | Target | Evidence |
|---|---|---|---|
| ewb-rt-a-private | 10.20.0.0/16 | pcx-0f1730cc1e9523713 | `infrastructure/05` |
| ewb-rt-b-private | 10.10.0.0/16 | pcx-0f1730cc1e9523713 | Not captured; required for tests 02 and 03 to succeed |
| ewb-rt-a-public | 10.20.0.0/16 | pcx-0f1730cc1e9523713 | `infrastructure/04` |

Next: the tests in `03-test-plan.md`, with results in `../evidence/test-results.md`.
