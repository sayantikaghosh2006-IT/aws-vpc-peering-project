# Resource Inventory

**[PLANNED]** values represent the original workbook design. **[ACTUAL]** values represent the deployed AWS environment in US East (N. Virginia).

AWS account: Not published (redacted for the public repository)  
Region: `us-east-1`  
Availability Zones: `us-east-1a` (ewb-a-public, ewb-a-private), `us-east-1b` (ewb-b-private / App-B), per the EC2 instance list  
Project tag: `EWB-VPC-Peering`

## Network

| Name tag | Type | [PLANNED] CIDR / placement | [ACTUAL] ID | [ACTUAL] CIDR |
|---|---|---|---|---|
| ewb-vpc-a | VPC | 10.10.0.0/16 | vpc-051fb20bc75c4d6c4 | 10.10.0.0/16 |
| ewb-a-public | Subnet | VPC A, 10.10.1.0/24 | subnet-0f0b9ba3ccae32a07 | 10.10.1.0/24 |
| ewb-a-private | Subnet | VPC A, 10.10.2.0/24 | subnet-0f6af2b86cf58dc0c | 10.10.2.0/24 |
| ewb-vpc-b | VPC | 10.20.0.0/16 | vpc-060887d515a935fba | 10.20.0.0/16 |
| ewb-b-private | Subnet | VPC B, 10.20.1.0/24 | subnet-02a6749cbddebed29 | 10.20.1.0/24 |
| ewb-igw-a | Internet gateway | Attached to VPC A | igw-0c0e6351e564526df | n/a |
| ewb-a-b-peer | Peering connection | Requester A, accepter B | pcx-0f1730cc1e9523713 | n/a |

## Route tables

| Name tag | [PLANNED] association | [PLANNED] routes | [ACTUAL] ID | [ACTUAL] association verified |
|---|---|---|---|---|
| ewb-rt-a-public | ewb-a-public | 10.10.0.0/16 local; 0.0.0.0/0 igw; 10.20.0.0/16 pcx | rtb-0d4f943ab6ceaf4e6 | Yes (`../evidence/screenshots/infrastructure/04-rt-a-public-routes-association.png`) |
| ewb-rt-a-private | ewb-a-private | 10.10.0.0/16 local; 10.20.0.0/16 pcx | rtb-00d4303fa8a4a5ea8 | Yes (`../evidence/screenshots/infrastructure/05-rt-a-private-routes-association.png`) |
| ewb-rt-b-private | ewb-b-private | 10.20.0.0/16 local; 10.10.0.0/16 pcx | Pending verification (no us-east-1 screenshot) | Pending verification |

## Security groups

| Name tag | VPC | [PLANNED] inbound rules | [ACTUAL] ID |
|---|---|---|---|
| ewb-sg-bastion | A | SSH 22 from your public IPv4 /32 | sg-036a936bf7abdb647 (`../evidence/screenshots/security/01-sg-bastion-inbound-rules.png`) |
| ewb-sg-app-a | A | SSH 22 from bastion; ICMP Echo from VPC B app | sg-049ac207ec5382f6c (`../evidence/screenshots/security/02-sg-app-a-inbound-rules.png`) |
| ewb-sg-app-b | B | SSH 22 from bastion; ICMP Echo from VPC A app | sg-03cde590d8254cf1d (`../evidence/screenshots/security/03-sg-app-b-inbound-rules.png`) |

## Compute

| Name tag | Subnet | [PLANNED] private IP | [PLANNED] public IP | [ACTUAL] instance ID | [ACTUAL] private IP | [ACTUAL] public IPv4 |
|---|---|---|---|---|---|---|
| ewb-bastion | ewb-a-public | 10.10.1.10 | Yes | i-0e82bea59f7443a3f | 10.10.1.196 | 52.54.130.241 |
| ewb-app-a | ewb-a-private | 10.10.2.10 | None | i-047cc151fe697b405 | 10.10.2.72 | None |
| ewb-app-b | ewb-b-private | 10.20.1.10 | None | i-0a0bbfd5ce64d267e | 10.20.1.75 | None |

