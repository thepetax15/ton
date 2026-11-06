# T1-1 unit test — results

- run_url: https://github.com/thepetax15/ton/actions/runs/42266826930
- ref: refs/heads/t1-1-unit-test
- sha: 09bce89a8e4fccd95c51d68a131cf5946e2d3ec1
- ended_utc: 1793990511 (epoch)
- elapsed_min (approx billed): 41
- cmake exit: 0
- build exit: 0
- full-run exit: 1

## Verdict per test (per-test runs, --filter, authoritative)

| Test | Role | Exit code | Meaning |
|---|---|---|---|
| FetchMcStateSmoke | SMOKE | 1 | FAIL (exit 1) |
| MergeAsymmetricS2 | FINDING | 1 | FAIL (exit 1) |
| MergeAsymmetricS3 | FINDING | 1 | FAIL (exit 1) |
| MergeSymmetricR1 | CONTROL | 1 | FAIL (exit 1) |
| MergeSymmetricS1 | CONTROL | 1 | FAIL (exit 1) |
| SplitS4 | CONTROL | 1 | FAIL (exit 1) |

## How to read

- FetchMcStateSmoke PASS = fixture + MasterchainStateQ::fetch + config/shard lookups work. FAIL = the crash is in the fixture/fetch, NOT in compare_global_balance; S2/S3 say nothing then.
- S2/S3 PASS (exit 0) = compare_global_balance returned GLOBAL BALANCE DISCREPANCY with the expected diff => **BUG CONFIRMED**.
- S2/S3 FAIL = compare returned OK or a different error => **finding DEAD** (or harness wrong — see log).
- R1/S1/S4 PASS = controls OK as expected. FAIL = harness wrong, no conclusion from S2/S3.
- NOTE: CHECK() in the test is FATAL (aborts the binary), so a failing test kills the full run at that point; per-test runs above are authoritative.

## Full-run summary


## Overall: HARNESS WRONG (smoke test failed — fixture/fetch crash, not compare)

## Per-test log tails

### FetchMcStateSmoke (exit 1)

```
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x5557bf53dec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x5557bf53dec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x5557bf53dec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x5557bf53dec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x5557bf53dec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x5557bf53dec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x5557bf53dec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x5557bf53dec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x5557bf53dec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x5557bf53dec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x5557bf53dec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x5557bf53dec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x5557bf53dec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x5557bf53dec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x5557bf53dec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x5557bf53dec6]
-------------------------------
--- Enhanced Backtrace (libbacktrace) ---
(another thread is printing backtrace)
------------------------------------------
```

### MergeAsymmetricS2 (exit 1)

```
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x55f68f473ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x55f68f473ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x55f68f473ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x55f68f473ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x55f68f473ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x55f68f473ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x55f68f473ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x55f68f473ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x55f68f473ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x55f68f473ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x55f68f473ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x55f68f473ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x55f68f473ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x55f68f473ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x55f68f473ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x55f68f473ec6]
-------------------------------
--- Enhanced Backtrace (libbacktrace) ---
(another thread is printing backtrace)
------------------------------------------
```

### MergeAsymmetricS3 (exit 1)

```
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x564ecf4d6ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x564ecf4d6ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x564ecf4d6ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x564ecf4d6ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x564ecf4d6ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x564ecf4d6ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x564ecf4d6ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x564ecf4d6ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x564ecf4d6ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x564ecf4d6ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x564ecf4d6ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x564ecf4d6ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x564ecf4d6ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x564ecf4d6ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x564ecf4d6ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x564ecf4d6ec6]
-------------------------------
--- Enhanced Backtrace (libbacktrace) ---
(another thread is printing backtrace)
------------------------------------------
```

### MergeSymmetricR1 (exit 1)

```
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x55b23d441ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x55b23d441ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x55b23d441ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x55b23d441ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x55b23d441ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x55b23d441ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x55b23d441ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x55b23d441ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x55b23d441ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x55b23d441ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x55b23d441ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x55b23d441ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x55b23d441ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x55b23d441ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x55b23d441ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x55b23d441ec6]
-------------------------------
--- Enhanced Backtrace (libbacktrace) ---
(another thread is printing backtrace)
------------------------------------------
```

### MergeSymmetricS1 (exit 1)

```
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x556b21716ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x556b21716ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x556b21716ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x556b21716ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x556b21716ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x556b21716ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x556b21716ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x556b21716ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x556b21716ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x556b21716ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x556b21716ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x556b21716ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x556b21716ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x556b21716ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x556b21716ec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x556b21716ec6]
-------------------------------
--- Enhanced Backtrace (libbacktrace) ---
(another thread is printing backtrace)
------------------------------------------
```

