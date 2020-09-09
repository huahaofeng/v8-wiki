This page lists the latest test results for different builds. The statistics are presented in the following format
- Test stats are listed as +passed/-failed (e.g., `+1000/-2`)
- Run-rate is the percentage of total tests run (including both passed and failed tests), less than 100% run-rate means that some tests are skipped by the test driver (e.g., runs too long, or known failures that are being investigated). Skipped tests can be found in `<test-suite>.status` files.

Note that `fuzzer` and `mkgrokdump` suites are excluded because they do not run any tests (similar to other targets).

# Running on HiFive Unleashed board
 
HiFive Unleashed board details:
| Parameter | |
| - | - |
| SoC | SiFive Freedom U540 SoC|
| Memory |8GB DDR4 with ECC|
|OS Version|Fedora Developer Rawhide [Fedora-Developer-Rawhide-20200108.n.0]|

## V8 test suite debug build on hifive

| Test Suite | Tests passed (run-rate)| Notes |
| - | - | - |
| cctest | +6946/-1 (99%)|pass|
| unittests | +3727/0 | |
| mjsunit | +5048/-39 |failed |
| wasm-spec-tests| +0/-0 | |
| wasm-js | +63/-0(51%) | |
| wasm-api-tests |+5/-12|failed |
| message|+309/-3(99%)|failed|
|inspector|+277/-0(48%)||
|intl|+213/-17 ||
| mkgrokdump| +0/-0 | skip|
| debugger | +317/-0(97%)| pass |

## benchmark  
#### sunspider  
```
                               benchmark:    score |
===================================================+
                3d-raytrace-sunspider:       2049.1|   
           access-binary-trees-sunspider:    384.2 |
               access-fannkuch-sunspider:   1382.4 |
                  access-nbody-sunspider:    475.9 |
                 access-nsieve-sunspider:    894.6 |
      bitops-3bit-bits-in-byte-sunspider:    184.3 |
           bitops-bits-in-byte-sunspider:    192.9 |
            bitops-bitwise-and-sunspider:    276.8 |
            bitops-nsieve-bits-sunspider:   1009.5 |
         controlflow-recursive-sunspider:    280.3 |
                    crypto-aes-sunspider:   1389.5 |
                    crypto-md5-sunspider:   1079.7 |
                   crypto-sha1-sunspider:   1327.5 |
             date-format-tofte-sunspider:   1327.2 |
             date-format-xparb-sunspider:   1917.7 |
                   math-cordic-sunspider:    572.0 |
             math-partial-sums-sunspider:   1552.3 |
            math-spectral-norm-sunspider:    361.5 |
                    regexp-dna-sunspider:    210.9 |
                 string-base64-sunspider:   2596.0 |
                  string-fasta-sunspider:   1496.0 |
               string-tagcloud-sunspider:   1868.5 |
            string-unpack-code-sunspider:   2184.5 |
         string-validate-input-sunspider:   1422.5 |
                               SunSpider:  29477.0 |
---------------------------------------------------+

```
#### octane   
```
      benchmark:    score |
==========================+
       Richards:    684.2 |
      DeltaBlue:     65.6 S
         Crypto:    276.4 S
       RayTrace:    577.8 |
    EarleyBoyer:    313.2 |
         RegExp:     29.0 |
          Splay:    425.4 |
   SplayLatency:   1394.3 S
   NavierStokes:    615.9 S
          PdfJS:    120.6 |
       Mandreel:    201.3 |
MandreelLatency:    274.1 |
        Gameboy:    140.8 |
       CodeLoad:    853.7 |
          Box2D:    129.2 |
           zlib:    976.4 |
     Typescript:    200.8 |
         Octane:    280.7 S
--------------------------+
```
#### kraken 
                               benchmark:    score |
===================================================+
                           ai-astar-orig:   6215.3 |
               audio-beat-detection-orig:   6354.3 |
                          audio-dft-orig:   5213.4 |
                          audio-fft-orig:   3538.3 |
                   audio-oscillator-orig:   4374.5 |
              imaging-gaussian-blur-orig:   8097.9 |
                   imaging-darkroom-orig:   6246.3 |
                 imaging-desaturate-orig:   4205.5 |
               json-parse-financial-orig:   2842.0 |
           json-stringify-tinderbox-orig:   1005.8 |
                stanford-crypto-aes-orig:   6651.4 |
                stanford-crypto-ccm-orig:   9653.2 |
             stanford-crypto-pbkdf2-orig:   8109.6 |
   stanford-crypto-sha256-iterative-orig:   2857.0 |
                                  Kraken:  75364.7 |
