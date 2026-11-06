# T1-1 unit test — results

- run_url: https://github.com/thepetax15/ton/actions/runs/42257911684
- ref: refs/heads/t1-1-unit-test
- sha: 27c99ccb581200bf118e1ee724dfc9f03c047a50
- ended_utc: 1793985885 (epoch)
- elapsed_min (approx billed): 23
- cmake exit: 0
- build exit: 0
- full-run exit: 139

## Verdict per test (per-test runs, --filter, authoritative)

| Test | Role | Exit code | Meaning |
|---|---|---|---|
| FetchMcStateSmoke | SMOKE | 0 | PASS |
| MergeAsymmetricS2 | FINDING | 139 | FAIL (exit 139) |
| MergeAsymmetricS3 | FINDING | 139 | FAIL (exit 139) |
| MergeSymmetricR1 | CONTROL | 139 | FAIL (exit 139) |
| MergeSymmetricS1 | CONTROL | 139 | FAIL (exit 139) |
| SplitS4 | CONTROL | 139 | FAIL (exit 139) |

## How to read

- FetchMcStateSmoke PASS = fixture + MasterchainStateQ::fetch + config/shard lookups work. FAIL = the crash is in the fixture/fetch, NOT in compare_global_balance; S2/S3 say nothing then.
- S2/S3 PASS (exit 0) = compare_global_balance returned GLOBAL BALANCE DISCREPANCY with the expected diff => **BUG CONFIRMED**.
- S2/S3 FAIL = compare returned OK or a different error => **finding DEAD** (or harness wrong — see log).
- R1/S1/S4 PASS = controls OK as expected. FAIL = harness wrong, no conclusion from S2/S3.
- NOTE: CHECK() in the test is FATAL (aborts the binary), so a failing test kills the full run at that point; per-test runs above are authoritative.

## Full-run summary


## Overall: HARNESS WRONG (a control failed)

## Per-test log tails

### FetchMcStateSmoke (exit 0)

```
0 test(s) passed
```

### MergeAsymmetricS2 (exit 139)

```
Running test Test_GlobalBalanceCalculator_MergeAsymmetricS2...
[pid 34110] [time 1793985884] Signal: 11
------- Stack Backtrace -------
./ton/build/test/validator/test-global-balance-calculator(+0x2ae047)[0x55f2e16a9047]
./ton/build/test/validator/test-global-balance-calculator(+0x11277d)[0x55f2e150d77d]
/lib/x86_64-linux-gnu/libc.so.6(+0x45330)[0x7f6b58445330]
./ton/build/test/validator/test-global-balance-calculator(+0x1e92a0)[0x55f2e15e42a0]
./ton/build/test/validator/test-global-balance-calculator(+0x1e8c76)[0x55f2e15e3c76]
./ton/build/test/validator/test-global-balance-calculator(+0x1cc79a)[0x55f2e15c779a]
./ton/build/test/validator/test-global-balance-calculator(+0x1f3a45)[0x55f2e15eea45]
./ton/build/test/validator/test-global-balance-calculator(+0x1f3508)[0x55f2e15ee508]
./ton/build/test/validator/test-global-balance-calculator(+0x1fe105)[0x55f2e15f9105]
/lib/x86_64-linux-gnu/libc.so.6(+0x2a1ca)[0x7f6b5842a1ca]
/lib/x86_64-linux-gnu/libc.so.6(__libc_start_main+0x8b)[0x7f6b5842a28b]
./ton/build/test/validator/test-global-balance-calculator(+0x1cb8f5)[0x55f2e15c68f5]
-------------------------------
--- Enhanced Backtrace (libbacktrace) ---
```

### MergeAsymmetricS3 (exit 139)

```
Running test Test_GlobalBalanceCalculator_MergeAsymmetricS3...
[pid 34122] [time 1793985885] Signal: 11
------- Stack Backtrace -------
./ton/build/test/validator/test-global-balance-calculator(+0x2ae047)[0x55c3c42b8047]
./ton/build/test/validator/test-global-balance-calculator(+0x11277d)[0x55c3c411c77d]
/lib/x86_64-linux-gnu/libc.so.6(+0x45330)[0x7fd520645330]
./ton/build/test/validator/test-global-balance-calculator(+0x1e92a0)[0x55c3c41f32a0]
./ton/build/test/validator/test-global-balance-calculator(+0x1e9d56)[0x55c3c41f3d56]
./ton/build/test/validator/test-global-balance-calculator(+0x1cc79a)[0x55c3c41d679a]
./ton/build/test/validator/test-global-balance-calculator(+0x1f3a45)[0x55c3c41fda45]
./ton/build/test/validator/test-global-balance-calculator(+0x1f3508)[0x55c3c41fd508]
./ton/build/test/validator/test-global-balance-calculator(+0x1fe105)[0x55c3c4208105]
/lib/x86_64-linux-gnu/libc.so.6(+0x2a1ca)[0x7fd52062a1ca]
/lib/x86_64-linux-gnu/libc.so.6(__libc_start_main+0x8b)[0x7fd52062a28b]
./ton/build/test/validator/test-global-balance-calculator(+0x1cb8f5)[0x55c3c41d58f5]
-------------------------------
--- Enhanced Backtrace (libbacktrace) ---
```

