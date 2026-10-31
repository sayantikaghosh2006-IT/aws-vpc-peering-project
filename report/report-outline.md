# Report Outline

Final file: `Sayantika_Ghosh_EWB_VPC_Peering.pdf`, saved in this folder.
Label every screenshot with host, test and result. Include only your own execution results.
Leave private keys, passwords and access tokens out of the report.

Each section maps to the workbook submission checklist (page 13) and marks.

## 1. Cover  (checklist item 1)
- Name: Sayantika Ghosh · Batch: Btech
- Project title: AWS VPC Peering
- Region: `us-east-1` · Completion date: 30-10-2026

## 2. Architecture and inventory  (item 2 · Architecture, CIDRs and inventory: 15 marks)
- Diagram from `../diagrams/architecture.svg` (deployed values)
- Inventory table from `../inventory/resource-inventory.md` [ACTUAL] columns
- Why 10.10.0.0/16 and 10.20.0.0/16 (non-overlapping)

## 3. Subnets, IGW and route tables  (item 3 · 20 marks)
- Subnet CIDRs
- All three route tables with routes and explicit subnet associations
- Screenshots: `../evidence/screenshots/infrastructure/` 01 to 05

## 4. Peering and routes  (item 4 · Peering plus forward and return routes: 20 marks)
- Active peering: name, status, requester and accepter VPC IDs (not captured: only `../evidence/screenshots/infrastructure/06-peering-request-pending.png` exists)
- Forward path and return path explanation (from `../docs/01-architecture.md`)

## 5. Security groups and private SSH  (items 4 and 5 · 20 marks)
- Inbound rules for each SG (`../evidence/screenshots/security/` 01 to 03)
- Test 04: `ssh ewb-app-b` through the bastion
- Test 05: no public IPv4 on App-A or App-B

## 6. Test evidence  (items 5 and 6 · Positive and negative evidence: 15 marks)
- Test 01 baseline failure (not recorded: state this in the report)
- Tests 02 and 03 forward and reverse ping success
- Controlled ICMP failure (F2) and recovery (F4) with a short explanation
- Source: `../evidence/test-results.md` and `../evidence/terminal-output/`

## 7. Cleanup and learning  (item 7 · Report clarity, viva and cleanup: 10 marks)
- Cleanup evidence from `../cleanup/screenshots/` (after cleanup)
- Short note on what you learned: Through this project, I gained practical experience in creating and connecting two AWS VPCs using VPC Peering. I learned how to configure subnets, route tables, internet gateways and security groups, and how each component contributes to private network communication. Testing connectivity in both directions helped me understand the importance of forward and return routes. I also experienced how a security group rule can affect connectivity through the controlled ICMP failure and recovery test. Setting up SSH access through a bastion host using ProxyJump gave me a better understanding of securely accessing private EC2 instances without assigning them public IP addresses. Overall, this project helped me connect the networking concepts learned in theory with their practical implementation on AWS.

## Pre-submission check
- [ ] Both private hosts have no public IP
- [ ] Every test uses private destination IPs
- [ ] Report shows only your own execution results
- [ ] No keys, passwords or tokens anywhere in the PDF
- [ ] Every screenshot labelled with host, test and result