### SplitS4 (exit 1)

```
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x555a97c8bec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x555a97c8bec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x555a97c8bec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x555a97c8bec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x555a97c8bec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x555a97c8bec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x555a97c8bec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x555a97c8bec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x555a97c8bec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x555a97c8bec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x555a97c8bec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x555a97c8bec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x555a97c8bec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x555a97c8bec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x555a97c8bec6]
./ton/build/test/validator/test-global-balance-calculator(+0x5bbec6)[0x555a97c8bec6]
-------------------------------
--- Enhanced Backtrace (libbacktrace) ---
(another thread is printing backtrace)
------------------------------------------
```

## Registered tests (nm count, expect 18)

- count: 0

## Crash offsets resolved (addr2line)

```
== 0x112746 ==
ton::validator::(anonymous namespace)::Test_GlobalBalanceCalculator_FetchMcStateSmoke::run()
/home/runner/work/ton/ton/ton/test/validator/test-global-balance-calculator.cpp:?
== 0x112f29 ==
ton::validator::(anonymous namespace)::make_test_mc_state_root(std::vector<ton::validator::(anonymous namespace)::TestShardDesc, std::allocator<ton::validator::(anonymous namespace)::TestShardDesc> > const&)
/home/runner/work/ton/ton/ton/crypto/vm/dict.h:?
== 0x113446 ==
ton::validator::(anonymous namespace)::Test_GlobalBalanceCalculator_MergeAsymmetricS2::run()
/home/runner/work/ton/ton/ton/test/validator/test-global-balance-calculator.cpp:?
== 0x1134a6 ==
ton::validator::(anonymous namespace)::Test_GlobalBalanceCalculator_MergeAsymmetricS3::run()
/home/runner/work/ton/ton/ton/test/validator/test-global-balance-calculator.cpp:?
== 0x113506 ==
ton::validator::(anonymous namespace)::Test_GlobalBalanceCalculator_MergeSymmetricR1::run()
/home/runner/work/ton/ton/ton/test/validator/test-global-balance-calculator.cpp:?
== 0x113566 ==
ton::validator::(anonymous namespace)::Test_GlobalBalanceCalculator_MergeSymmetricS1::run()
/home/runner/work/ton/ton/ton/test/validator/test-global-balance-calculator.cpp:?
== 0x1135c5 ==
ton::validator::(anonymous namespace)::Test_GlobalBalanceCalculator_SplitS4::run()
/home/runner/work/ton/ton/ton/test/validator/test-global-balance-calculator.cpp:?
== 0x11365d ==
td::default_failure_signal_handler(int)
/home/runner/work/ton/ton/ton/tdutils/td/utils/port/signals.cpp:347
== 0x1cc8b5 ==
_start
??:?
== 0x1cd75a ==
td::Test::step()
/home/runner/work/ton/ton/ton/tdutils/td/utils/tests.h:94
== 0x1ef424 ==
td::NullLog::NullLog()
/home/runner/work/ton/ton/ton/tdutils/td/utils/logging.h:301
== 0x1f0317 ==
td::Logger::~Logger()
/home/runner/work/ton/ton/ton/tdutils/td/utils/logging.cpp:145
== 0x1f0a87 ==
td::process_fatal_error(td::CSlice)
:?
== 0x1f0ff7 ==
td::DefaultLog::append(td::CSlice, int)
/home/runner/work/ton/ton/ton/tdutils/td/utils/logging.cpp:?
== 0x1f36b8 ==
td::TestsRunner::run_all()
/home/runner/work/ton/ton/ton/tdutils/td/utils/tests.cpp:186
== 0x1f3bf5 ==
td::TestsRunner::run_all_step()
/home/runner/work/ton/ton/ton/tdutils/td/utils/tests.cpp:223
== 0x1fe2b5 ==
main
/home/runner/work/ton/ton/ton/test/test-td-main.cpp:57
== 0x2a1ca ==
??
??:0
== 0x2ae1f7 ==
td::(anonymous namespace)::print_backtrace()
/home/runner/work/ton/ton/ton/tdutils/td/utils/port/stacktrace.cpp:299
== 0x45330 ==
??
??:0
== 0x5bbd59 ==
resolve_unit_addrs_overlap_walk
/home/runner/work/ton/ton/ton/third-party/libbacktrace/dwarf.c:1624
== 0x5bbec6 ==
resolve_unit_addrs_overlap_walk
/home/runner/work/ton/ton/ton/third-party/libbacktrace/dwarf.c:1684
```