---------------------------------------------------+ 

## V8 test suite release build on hifve

| Test Suite | Tests passed (run-rate)| Notes |
| - | - | - |
| cctest | +6932/-11(99%)|failed|
| unittests | +3727/0 | pass|
| mjsunit | +5016/-27(97%) |failed|
| wasm-spec-tests|+72/-5(45%)|failed|
| wasm-js | +64/-0(52%) | pass|
| wasm-api-tests |+15/-0(88%)|pass|
|intl|+212/-0(96%)|pass|
| message|+309/-0(48%)|pass|
|inspector|+277/-0(48%)|pass|
| mkgrokdump| 0 (0%) -0 | skip |
| debugger | +317/-0(97%)| pass|

benchmark
| Test Suite | Tests passed (run-rate)| Notes |
| - | - | - |
| sunspider|TODO|N/A|
| octane|TODO|N/A|
| kraken|TODO|N/A|


## NodeJS test suite (Debug Build)

| Test Suite | Tests passed (run-rate)| Notes |
| - | - | - |
|all |+2834/-13(98%)||

## NodeJS test suite (Release Build)

| Test Suite | Tests passed (run-rate)| Notes |
| - | - | - |
|all |+2910/-35(100%)||

# Running in QEMU

Running on QEMU 5.0.0 with fedora developer 20200108.

`commit 5417ef17a123aa969d09c2d0669f839c5ab55757`

## Debug Build

last update: 20200906

