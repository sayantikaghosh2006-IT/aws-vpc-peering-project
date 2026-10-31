# 04 Troubleshooting

Check in this order: instance state, destination IP, subnet route table, security rule.
(Reference only, from workbook page 12.)

| Symptom | What to inspect |
|---|---|
| Bastion SSH times out | Bastion's current public IP; your current public IP /32 in the SSH rule; public subnet association; 0.0.0.0/0 route to the attached IGW |
| App-A SSH fails | App-A private IP; TCP 22 allowed from the bastion (workbook: bastion private IP /32; deployed: the ewb-sg-bastion security group); correct key, ec2-user, running instance |
| App-B SSH fails | Bastion public subnet needs 10.20.0.0/16 to pcx; VPC B needs the return route; App-B SSH source is the bastion private IP |
| Ping fails after acceptance | Both private subnet route tables; correct pcx ID and Active state; Echo request rule on the destination; actual host IPs |
| Peering creation fails | Every CIDR associated with both VPCs for overlap, including secondary CIDRs |
| Permission denied (publickey) | Correct key pair, local IdentityFile and ec2-user. Routing works if you reached SSH authentication |
| Failure with custom ACLs | Both directions, ICMP rules and SSH return ephemeral ports. This lab assumes default allow-all ACLs |

Private instances have no internet egress. A failed package update there does not mean
peering is broken.

## Issues met during the lab [ACTUAL]

| When (IST) | Symptom | Cause found | Fix |
|---|---|---|---|
| 30 to 31 Oct | EC2 instances in `ap-south-1` could not be launched and started as planned | AWS Support confirmed the new account could launch EC2 only in US Regions at the time | Rebuilt the whole lab in `us-east-1` and abandoned the `ap-south-1` attempt |
| 31 Oct, about 03:00 | SSH from the bastion to App-A failed with `Permission denied (publickey)` | The private key was not on the host making the connection | The key was copied to the bastion as a shortcut (against the workbook's guidance). It was later deleted, and SSH was done from WSL with ProxyJump |
| 31 Oct, about 03:25 | `scp` of the key from Windows PowerShell failed with `UNPROTECTED PRIVATE KEY FILE` | The key file's Windows permissions were too open | Restricted the file permissions with `icacls`; in WSL the key was copied to `~/.ssh/` and set to `chmod 400` |
| 31 Oct, about 04:00 | Forward ping App-A to App-B: 100% packet loss after peering | `ewb-sg-app-b` had only the SSH rule, no ICMP rule | Added All ICMP IPv4 from `10.10.2.72/32`; the rerun gave 0% loss (test 02) |
