The v8 project has an extensive suite of tests cases. Run all of the tests using the following command. The directory specified in this command, `out/riscv64.sim`, should match the directory (specified when you built) of the configuration you would like to test.

```bash
cd $V8_ROOT/v8
tools/run-tests.py --outdir=out/riscv64.sim
```

Individual suites can be run as shown below:

```bash
tools/run-tests.py --outdir=out/riscv64.sim cctest
```

This repositories automated testing currently runs the following suites:
* cctest
* unittests
* wasm-api-tests
* wasm-js wasm-spec-tests
* mjsunit
* intl
* message
* debugger
* inspector
* mkgrokdump