### MergeSymmetricR1 (exit 139)

```
Running test Test_GlobalBalanceCalculator_MergeSymmetricR1...
[pid 34138] [time 1793985885] Signal: 11
------- Stack Backtrace -------
./ton/build/test/validator/test-global-balance-calculator(+0x2ae047)[0x55f291bc0047]
./ton/build/test/validator/test-global-balance-calculator(+0x11277d)[0x55f291a2477d]
/lib/x86_64-linux-gnu/libc.so.6(+0x45330)[0x7f3f54445330]
./ton/build/test/validator/test-global-balance-calculator(+0x1e92a0)[0x55f291afb2a0]
./ton/build/test/validator/test-global-balance-calculator(+0x1e9dc6)[0x55f291afbdc6]
./ton/build/test/validator/test-global-balance-calculator(+0x1cc79a)[0x55f291ade79a]
./ton/build/test/validator/test-global-balance-calculator(+0x1f3a45)[0x55f291b05a45]
./ton/build/test/validator/test-global-balance-calculator(+0x1f3508)[0x55f291b05508]
./ton/build/test/validator/test-global-balance-calculator(+0x1fe105)[0x55f291b10105]
/lib/x86_64-linux-gnu/libc.so.6(+0x2a1ca)[0x7f3f5442a1ca]
/lib/x86_64-linux-gnu/libc.so.6(__libc_start_main+0x8b)[0x7f3f5442a28b]
./ton/build/test/validator/test-global-balance-calculator(+0x1cb8f5)[0x55f291add8f5]
-------------------------------
--- Enhanced Backtrace (libbacktrace) ---
```

### MergeSymmetricS1 (exit 139)

```
Running test Test_GlobalBalanceCalculator_MergeSymmetricS1...
[pid 34152] [time 1793985885] Signal: 11
------- Stack Backtrace -------
./ton/build/test/validator/test-global-balance-calculator(+0x2ae047)[0x556f19c33047]
./ton/build/test/validator/test-global-balance-calculator(+0x11277d)[0x556f19a9777d]
/lib/x86_64-linux-gnu/libc.so.6(+0x45330)[0x7f68be845330]
./ton/build/test/validator/test-global-balance-calculator(+0x1e92a0)[0x556f19b6e2a0]
./ton/build/test/validator/test-global-balance-calculator(+0x1e9e36)[0x556f19b6ee36]
./ton/build/test/validator/test-global-balance-calculator(+0x1cc79a)[0x556f19b5179a]
./ton/build/test/validator/test-global-balance-calculator(+0x1f3a45)[0x556f19b78a45]
./ton/build/test/validator/test-global-balance-calculator(+0x1f3508)[0x556f19b78508]
./ton/build/test/validator/test-global-balance-calculator(+0x1fe105)[0x556f19b83105]
/lib/x86_64-linux-gnu/libc.so.6(+0x2a1ca)[0x7f68be82a1ca]
/lib/x86_64-linux-gnu/libc.so.6(__libc_start_main+0x8b)[0x7f68be82a28b]
./ton/build/test/validator/test-global-balance-calculator(+0x1cb8f5)[0x556f19b508f5]
-------------------------------
--- Enhanced Backtrace (libbacktrace) ---
```

### SplitS4 (exit 139)

```
Running test Test_GlobalBalanceCalculator_SplitS4...
[pid 34166] [time 1793985885] Signal: 11
------- Stack Backtrace -------
./ton/build/test/validator/test-global-balance-calculator(+0x2ae047)[0x5594baaed047]
./ton/build/test/validator/test-global-balance-calculator(+0x11277d)[0x5594ba95177d]
/lib/x86_64-linux-gnu/libc.so.6(+0x45330)[0x7fa38ee45330]
./ton/build/test/validator/test-global-balance-calculator(+0x1e92a0)[0x5594baa282a0]
./ton/build/test/validator/test-global-balance-calculator(+0x1e9eb5)[0x5594baa28eb5]
./ton/build/test/validator/test-global-balance-calculator(+0x1cc79a)[0x5594baa0b79a]
./ton/build/test/validator/test-global-balance-calculator(+0x1f3a45)[0x5594baa32a45]
./ton/build/test/validator/test-global-balance-calculator(+0x1f3508)[0x5594baa32508]
./ton/build/test/validator/test-global-balance-calculator(+0x1fe105)[0x5594baa3d105]
/lib/x86_64-linux-gnu/libc.so.6(+0x2a1ca)[0x7fa38ee2a1ca]
/lib/x86_64-linux-gnu/libc.so.6(__libc_start_main+0x8b)[0x7fa38ee2a28b]
./ton/build/test/validator/test-global-balance-calculator(+0x1cb8f5)[0x5594baa0a8f5]
-------------------------------
--- Enhanced Backtrace (libbacktrace) ---
```
