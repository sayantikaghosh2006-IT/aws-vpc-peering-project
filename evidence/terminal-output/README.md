# Terminal output [ACTUAL]

Raw, unedited terminal output from the tests. The mapping to tests is in
`../test-results.md`.

| File | Contents | Test |
|---|---|---|
| `ewb-test-03.txt` | `ping -c 4 10.20.1.75`: 4 received, 0% loss | 02 Forward path (App-A to App-B) |
| `ewb-test-02.txt` | `ping -c 4 10.10.2.72`: 4 received, 0% loss | 03 Reverse path (App-B to App-A) |
| `ewb-test-04.txt` | App-B `hostname` and `ip addr` (`10.20.1.75/24`) | 04 Private SSH |
| `ewb-f1.txt` | `ping -c 4 10.20.1.75`: 0 received, 100% loss | F2 after removing the ICMP rule |
| `ewb-f2.txt` | `ping -c 4 10.20.1.75`: 0 received, 100% loss | Earlier F2 attempt; rule state at the time not captured, kept as supporting output only (see `../test-results.md` note 2) |
| `ewb-f4.txt` | `ping -c 4 10.20.1.75`: 4 received, 0% loss | F4 after restoring the rule |

The file names for tests 02 and 03 are swapped relative to the test numbers. They are kept
unchanged as recorded. No baseline (test 01) output was recorded.

Do not paste private keys, access keys or tokens.
