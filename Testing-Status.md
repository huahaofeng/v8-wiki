This page lists the latest test results for different builds.

# Simulated v8-riscv testing

The following stats are obtained from CI test runs and can be reproduced as
```
tools/run-tests.py --outdir=<your-simulated-build-dir> <test-suites>
```

| Test Suite | Tests passed (run-rate)| Notes |
| - | - | - |
| cctest | +6515/-0 (99%) | all pass |
| unittests | +3282/-0 (99%) | all pass |
| mjsunit | +4882/-0(96%) | ~20 additional failures for riscv64 are currently skipped (issues opened)|
| wasm-spec-tests, wasm-js | disabled | after pulling the latest from the latest V8 repo, more tests are failing, not yet investigated  |
| wasm-api-tests | +17/-0 (100%) | all passed |
| fuzzer, mkgrokdump | | did not pick up any tests, similar to ARM64 (see #6)|
| intl | +218/-0 (96%) | same run rate as ARM64 |
| message | +296/-0 (48%) | same run-rate as ARM64 |
| inspector | +253/-0 (48%) | same run-rate as ARM64 |
| debugger | +308/-0 (97%) | same run-rate as ARM64 |

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
| mjsunit | 4566(4816) | |
| wasm-spec-tests, wasm-js | disabled | |
| wasm-api-tests |5(17)|failed|
| fuzzer | | fuzzer tests seem not enabled (see #6)|
| message, inspector, mkgrokdump, debugger |305(857) 27%|  |
|intl|25(218)|python script error|

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
|intl|25(218)|python script error|