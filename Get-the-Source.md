The V8 project uses Google's [`depot_tools`](https://commondatastorage.googleapis.com/chrome-infra-docs/flat/depot_tools/docs/html/depot_tools_tutorial.html#_setting_up) to manage the source code. For this fork, we will first follow [the instructions from v8](https://v8.dev/docs/source-code) and use these tools to retrieve all of the required source from the core project. Then we will use `git` directly to retrieve the branch with the RISC-V port.

## Installing `depot_tools`

Clone the `depot_tools` repository to your system:

```bash
git clone https://chromium.googlesource.com/chromium/tools/depot_tools.git
export PATH=$PATH:/path/to/depot_tools
```

You may also want to add `export PATH=$PATH:/path/to/depot_tools` into your shell configuration file (ex. `~/.bashrc`) to ensure that this directory is always in your path.

Now, check that `depot_tools` is correctly installed:

```bash
gclient
```
If this shows the usage information, then your installation is successful.

## Checkout V8 Source from Google

Assuming `V8_ROOT` is defined to some root directory for your v8 work:
```bash
mkdir $V8_ROOT
cd $V8_ROOT
fetch v8
```
You can now browse the original v8 code under _$V8_ROOT/v8_.

## Checkout the RISC-V Branch

Since the RISC-V port is not yet upstream, we need to pull it from this repository.

```bash
cd $V8_ROOT/v8
git remote add riscv https://github.com/v8-riscv/v8.git
# or alternatively using ssh
git remote add riscv git@github.com:v8-riscv/v8.git
git fetch riscv
git checkout riscv64
```

## Patch the Chromium Build Toolchain

Since this port is not yet upstream, we will also need to apply one patch to the build files which have been fetched from a separate repository. We have added a patchfile under _patches/build.patch_ to simplify this step. Apply this patch in the _build/_ directory:

```bash
cp $V8_ROOT/v8/patches/build.patch $V8_ROOT/v8/build/
cd $V8_ROOT/v8/build
git apply build.patch
```

_Next, continue to [Simulator Build](Simulator-Build) or [Cross-compiled Build](Cross-compiled-Build)_