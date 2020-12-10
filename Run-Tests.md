The v8 project has an extensive suite of tests cases. This repositories automated testing runs all of the standard test suites:
* cctest
* unittests
* wasm-api-tests
* wasm-js
* wasm-spec-tests
* mjsunit
* intl
* message
* debugger
* inspector
* mkgrokdump

**The official set of tests which must be verified before opening a pull request should be run using the script:**

For the debug build:
```bash
tools/dev/gm.py riscv64.debug.checkall
```

For the release build:
```bash
tools/dev/gm.py riscv64.release.checkall
```

Additional flags, such as `--progress=verbose` may be useful.

Individual suites can be run as shown below:

```bash
tools/run-tests.py --outdir=out/riscv64.debug cctest
```

When a test fails, it should print the command used to run that one individual test. For example, to run a single cctest:
```bash
cd out/riscv64.debug
./cctest test-macro-assembler-riscv64/Ror
```