AMI: Amazon Linux 2023  
Instance type: `t3.micro` (all three instances)  
Key pair: `ewb-lab-key-us`  
Private key: Stored outside the project folder and never committed. During early testing a copy was placed on the bastion (see Deviations). It was then deleted from the bastion with `rm -f ~/ewb-lab-key-us.pem` (`../evidence/screenshots/security/04-bastion-private-key-removed.png`).  
Bastion public IPv4 `52.54.130.241` is an Elastic IP associated with the bastion (`../evidence/screenshots/infrastructure/09-bastion-elastic-ip-associated.png`). It must be released during cleanup. Bastion instance ID and both IPs also appear in `../evidence/screenshots/infrastructure/10-bastion-instance-connect-session.png`.

## Actual addresses for security group sources and tests [ACTUAL]

The instances use AWS-assigned private IPs, not the planned ones. The table shows the SG sources
as they appear in the console screenshots in `../evidence/screenshots/security/`.

| Rule | Workbook source | [ACTUAL] source in console | Note |
|---|---|---|---|
| ewb-sg-app-a SSH 22 | 10.10.1.10/32 (bastion) | Security group `sg-036a936bf7abdb647` (ewb-sg-bastion) | Equivalent scope: only the bastion's interface |
| ewb-sg-app-a ICMP | Echo request from 10.20.1.10/32 | All ICMP - IPv4 from 10.20.1.75/32 | Broader ICMP type than the workbook |
| ewb-sg-app-b SSH 22 | 10.10.1.10/32 (bastion) | 10.10.1.0/24 (whole public subnet A) | Broader than the workbook's /32 |
| ewb-sg-app-b ICMP | Echo request from 10.10.2.10/32 | All ICMP - IPv4 from 10.10.2.72/32 | Broader ICMP type than the workbook |
| ewb-sg-bastion SSH 22 | Student public IPv4 /32 | Student public IPv4 /32 (value not published in the text) | Captured at launch. The group was edited at about 03:01 and 04:01 IST (success banners only); the final rule set was not captured |

## Deviations from the workbook [ACTUAL]

| Item | Workbook | Actual |
|---|---|---|
| Region | ap-south-1 (example) | us-east-1. A first attempt in ap-south-1 was abandoned because the new account could launch EC2 only in US Regions at the time |
| Private IPs | 10.10.1.10, 10.10.2.10, 10.20.1.10 | 10.10.1.196, 10.10.2.72, 10.20.1.75 (AWS-assigned) |
| Key pair name | ewb-lab | ewb-lab-key-us |
| Laptop to bastion | SSH with local key | EC2 Instance Connect (browser) for the first checks; later SSH from WSL with the local key (test 04) |
| Bastion to App-A / App-B | SSH ProxyJump from the laptop; key never placed on an instance | First run: SSH from the bastion with the key copied to the bastion (`../evidence/screenshots/tests/test-04-bastion-ssh-app-b-hostname-ip.png`). The key was then deleted from the bastion, and test 04 was repeated from WSL with ProxyJump (`../evidence/screenshots/tests/test-04-wsl-proxyjump-ssh-app-b.png`). |

## Connectivity verification

| Test | Result |
|---|---|
| Laptop to bastion using EC2 Instance Connect | Successful |
| Bastion to App A using SSH | Successful |
| Bastion to App B using SSH across VPC peering | Successful |
| WSL laptop to App B through the bastion (test 04) | Successful |

Evidence: `../evidence/screenshots/tests/test-04-bastion-ssh-app-b-hostname-ip.png` shows the App-B shell
(`ip-10-20-1-75.ec2.internal`, `ens5` `10.20.1.75/24`) reached from the bastion (`10.10.1.196`).
Ping tests 02 and 03 and the controlled failure are recorded in `../evidence/test-results.md`. Test 01 (baseline) was not recorded.

## Cleanup tracking

Deletion status for each resource is tracked in `../cleanup/cleanup-checklist.md`.

**Note:** Pending verification entries must be completed from the AWS console. Do not treat unverified route-table or security-group IDs as confirmed.
