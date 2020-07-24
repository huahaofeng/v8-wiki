This page describes how to build V8 for execution on the built-in RISC-V simulator. It is assumed that you have already followed the steps to [get the source](get-the-source).

## Install prerequisites

The following script will use `apt` to install any packages required for building v8. You will need `sudo` access to install the packages.

```bash
cd $V8_ROOT/v8
./build/install-build-deps.sh
```

## Build v8 for RISC-V Simulation of an x86-64 Host

### Configure with GN

The following commands build an x86-64 executable for v8 which generates RISC-V code and executes it within a builtin RISC-V simulator. We use `gn` to configure the build as such:

```bash
gn gen out/riscv64.sim --args='is_component_build=false is_debug=true target_cpu="x64" v8_target_cpu="riscv64" use_goma=false goma_dir="None"'
```

If you plan to use gdb to debug v8, it will be useful to add the flag `v8_optimized_debug=false` to the arguments. Without this, the code is optimized by default and will be difficult to debug.

### Build with Ninja

Next, we use ninja to complete the build:

```bash
ninja -C out/riscv64.sim -j8
```
Note, `-j8` specifies to use 8 cores for the build and should be adjusted for your build machine.

