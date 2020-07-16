This page lists the latest test results for different builds.

# Simulated build testing

The following stats are obtained from CI test runs and can be reproduced as
```
tools/run-tests.py --outdir=<your-simulated-build-dir> <test-suites>
```

| Test Suite | Tests passed (run-rate)| Notes |
| - | - | - |
| cctest | 6515 (99%) | all pass |
| unittests | 3282 (99%) | all pass |
| mjsunit | 4598 (90%) | some `asm` and `wasm` tests fail (currently all `asm/` and `wasm/` tests are skipped|
| wasm-spec-tests | 85 (44%) | `f32` and `f64` tests fail, arm64.simulated build run-rate 49% (+203/-0 tests), #13 |
| wasm-js, wasm-api-tests | 50 (50%) | no outstanding issue identified (same success rate as arm64.simulated build)|
| fuzzer | | fuzzer tests seem not enabled (see #6)|
| intl, message, inspector, mkgrokdump, debugger | 857 (59%) | arm64.simulated build run-rate 64% (+1075/-0 tests) |

To run one or more test suites listed below, specify your own simulated build directory, e.g.,
```
tools/run-tests.py --outdir=out.gn/riscv.gcc.simulated.debug/ <test suite>
```

**TODO**
- Add issue number for all failure cases

# Native build testing

TO BE ADDED

## Running in QEMU

## Running on real hardware 