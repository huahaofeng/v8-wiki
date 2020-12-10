This page describes how to build V8 for execution on the built-in RISC-V simulator. It is assumed that you have already followed the steps to [get the source](Get-the-Source).

- [Install prerequisites](#install-prerequisites)
- [Build v8 for RISC-V Simulation of an x86-64 Host](#build-v8-for-risc-v-simulation-of-an-x86-64-host)
  * [Configure with GN](#configure-with-gn)
  * [Build with Ninja](#build-with-ninja)
- [Build v8 Release Simulated Build](#build-v8-release-simulated-build)
  * [Build with gm.py](#build-with-gmpy)

<small><i><a href='http://ecotrust-canada.github.io/markdown-toc/'>Table of contents generated with markdown-toc</a></i></small>

## Install prerequisites

The following script will use `apt` to install any packages required for building v8. You will need `sudo` access to install the packages.

```bash
cd $V8_ROOT/v8
./build/install-build-deps.sh
```

## Build v8 for RISC-V Simulation on an x86-64 Host in Debug Mode

### Build with gm.py

The following command builds a debug build for an x86-64 executable for v8 which generates RISC-V code and executes it within a builtin RISC-V simulator.

```bash
tools/dev/gm.py riscv64.debug.all
```

The generated files can be found in _out/riscv64.debug/_.

If you plan to use gdb to debug v8, it will be useful to add the flag `v8_optimized_debug=false` to the arguments in _out/riscv64.debug/args.gn_. Without this, the code is optimized by default and will be difficult to debug.

## Build Release Mode

### Build with gm.py

To compile the release build (note: this only builds `d8` but none of the test binaries):

```bash
tools/dev/gm.py riscv64.release
```

The generated files can be found in _out/riscv64.release/_.

_Next, continue to [Run Tests](Run-Tests)_