This page lists the latest test results for different builds.

# Simulated v8-riscv testing

The following stats are obtained from CI test runs and can be reproduced as
```
tools/run-tests.py --outdir=<your-simulated-build-dir> <test-suites>
```

| Test Suite | Tests passed (run-rate)| Notes |
| - | - | - |
| cctest | 6515 (99%) | all pass |
| unittests | 3282 (99%) | all pass |
| mjsunit | 4598 (90%) | some `asm` and `wasm` tests fail (currently all `asm/` and `wasm/` tests are skipped|
| wasm-spec-tests, wasm-js | disabled | after pulling the latest from the latest V8 repo, more tests are failing, not yet investigated  |
| wasm-api-tests | 50 (50%) | no outstanding issue identified (same success rate as arm64.simulated build)|
| fuzzer | | fuzzer tests seem not enabled (see #6)|
| intl, message, inspector, mkgrokdump, debugger | 857 (59%) | arm64.simulated build run-rate 64% (+1075/-0 tests) |

To run one or more test suites listed below, specify your own simulated build directory, e.g.,
```
tools/run-tests.py --outdir=out.gn/riscv.gcc.simulated.debug/ <test suite>
```

**TODO**
- Add performance benchmark success rate

# Cross-compiled v8-riscv testing

## Running in QEMU

- `hello-world.js` runs successfully in QEMU 5.0.0

| Test Suite | Tests passed (run-rate)| Notes |
| - | - | - |
| cctest |4203(63%)|failed|
| unittests | 3280(3282) 99%| 2 case failed|
| mjsunit | 4596(4598) 90%| |
| wasm-spec-tests, wasm-js | disabled | |
| wasm-api-tests |5(17)|failed|
| fuzzer | | fuzzer tests seem not enabled (see #6)|
| intl, message, inspector, mkgrokdump, debugger | |  |

## Running on real hardware 

| Test Suite | Tests passed (run-rate)| Notes |
| - | - | - |
| cctest |4231(6524) 64%|failed|
| unittests | 3280(3282) 99%| 2 case failed |
| mjsunit | 4575(4816) 88%|failed |
| wasm-spec-tests, wasm-js | disabled | |
| wasm-api-tests |5(17)|failed |
| fuzzer | | fuzzer tests seem not enabled (see #6)|
|  message, inspector, mkgrokdump, debugger |855(857)|2 case failed|
|intl||python script error|