| Test Suite | Tests passed (run-rate)| Notes |
| - | - | - |
| cctest | +6947/-5 (%99) | [#216](https://github.com/v8-riscv/v8/issues/216) [#214](https://github.com/v8-riscv/v8/issues/214) [#213](https://github.com/v8-riscv/v8/issues/213) [#192](https://github.com/v8-riscv/v8/issues/192) [#130](https://github.com/v8-riscv/v8/issues/130) http://paste.ubuntu.com/p/hcPsjFcx9B/|
| unittests | +3729/-1 (99%)| 1 failed [#174](https://github.com/v8-riscv/v8/issues/174) http://paste.ubuntu.com/p/DGmyPdjnJP/|
| mjsunit | +5026/-3 (96%) | 3 failed #177 #179 http://paste.ubuntu.com/p/bqgYRHQYJb/|
| wasm-spec-tests | +172/-5 (45%) | http://paste.ubuntu.com/p/xhgCMj9mHm/ |
| wasm-js | +61/-0 (54%) |  |
| wasm-api-tests |+15/-0 (88%) | |
| intl  | +212/-0 (96%) | PASS |
| message| +309/-0  (48%) |PASS|
| inspector| +277/-0 (48%) |PASS?|
| mkgrokdump| +0/-0 |Skipped|
| debugger | +316/-0 (97%)|PASS|

## Release Build

last update: 20200906

| Test Suite | Tests passed (run-rate)| Notes |
| - | - | - |
| cctest | 6944 (99%) -11 | http://paste.ubuntu.com/p/G4xf83jfPF/ |
| unittests | 3721 (99%) -0 | http://paste.ubuntu.com/p/X4KRBq8d68/ |
| mjsunit | 5043 (97%) -25 |http://paste.ubuntu.com/p/vTMbMNwS43/ |
| wasm-spec-tests| 177 (45%) -5 | http://paste.ubuntu.com/p/Dhnt3gFx88/ |
| wasm-js | 62 (55%) -0 | PASS |
| wasm-api-tests | 15 (88%) -0 | PASS |
| intl  | 212 (96%) -0 | PASS |
| message| 309 (48%) -0 | PASS |
| inspector| 277 (48%) -0 | PASS |
| mkgrokdump| 0 (0%) -0 | skip |
| debugger | 317 (97%) -0 | PASS |

# Running on x86 via simulated v8-riscv build

The simulated build is built w/ x86 as host architecture and riscv64 as target architecture so that the v8 binary is in x86 but the code generated by v8 is riscv-64 binary and run in a riscv64 simulator embedded in V8.

## Debug build w/ default options

last update: 20200906

The following stats are obtained from CI test runs and can be reproduced as
```
tools/run-tests.py --outdir=<your-simulated-build-dir> <test-suites>
```

| Test Suite | Test Status (run-rate)| Notes |
| - | - | - |
| cctest | +6938/-0 (99%) | PASS |
| unittests | +3731/-0 (99%) | all passed |
| mjsunit | +5026/-0(96%) | PASS |
| wasm-spec-tests | +177/-0 (45%) | PASS? |
| wasm-js | +61/-0 (54%) | PASS? |
| wasm-api-tests | +17/-0 (88%) | PASS |
| intl | +212/-0 (96%) | same run rate as ARM64 |
| message | +309/-0 (48%) | same run-rate as ARM64 |
| inspector | +277/-0 (48%) | same run-rate as ARM64 |
| debugger | +316/-0 (97%) | same run-rate as ARM64 |

To run one or more test suites listed below, specify your own simulated build directory, e.g.,
```
tools/run-tests.py --outdir=out.gn/riscv.gcc.simulated.debug/ <test suite>
```

## Debug build w/ stress options

last update: 20200906

The following stats are obtained from CI test runs and can be reproduced as
```
tools/run-tests.py --variants=stress --outdir=<your-simulated-debug-build-dir> <test-suites>
```

| Test Suite | Test Status (run-rate)| Notes |
| - | - | - |
| cctest | +6912/-0 (98%) | PASS|
| unittests | +0/-0 (100%) | No tests run with stress option enabled |
| mjsunit | +4989/-0(95%) | PASS |
| wasm-spec-tests | +177/-0 %45| PASS |
| wasm-js | +61/-0 %54| PASS |
| wasm-api-tests | +0/-0 | No tests run with stress option enabled |
| intl | +212/-0 (96%) | PASS same run rate as ARM64|
| message | +309/-0 (48%) |PASS same run rate as ARM64 |
| inspector | +277/-0 (48%) | PASS same run rate as ARM64|
| debugger | +315/-0 (97%) | PASS|

## Release build testing w/ default options

last update: 20200906

The following stats are obtained from CI test runs and can be reproduced as
```
tools/run-tests.py --outdir=<your-release-build-dir> <test-suites>
```
commit:5417ef17a123aa969d09c2d0669f839c5ab55757
| Test Suite | Test Status (run-rate)| Notes |
| - | - | - |
| cctest | 6935 (99%) -10 | **10 tests failed** http://paste.ubuntu.com/p/GtzhhJJkc4/ |
| unittests | +3723/-0 (99%) | PASSED http://paste.ubuntu.com/p/YPxNdGK2x4/|
| mjsunit | 5043 (97%) -25 | **25 tests failed** http://paste.ubuntu.com/p/7T7RFyJ2H2/) |
| wasm-spec-tests | 177 (45%) -0 | pass |
| wasm-js | 62 (55%) -0 | pass |
| wasm-api-tests | 15 (88%) -0 | all passed |
| intl | 212 (96%) -0 | same run rate as ARM64|
| message | 309 (48%) -0 | same run rate as ARM64|
| inspector | 277 (48%) -0 | same run rate as ARM64|
| debugger | 317 (97%) -0 | same run rate as ARM64|

## Release build w/ the stress options

last update: 20200906

The following stats are obtained from CI test runs and can be reproduced as
```
tools/run-tests.py --variants=stress --outdir=<your-simulated-release-build-dir> <test-suites>
```

| Test Suite | Tests passed (run-rate)| Notes |
| - | - | - |
| cctest | + 6918 / -9 （ -98% 6982 ） | http://paste.ubuntu.com/p/WnB78vQ9QC/ |
| unittests | + 0 / 0 （ 0% 3736 ） | http://paste.ubuntu.com/p/zTB6JrcB2b/ |
| mjsunit | + 5025 / -23 （ -96% 5197 ） | http://paste.ubuntu.com/p/p8XgfdWY3X/ |
| wasm-spec-tests | + 177 / 0 （ -45% 387 ） | http://paste.ubuntu.com/p/k3DtYPQfc7/ |
| wasm-js | + 61 / 0 （ -54% 111 ） | http://paste.ubuntu.com/p/ntKVB7kw4f/ |
| wasm-api-tests | + 0 / 0 （ 0% 17 ） | http://paste.ubuntu.com/p/SKh63TsJpg/ |
| intl | + 212 / 0 （ -96% 219 ） | http://paste.ubuntu.com/p/j7FsDgdkjF/ |
| message | + 309 / 0 （ -48% 637 ） | http://paste.ubuntu.com/p/xpJxPphbRF/ |
| inspector | + 277 / 0 （ -48% 571 ） | http://paste.ubuntu.com/p/xJDpS8MTDP/ |
| mkgrokdump | + 0 / 0 （ 0% 1 ） | http://paste.ubuntu.com/p/wgS8cgvPXT/ |
| debugger | + 316 / 0 （ -97% 324 ） | http://paste.ubuntu.com/p/mTH69gxH3G/ |