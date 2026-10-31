# VPC Peering Test Results [ACTUAL]

Results below come from the terminal output in `terminal-output/` and the screenshots in
`screenshots/`, or, where marked "reported", from the student's own account of the step. A step without a
recorded result is not marked as passed. Expected results are in `../docs/03-test-plan.md`.

## Environment
- Region: us-east-1 (N. Virginia)
- VPC-A: 10.10.0.0/16
- VPC-B: 10.20.0.0/16
- Bastion: 10.10.1.196 (public subnet A)
- App-A: 10.10.2.72 (private subnet A)
- App-B: 10.20.1.75 (private subnet B)

## Test Summary

| Test | Description | Command | Actual Result | Status | Evidence |
|---|---|---|---|---|---|
| 01 | Baseline before peering (App-A to App-B) | `ping -c 4 10.20.1.75` | Not recorded | NOT RECORDED | None |
| 02 | Forward path: App-A to App-B | `ping -c 4 10.20.1.75` | 4 transmitted, 4 received, 0% packet loss | PASS | `terminal-output/ewb-test-03.txt` (see note 1); `screenshots/tests/test-02-03-ping-forward-and-reverse.png` |
| 03 | Reverse path: App-B to App-A | `ping -c 4 10.10.2.72` | 4 transmitted, 4 received, 0% packet loss | PASS | `terminal-output/ewb-test-02.txt` (see note 1); `screenshots/tests/test-02-03-ping-forward-and-reverse.png` |
| 04 | SSH from laptop (WSL) to App-B through the bastion | `ssh ewb-app-b` | Shell on App-B: hostname `ip-10-20-1-75.ec2.internal`, `ens5` `10.20.1.75/24` | PASS | `terminal-output/ewb-test-04.txt`; `screenshots/tests/test-04-wsl-proxyjump-ssh-app-b.png`. Earlier bastion-hop session (key on the bastion, see inventory deviations): `screenshots/tests/test-04-bastion-ssh-app-b-hostname-ip.png` |
| 05 | Private instance isolation | EC2 console, instance details | App-A and App-B show no public IPv4 address in EC2 instance details | PASS | `screenshots/infrastructure/07-ec2-app-a-details-no-public-ip.png`, `screenshots/infrastructure/08-ec2-app-b-details-no-public-ip.png` |

## Controlled failure

| Step | Description | Actual Result | Status | Evidence |
|---|---|---|---|---|
| F1 | Remove the App-B inbound ICMP rule (source `10.10.2.72/32`) | ewb-sg-app-b left with only the SSH rule | DONE | `screenshots/tests/test-f1-app-b-icmp-rule-removed.png` |
| F2 | From App-A: `ping -c 4 10.20.1.75` | 4 transmitted, 0 received, 100% packet loss | PASS (failed as expected) | `terminal-output/ewb-f1.txt`; `screenshots/tests/test-f2-ping-blocked-after-rule-removal.png` (see note 2) |
| F3 | Restore the App-B inbound ICMP rule | Rule restored (reported) | DONE | Screenshot not yet in the repository |
| F4 | From App-A: `ping -c 4 10.20.1.75` | 4 transmitted, 4 received, 0% packet loss | PASS | `terminal-output/ewb-f4.txt` |

## Notes

1. The two ping files are named the other way round from the test numbers.
   `ewb-test-03.txt` pings `10.20.1.75` (App-A to App-B, test 02) and `ewb-test-02.txt`
   pings `10.10.2.72` (App-B to App-A, test 03). The files are kept unchanged as evidence.
2. `ewb-f1.txt` is the run shown in `test-f2-ping-blocked-after-rule-removal.png`, taken right after
   the rule removal in `test-f1-app-b-icmp-rule-removed.png`. `ewb-f2.txt` also shows 100% loss,
   but it comes from an earlier attempt, and the screenshots don't show the rule state at the time
   it ran, so it is kept only as supporting output. No screenshot of the rule being restored (F3)
   or of the F4 ping exists; F4 relies on `ewb-f4.txt`.
3. The ping output files do not include `hostname` or `hostname -I`, so the source host is
   identified by the test description, not by the file itself.
4. Test 01 was not captured before the peering routes were added. It is not claimed as passed.
5. Before test 02 was recorded, a forward ping from App-A failed with 100% packet loss because
   `ewb-sg-app-b` did not yet have an ICMP rule. The rule was added and the ping was rerun
   (`ewb-test-03.txt`). That failed run happened after peering, so it is not a substitute for
   the test 01 baseline. See `../docs/04-troubleshooting.md`.

## Conclusion

Tests 02 and 03 show two-way private connectivity between App-A and App-B over VPC peering.
Test 04 shows administrative SSH to App-B through the bastion. The controlled failure shows
that removing the App-B ICMP rule blocked ping from App-A (F2) and that restoring it
recovered connectivity (F4). The pre-peering baseline (test 01) was not recorded.
