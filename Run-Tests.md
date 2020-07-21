The v8 project has an extensive suite of tests cases. This repositories automated testing currently runs the following suites:
* cctest
* unittests
* wasm-api-tests
* wasm-js _(temporarily removed)_
* wasm-spec-tests _(temporarily removed)_
* mjsunit
* intl
* message
* debugger
* inspector
* mkgrokdump

**The official set of tests which must be verified before opening a pull request should be run using the script:**
```
v8/v8-riscv/test-riscv.sh
```

Run all of the tests using the following command. The directory specified in this command, `out/riscv64.sim`, should match the directory (specified when you built) of the configuration you would like to test.

```bash
cd $V8_ROOT/v8
tools/run-tests.py --outdir=out/riscv64.sim
```

Individual suites can be run as shown below:

```bash
tools/run-tests.py --outdir=out/riscv64.sim cctest
```

When a test fails, it should print the command used to run that one individual test. For example, to run a single cctest:
```bash
cd out/riscv64.sim
./cctest test-macro-assembler-riscv64/Ror
```