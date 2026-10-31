# Cleanup Checklist

Workbook page 14. **Save all evidence first.** Remove only the resources created for this project. Follow the order below. Save cleanup screenshots in `screenshots/` in this folder.

Region: `us-east-1` · Tag filter: `Project = EWB-VPC-Peering`

## 0. Before cleanup
- [ ] All screenshots saved in `../evidence/screenshots/`
- [ ] All terminal output saved in `../evidence/terminal-output/`
- [ ] `../inventory/resource-inventory.md` [ACTUAL] columns complete
- [ ] Final evidence saved in the repository (no live demonstration)

## 1. Remove compute and credentials
- [ ] Terminate `ewb-bastion` — `i-0e82bea59f7443a3f`
- [ ] Terminate `ewb-app-a` — `i-047cc151fe697b405`
- [ ] Terminate `ewb-app-b` — `i-0a0bbfd5ce64d267e`
- [ ] Confirm their lab EBS volumes are deleted
- [ ] Delete any leftover lab volumes or snapshots
- [ ] Release Elastic IP `52.54.130.241` (allocation `eipalloc-064bf5f4dc2f2ba11`, associated with the bastion; see `../evidence/screenshots/infrastructure/09-bastion-elastic-ip-associated.png`)
- [ ] Delete the `ewb-lab-key-us` key pair in EC2 if no longer needed
- [ ] Delete the local PEM key if no longer needed, and remove any project-specific SSH configuration entries

## 2. Remove peering and network dependencies
- [ ] Delete the peer route `10.20.0.0/16` in `ewb-rt-a-private`
- [ ] Delete the peer route `10.10.0.0/16` in `ewb-rt-b-private`
- [ ] Delete the peer route `10.20.0.0/16` in `ewb-rt-a-public`
- [ ] Delete peering connection `ewb-a-b-peer` — `pcx-0f1730cc1e9523713`
- [ ] After network interfaces disappear, delete:
  - `ewb-sg-bastion` — `sg-036a936bf7abdb647`
  - `ewb-sg-app-a` — `sg-049ac207ec5382f6c`
  - `ewb-sg-app-b` — `sg-03cde590d8254cf1d`
- [ ] Remove subnet associations as required
- [ ] Delete subnets:
  - `ewb-a-public` — `subnet-0f0b9ba3ccae32a07`
  - `ewb-a-private` — `subnet-0f6af2b86cf58dc0c`
  - `ewb-b-private` — `subnet-02a6749cbddebed29`
- [ ] Delete custom route tables:
  - `ewb-rt-a-public` — `rtb-0d4f943ab6ceaf4e6`
  - `ewb-rt-a-private` — `rtb-00d4303fa8a4a5ea8`
  - `ewb-rt-b-private` — ID pending verification
- [ ] Detach and delete `ewb-igw-a` — `igw-0c0e6351e564526df`
- [ ] Delete `ewb-vpc-a` — `vpc-051fb20bc75c4d6c4`
- [ ] Delete `ewb-vpc-b` — `vpc-060887d515a935fba`

## 3. Verify the account is clear
- [ ] Filter by project tag and check EC2 instances, volumes and snapshots
- [ ] Check public IPv4 / Elastic IP allocations
- [ ] Check VPC resources (VPCs, subnets, route tables, IGWs, peering, SGs) in `us-east-1`
- [ ] Check Billing for remaining chargeable resources (usage reporting can lag)
- [ ] Capture cleanup evidence

## Cleanup log [ACTUAL]

| Resource | [ACTUAL] ID | Deleted on | Evidence screenshot |
|---|---|---|---|
| ewb-bastion | i-0e82bea59f7443a3f | | |
| ewb-app-a | i-047cc151fe697b405 | | |
| ewb-app-b | i-0a0bbfd5ce64d267e | | |
| EBS volumes | Pending verification | | |
| ewb-lab key pair | ewb-lab-key-us | | |
| Elastic IP (bastion) | 52.54.130.241 / eipalloc-064bf5f4dc2f2ba11 | | |
| ewb-a-b-peer | pcx-0f1730cc1e9523713 | | |
| ewb-sg-bastion / app-a / app-b | See section 2 | | |
| Subnets (3) | See section 2 | | |
| Route tables (3) | See section 2 | | |
| ewb-igw-a | igw-0c0e6351e564526df | | |
| ewb-vpc-a | vpc-051fb20bc75c4d6c4 | | |
| ewb-vpc-b | vpc-060887d515a935fba | | |