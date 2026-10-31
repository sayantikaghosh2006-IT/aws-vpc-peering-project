# 05 Viva Notes

Answers to the workbook's "Be ready to explain" questions, using the deployed addresses.

1. **Why is the return route needed?**
   Traffic from App-A reaches App-B through `ewb-rt-a-private`, but App-B's reply is routed by
   `ewb-rt-b-private`. Without 10.10.0.0/16 > pcx there, the reply has no path back. A one-way
   route is insufficient.

2. **Why is the App-B SSH source the bastion private IP?**
   ProxyJump makes the bastion open a new SSH connection to App-B from its own interface
   (10.10.1.196) across the peering connection. App-B never sees the laptop's public IP.

3. **Why does Active peering not guarantee a successful ping?**
   Peering is only the link. Route tables on both sides must point at the pcx and the
   destination security group must allow ICMP Echo request from the source IP. This was seen in
   the lab: the first forward ping failed until `ewb-sg-app-b` had an ICMP rule, and the
   controlled failure (F2) stopped replies with peering and routes unchanged.

4. **Can VPC B use VPC A's internet gateway over peering?**
   No. Peering does not provide edge-to-edge routing or transit through an IGW in the peer VPC.

## Core concepts
- Peering is one-to-one and non-transitive. A-B plus A-C does not create B-C connectivity.
- Accepted connections can be deleted. Routes and security rules remain configurable.
  Changing the paired VPCs requires a new connection.
- Security groups are stateful. Network ACLs are stateless.
