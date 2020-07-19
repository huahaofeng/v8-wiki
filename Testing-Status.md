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
- others to be added

## Running on real hardware 