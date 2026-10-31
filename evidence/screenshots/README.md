# Evidence screenshots [ACTUAL]

Unedited copies of the student's original screenshots, renamed for clarity. "Original" is the
file name in the student's local capture folder (local time, IST), so each copy can be traced
back to its source. No screenshot has been cropped, redacted or altered.

## Public-safety flags

The "Before publishing" column lists what must be removed from a **public-safe copy** before
the repository is made public. The unedited files stay in the student's original collection.

- **Account ID:** the 12-digit AWS account ID appears in the console header (next to the
  account name), and in Owner ID and ARN fields. It must not be published.
- **Account name:** the console header shows the AWS account holder's name. Keep it only if it
  is the student's name and the student agrees to show it; otherwise remove it with the ID.
- **Browser tabs:** the tab bar shows a personal GitHub profile and unrelated tabs.
- **Home public IPv4:** the student's home IP appears as a security group source.
- **Local user / machine name:** WSL prompts show the Windows user and computer name. Low risk;
  remove if preferred.

## infrastructure/

| File | Shows | Original | Before publishing |
|---|---|---|---|
| `01-vpc-a-created.png` | ewb-vpc-a `vpc-051fb20bc75c4d6c4`, 10.10.0.0/16, us-east-1 | Screenshot 2026-10-31 021646 | Account ID (header, Owner ID); browser tabs |
| `02-subnet-a-public-details.png` | ewb-a-public `subnet-0f0b9ba3ccae32a07`, 10.10.1.0/24, us-east-1a, route table ewb-rt-a-public | Screenshot 2026-10-31 025510 | Account ID (header, ARN, Owner); browser tabs |
| `03-igw-a-attached.png` | ewb-igw-a `igw-0c0e6351e564526df` attached to VPC A | Screenshot 2026-10-31 025333 | Account ID (header, Owner); browser tabs |
| `04-rt-a-public-routes-association.png` | ewb-rt-a-public: 0.0.0.0/0 > igw, 10.10.0.0/16 local, 10.20.0.0/16 > pcx; associated with ewb-a-public | Screenshot 2026-10-31 025227 | Account ID (header, Owner ID); browser tabs |
| `05-rt-a-private-routes-association.png` | ewb-rt-a-private: 10.10.0.0/16 local, 10.20.0.0/16 > pcx (Active); associated with ewb-a-private | Screenshot 2026-10-31 025202 | Account ID (header, Owner ID) |
| `06-peering-request-pending.png` | ewb-a-b-peer `pcx-0f1730cc1e9523713` requested: requester VPC A, accepter VPC B, Pending acceptance | Screenshot 2026-10-31 022926 | Account ID (Owner IDs, ARN); partial header |
| `07-ec2-app-a-details-no-public-ip.png` | ewb-app-a `i-047cc151fe697b405`, 10.10.2.72, no public IPv4 (test 05) | Screenshot 2026-10-31 024232 | Account ID (header, ARN); browser tabs |
| `08-ec2-app-b-details-no-public-ip.png` | ewb-app-b `i-0a0bbfd5ce64d267e`, 10.20.1.75, no public IPv4 (test 05) | Screenshot 2026-10-31 024249 | Account ID (header, ARN); browser tabs |
| `09-bastion-elastic-ip-associated.png` | Elastic IP 52.54.130.241 associated with ewb-bastion (10.10.1.196) | Screenshot 2026-10-31 025805 | Account ID (header, owner account ID); browser tabs |
| `10-bastion-instance-connect-session.png` | Bastion shell through EC2 Instance Connect: `ip addr` and footer with `i-0e82bea59f7443a3f`, public 52.54.130.241, private 10.10.1.196 | Screenshot 2026-10-31 030217 | Account ID (header); browser tabs |

## security/

| File | Shows | Original | Before publishing |
|---|---|---|---|
| `01-sg-bastion-inbound-rules.png` | ewb-sg-bastion `sg-036a936bf7abdb647` at launch: SSH 22 from the student's public IPv4 /32 | Screenshot 2026-10-31 024547 | **Home public IPv4** (Source column); Account ID (Owner ID) |
| `02-sg-app-a-inbound-rules.png` | ewb-sg-app-a `sg-049ac207ec5382f6c`: SSH from ewb-sg-bastion, All ICMP from 10.20.1.75/32 | Screenshot 2026-10-31 041143 | Account ID (header, Owner); browser tabs |
| `03-sg-app-b-inbound-rules.png` | ewb-sg-app-b `sg-03cde590d8254cf1d`: All ICMP from 10.10.2.72/32, SSH from 10.10.1.0/24 | Screenshot 2026-10-31 041115 | Account ID (header, Owner); browser tabs |
| `04-bastion-private-key-removed.png` | On the bastion: `rm -f ~/ewb-lab-key-us.pem`, file no longer present | Screenshot 2026-10-31 035553 | None found |

## tests/

| File | Shows | Original | Before publishing |
|---|---|---|---|
| `test-02-03-ping-forward-and-reverse.png` | WSL: `ssh ewb-app-a "ping -c 4 10.20.1.75"` 0% loss (test 02) and `ssh ewb-app-b "ping -c 4 10.10.2.72"` 0% loss (test 03) | Screenshot 2026-10-31 040608 | Local user and machine name; local folder path |
| `test-04-wsl-proxyjump-ssh-app-b.png` | Test 04: WSL `ssh ewb-app-b` through ProxyJump; `hostname` and `ip addr show ens5` = 10.20.1.75 | Screenshot 2026-10-31 035721 | Local user and machine name |
| `test-04-bastion-ssh-app-b-hostname-ip.png` | Earlier session with the key on the bastion: bastion (10.10.1.196) > App-B, `hostname` `ip-10-20-1-75.ec2.internal`, `ens5` 10.20.1.75/24 | Screenshot 2026-10-31 034921 | None found |
| `test-f1-app-b-icmp-rule-removed.png` | F1: ewb-sg-app-b after the ICMP rule was removed, 1 inbound rule (SSH) | Screenshot 2026-10-31 042129 | Account ID (header, Owner); browser tabs |
| `test-f2-ping-blocked-after-rule-removal.png` | F2: App-B to App-A still 0% loss, then App-A to App-B 100% packet loss, saved as `ewb-f1.txt` | Screenshot 2026-10-31 042319 | Local user and machine name; local folder path |

## Not captured

Test 01 baseline; peering connection in Active state; VPC B, `ewb-b-private` and
`ewb-rt-b-private`; the final `ewb-sg-bastion` rules; F3 rule restore; F4 ping; cleanup.
