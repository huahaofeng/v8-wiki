
This page lists the latest test results for different builds. The statistics are presented in the following format
- Test stats are listed as +passed/-failed (e.g., `+1000/-2`)
- Run-rate is the percentage of total tests run (including both passed and failed tests), less than 100% run-rate means that some tests are skipped by the test driver (e.g., runs too long, or known failures that are being investigated). Skipped tests can be found in `<test-suite>.status` files.

Note that `mkgrokdump` suite is excluded because it does not run any tests.

- [Running on HiFive Unleashed board](#running-on-hifive-unleashed-board)
  * [V8 test suite debug build on hifive](#v8-test-suite-debug-build-on-hifive)
    + [benchmark](#benchmark)
  * [V8 test suite release build on hifve](#v8-test-suite-release-build-on-hifve)
    + [benchmark](#benchmark-1)
      - [sunspider](#sunspider)
      - [octane](#octane)
      - [kraken](#kraken)
  * [NodeJS test suite (Debug Build)](#nodejs-test-suite--debug-build-)
  * [NodeJS test suite (Release Build)](#nodejs-test-suite--release-build-)
- [Running in QEMU](#running-in-qemu)
  * [Debug Build](#debug-build)
  * [Release Build](#release-build)
- [Running on x86 via simulated v8-riscv build](#running-on-x86-via-simulated-v8-riscv-build)
  * [Debug build w/ default options](#debug-build-w--default-options)
  * [Debug build w/ stress options](#debug-build-w--stress-options)
  * [Release build testing w/ default options](#release-build-testing-w--default-options)
  * [Release build w/ the stress options](#release-build-w--the-stress-options)

<small><i><a href='http://ecotrust-canada.github.io/markdown-toc/'>Table of contents generated with markdown-toc</a></i></small>

***

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
|cctest|+ 6970/-   2|failed|
|unittests|+3727/- 0|success|
|mjsunit|+ 5022/-   4|failed|
|intl|+ 212/-   0|success|
|message|+ 309/-   0|success|
|inspector|+ 277/-   0|success|
|mkgrokdump|+   0/-   0|success|
|debugger|+ 316/-   0|success|
|wasm-js|+  61/-   0|success|
|wasm-spec-tests|+ 174/-   5|failed|
|wasm-api-tests|+  15/-   0|success|
### benchmark
sunspider
```
                               benchmark:    score |  master_ |      % |
===================================================+==========+========+
                      3d-morph-sunspider:   1159.4 |   1160.0 |        |
                   3d-raytrace-sunspider:   2039.7 |   2043.7 |    0.2 |
           access-binary-trees-sunspider:    381.3 |    380.8 |   -0.1 |
               access-fannkuch-sunspider:   1391.4 |   1390.6 |        |
                  access-nbody-sunspider:    475.4 |    475.1 |        |
                 access-nsieve-sunspider:    903.8 |    903.3 |        |
      bitops-3bit-bits-in-byte-sunspider:    185.0 |    184.5 |   -0.3 |
           bitops-bits-in-byte-sunspider:    190.6 |    190.6 |        |
            bitops-bitwise-and-sunspider:    274.5 |    274.5 |        |
            bitops-nsieve-bits-sunspider:   1021.8 |   1021.4 |        |
         controlflow-recursive-sunspider:    280.0 |    280.4 |    0.1 |
                    crypto-aes-sunspider:   1374.8 |   1375.9 |        |
                    crypto-md5-sunspider:   1070.7 |   1069.9 |        |
                   crypto-sha1-sunspider:   1322.8 |   1322.9 |        |
             date-format-tofte-sunspider:   1315.8 |   1315.9 |        |
             date-format-xparb-sunspider:   1886.7 |   1885.8 |        |
                   math-cordic-sunspider:    562.4 |    562.4 |        |
             math-partial-sums-sunspider:   1546.1 |   1546.1 |        |
            math-spectral-norm-sunspider:    357.0 |    356.2 |   -0.2 |
                    regexp-dna-sunspider:    210.2 |    210.2 |        |
                 string-base64-sunspider:   2586.0 |   2586.4 |        |
                  string-fasta-sunspider:   1495.1 |   1495.5 |        |
               string-tagcloud-sunspider:   1854.5 |   1855.2 |        |
            string-unpack-code-sunspider:   2182.4 |   2181.1 |        |
         string-validate-input-sunspider:   1409.1 |   1410.8 |    0.1 |
                               SunSpider:  29311.1 |  29315.7 |        |
---------------------------------------------------+----------+--------+
```
octane
```
                               benchmark:    score |  master_ |      % |
===================================================+==========+========+

                                Richards:    691.3 |    691.7 |        |
                               DeltaBlue:     64.3 |     86.4 S  -25.6 |
                                  Crypto:    288.6 S    284.8 S    1.3 |
                                RayTrace:    584.3 |    578.4 S    1.0 |
                             EarleyBoyer:    315.3 |    311.4 S    1.3 |
                                  RegExp:     29.1 |     29.1 |        |
                                   Splay:    423.2 |    427.3 |   -1.0 |
                            SplayLatency:   1601.2 S   1739.3 S   -7.9 |
                            NavierStokes:    621.1 |    623.4 |   -0.4 |
                                   PdfJS:    121.4 |    121.4 |        |
                                Mandreel:    199.6 |    200.2 |   -0.3 |
                         MandreelLatency:    274.2 |    274.4 |        |
                                 Gameboy:    140.8 |    140.8 |        |
                                CodeLoad:    851.2 |    853.9 |   -0.3 |
                                   Box2D:    130.6 |    128.6 S    1.6 |
                                    zlib:    977.3 |    976.1 |    0.1 |
                              Typescript:    203.2 |    201.9 |    0.6 |
                                  Octane:    286.4 S    289.8 S   -1.2 |
---------------------------------------------------+----------+--------+
```
kraken
```
                               benchmark:    score |  master_ |      % |
===================================================+==========+========+
                           ai-astar-orig:   6184.6 |   6197.3 |    0.2 |
               audio-beat-detection-orig:   6330.7 |   6329.2 |        |
                          audio-dft-orig:   5271.4 |   5280.0 |    0.2 |
                          audio-fft-orig:   3530.1 |   3527.9 |        |
                   audio-oscillator-orig:   4358.9 |   4360.5 |        |
              imaging-gaussian-blur-orig:   8068.8 |   8069.2 |        |
                   imaging-darkroom-orig:   6232.2 |   6234.7 |        |
                 imaging-desaturate-orig:   4206.6 |   4211.6 |    0.1 |
               json-parse-financial-orig:   2841.4 |   2841.7 |        |
           json-stringify-tinderbox-orig:   1010.4 |   1010.5 |        |
                stanford-crypto-aes-orig:   6609.9 |   6631.5 |    0.3 |
                stanford-crypto-ccm-orig:   9544.2 |   9565.3 |    0.2 |
             stanford-crypto-pbkdf2-orig:   8068.4 |   8085.6 |    0.2 |
   stanford-crypto-sha256-iterative-orig:   2838.5 |   2842.8 |    0.2 |
                                  Kraken:  75096.0 |  75187.8 |    0.1 |
---------------------------------------------------+----------+--------+
```

## V8 test suite release build on hifve

| Test Suite | Tests passed (run-rate)| Notes |
| - | - | - |
|unittests|+ 3721/-   0|success|
|mjsunit|+ 5016/-  27|failed|
|intl|+ 212/-   0|success|
|message|+ 309/-   0|success|
|inspector|+ 277/-   0|success|
|mkgrokdump|+   0/-   0|success|
|debugger|+ 317/-   0|success|
|wasm-js|+  62/-   0|success|
|wasm-spec-tests|+ 174/-   5|failed|
|wasm-api-tests|+  15/-   0|success|

### benchmark  
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
```
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
```

## NodeJS test suite (Debug Build)

| Test Suite | Tests passed (run-rate)| Notes |
| - | - | - |
|all |+2834/-13||

## NodeJS test suite (Release Build)

| Test Suite | Tests passed (run-rate)| Notes |
| - | - | - |
|all |+2910/-35||

# Running in QEMU

Running on QEMU 5.0.0 with fedora developer 20200108.

`commit 4950b382671c20e1900d02164d705178960502da`

## Debug Build

last update: 20200930

| Test Suite | Tests passed (run-rate)| Notes |
| - | - | - |
| cctest | +6969/-5  | http://paste.ubuntu.com/p/xTnfpNwhP4/ [#216](https://github.com/v8-riscv/v8/issues/216) [#214](https://github.com/v8-riscv/v8/issues/214) [#213](https://github.com/v8-riscv/v8/issues/213) [#192](https://github.com/v8-riscv/v8/issues/192) [#130](https://github.com/v8-riscv/v8/issues/130) |
| unittests | +3728/0 | PASS |
| mjsunit | +5023/-3  | http://paste.ubuntu.com/p/JqTVVbD6gf/|
| wasm-spec-tests | +172/-5  | http://paste.ubuntu.com/p/NPB5n42Hg8/ |
| wasm-js | +61/-0  | PASS |
| wasm-api-tests |+15/-0  | PASS |
| intl  | +212/-0  | PASS |
| message| +309/-0   |PASS|
| inspector| +277/-0  |PASS?|
| mkgrokdump| +1/-0 |Skipped|
| debugger | +316/-0 |PASS|

## Release Build

last update: 20200930

| Test Suite | Tests passed (run-rate)| Notes |
| - | - | - |
| cctest | 6968/-3 | http://paste.ubuntu.com/p/W9jMHQqzq3/ 11 -> 3 failures had been fixed |
| unittests | 3721/-0 | PASS |
| mjsunit | 5039/-4 | http://paste.ubuntu.com/p/Qdnm6h5NSW/ 25 -> 4|
| wasm-spec-tests| 172/-5 | http://paste.ubuntu.com/p/YYxWZGQ3Pk/ 5 -> 5|
| wasm-js | 62/-0 | PASS |
| wasm-api-tests | 15/-0 | PASS |
| intl  | 212/-0 | PASS |
| message| 309/-0 | PASS |
| inspector| 277/-0 | PASS |
| mkgrokdump| 1/-0 | PASS |
| debugger | 317/-0 | PASS |

# Running on x86 via simulated v8-riscv build

The simulated build is built w/ x86 as host architecture and riscv64 as target architecture so that the v8 binary is in x86 but the code generated by v8 is riscv-64 binary and run in a riscv64 simulator embedded in V8.

## Debug build w/ default options

last update: 20200930

The following stats are obtained from CI test runs and can be reproduced as
```
tools/run-tests.py --outdir=<your-simulated-build-dir> <test-suites>
```

| Test Suite | Test Status (run-rate)| Notes |
| - | - | - |
| cctest | +6965/-0  | PASS |
| unittests | +3731/-0  | PASS |
| mjsunit | +5026/-0 | PASS |
| wasm-spec-tests | +179/-0  | PASS |
| wasm-js | +61/-0  | PASS |
| wasm-api-tests | +15/-0  | PASS |
| intl | +212/-0  | PASS |
| message | +309/-0  | PASS |
| inspector | +277/-0  | PASS |
| debugger | +316/-0  | PASS |

To run one or more test suites listed below, specify your own simulated build directory, e.g.,
```
tools/run-tests.py --outdir=out.gn/riscv.gcc.simulated.debug/ <test suite>
```

## Debug build w/ stress options

last update: 20200930

The following stats are obtained from CI test runs and can be reproduced as
```
tools/run-tests.py --variants=stress --outdir=<your-simulated-debug-build-dir> <test-suites>
```

| Test Suite | Test Status (run-rate)| Notes |
| - | - | - |
| cctest | +6939/-0  | PASS|
| unittests | +0/-0  | No tests run with stress option enabled |
| mjsunit | +4989/-0 | PASS |
| wasm-spec-tests | +179/-0 | PASS |
| wasm-js | +61/-0 | PASS |
| wasm-api-tests | +0/-0 | No tests run with stress option enabled |
| intl | +212/-0  | PASS |
| message | +309/-0  |PASS |
| inspector | +277/-0  | PASS |
| debugger | +315/-0  | PASS|

## Release build testing w/ default options

last update: 20200930

The following stats are obtained from CI test runs and can be reproduced as
```
tools/run-tests.py --outdir=<your-release-build-dir> <test-suites>
```
commit:5417ef17a123aa969d09c2d0669f839c5ab55757
| Test Suite | Test Status (run-rate)| Notes |
| - | - | - |
| cctest | 6962/-0 | PASS 10 -> 0 |
| unittests | +3723/-0  | PASS |
| mjsunit | 5041/-2 | 25 -> 2 [log](http://paste.ubuntu.com/p/tJZBybndNK/) |
| wasm-spec-tests | 179/-0 | PASS |
| wasm-js | 62/-0 | PASS |
| wasm-api-tests | 15/-0 | PASS |
| intl | 212/-0 | PASS |
| message | 309/-0 | PASS |
| inspector | 277/-0 | PASS |
| debugger | 317/-0 | PASS |

## Release build w/ the stress options

last update: 20200930

The following stats are obtained from CI test runs and can be reproduced as
```
tools/run-tests.py --variants=stress --outdir=<your-simulated-release-build-dir> <test-suites>
```

| Test Suite | Tests passed (run-rate)| Notes |
| - | - | - |
| cctest | +6936/-0  | PASS 9 -> 0 |
| unittests | +0 / 0  | Skipped |
| mjsunit | +5002/-0  | PASS 23 -> 0 |
| wasm-spec-tests | +179 / 0  | PASS |
| wasm-js | +61 / 0  | PASS |
| wasm-api-tests | +0 / 0  | Skipped |
| intl | +212 / 0  | PASS |
| message | +309 / 0  | PASS |
| inspector | +277 / 0  | PASS |
| mkgrokdump | +0 / 0  | PASS |
| debugger | +316 / 0  | PASS |
