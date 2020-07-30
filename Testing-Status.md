This page lists the latest test results for different builds.

The statistics are presented in the following format
- Test stats are listed as +passed/-failed (e.g., `+1000/-2`)
- Run-rate is the percentage of total tests run (including both passed and failed tests), less than 100% run-rate means that some tests are skipped by the test driver (e.g., runs too long, or known failures that are being investigated). Skipped tests can be found in `<test-suite>.status` files.

# Simulated v8-riscv testing

The following stats are obtained from CI test runs and can be reproduced as
```
tools/run-tests.py --outdir=<your-simulated-build-dir> <test-suites>
```


| Test Suite | Test Status (run-rate)| Notes |
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
- Add release build stats
- Add stress-test stats

## Testing with the stress option enabled

The following stats are obtained from CI test runs and can be reproduced as
```
tools/run-tests.py --variants=stress --outdir=<your-simulated-debug-build-dir> <test-suites>
```

| Test Suite | Test Status (run-rate)| Notes |
| - | - | - |
| cctest | +6489/-0 (98%) | |
| unittests | +0/-0 (100%) | No tests run with stress option enabled |
| mjsunit | +4819/-33(95%) | ~30 additional failure compared to non stress build (issues opened see [#56](https://github.com/v8-riscv/v8/issues/56) and [#84](https://github.com/v8-riscv/v8/issues/84)) |
| wasm-spec-tests, wasm-js | disabled | after pulling the latest from the latest V8 repo, more tests are failing, not yet investigated  |
| wasm-api-tests | +0/-0 (100%) | No tests run with stress option enabled |
| fuzzer, mkgrokdump | | did not pick up any tests, similar to ARM64 (see #6)|
| intl | +218/-0 (96%) | |
| message | +296/-0 (48%) | |
| inspector | +253/-0 (48%) | |
| debugger | +304/-4 (97%) | 4 additional failures compared to non-stress build (see [#56](https://github.com/v8-riscv/v8/issues/56))|

# Cross-compiled v8-riscv testing

## Running in QEMU

- `hello-world.js` runs successfully in QEMU 5.0.0

| Test Suite | Tests passed (run-rate)| Notes |
| - | - | - |
| cctest |4134/-2390|failed|
| unittests | 3280/-2 99%| 2 case failed|
| mjsunit | 4566/-306 | |
| wasm-spec-tests, wasm-js | disabled | |
| wasm-api-tests |5/-12|failed|
| fuzzer | | fuzzer tests seem not enabled (see #6)|
| message, inspector, mkgrokdump, debugger |305/-552 27%|may be error, test it again|
|intl|25/-193|python script error|

## Running on real hardware 

| Test Suite | Tests passed (run-rate)| Notes |
| - | - | - |
| cctest | +4231/-2293 (64%)|failed|
| unittests | +3280/-2 99%| 2 case failed |
| mjsunit | +4575/-241 88%|failed |
| wasm-spec-tests, wasm-js | disabled | |
| wasm-api-tests |+5/-12|failed |
| fuzzer | | fuzzer tests seem not enabled (see #6)|
| message, inspector, mkgrokdump, debugger |+855/-2|2 case failed|
|intl|+25/-193|python script error|


# Simulated Release Build v8-riscv testing

The following stats are obtained from CI test runs and can be reproduced as
```
tools/run-tests.py --outdir=<your-release-build-dir> <test-suites>
```

| Test Suite | Test Status (run-rate)| Notes |
| - | - | - |
| cctest | +6505/-0 (99%) | Some tests are skipped due to being killed see [#88](https://github.com/v8-riscv/v8/issues/88) |
| unittests | +3281/-0 (99%) | all pass |
| mjsunit | +4872/-4(96%) | Some tests are skipped due to being killed see [#89](https://github.com/v8-riscv/v8/issues/89) |
| wasm-spec-tests, wasm-js | disabled | after pulling the latest from the latest V8 repo, more tests are failing, not yet investigated  |
| wasm-api-tests | +17/-0 (100%) | all passed |
| fuzzer, mkgrokdump | | did not pick up any tests, similar to ARM64 (see #6)|
| intl | +218/-0 (96%) | |
| message | +296/-0 (48%) | |
| inspector | +253/-0 (48%) | |
| debugger | +309/-0 (97%) | |

## Testing with the stress option enabled

The following stats are obtained from CI test runs and can be reproduced as
```
tools/run-tests.py --variants=stress --outdir=<your-simulated-release-build-dir> <test-suites>
```

| Test Suite | Test Status (run-rate)| Notes |
| - | - | - |
| cctest | +6478/-1 (99%) | Like in the non-stress some tests are skipped, 1 additional issue [#85](https://github.com/v8-riscv/v8/issues/85) |
| unittests | +0/-0 (100%) | No unit tests run during stress testing |
| mjsunit | +4819/-22(96%) | see issues [#85](https://github.com/v8-riscv/v8/issues/85), [#86](https://github.com/v8-riscv/v8/issues/86), [#87](https://github.com/v8-riscv/v8/issues/87) |
| wasm-spec-tests, wasm-js | disabled | after pulling the latest from the latest V8 repo, more tests are failing, not yet investigated  |
| wasm-api-tests | +0/-0 (100%) | No unit tests run during stress testing |
| fuzzer, mkgrokdump | | did not pick up any tests, similar to ARM64 (see #6)|
| intl | +217/-1 (96%) | see issue [#85](https://github.com/v8-riscv/v8/issues/85) |
| message | +296/-1 (48%) | see issue [#85](https://github.com/v8-riscv/v8/issues/85) |
| inspector | +253/-1 (48%) | see issue [#85](https://github.com/v8-riscv/v8/issues/85) |
| debugger | +303/-6 (97%) | see issue [#85](https://github.com/v8-riscv/v8/issues/85) |