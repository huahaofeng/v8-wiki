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
cd v8
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
git checkout riscv-porting-dev
```

## Patch the Chromium Build Toolchain

Since this port is not yet upstream, we will also need to apply one patch to the build files which have been fetched from a separate repository. Create a file, _build.patch_, under $V8_ROOT/v8/build with the following contents:

```diff
diff --git a/build_config.h b/build_config.h
index d3cdd2db4..18f37cbbb 100644
--- a/build_config.h
+++ b/build_config.h
@@ -166,6 +166,20 @@
 #define ARCH_CPU_32_BITS 1
 #define ARCH_CPU_BIG_ENDIAN 1
 #endif
+#elif defined(__riscv) || defined(__riscv__)
+#if __riscv_xlen == 64
+#define ARCH_CPU_RISCV_FAMILY 1
+#define ARCH_CPU_RISCV64 1
+#define ARCH_CPU_64_BITS 1
+#define ARCH_CPU_LITTLE_ENDIAN 1
+#elif __riscv_xlen == 32
+#define ARCH_CPU_RISCV_FAMILY 1
+#define ARCH_CPU_RISCV32 1
+#define ARCH_CPU_32_BITS 1
+#define ARCH_CPU_LITTLE_ENDIAN 1
+#else
+#error "Cannot detect Riscv's bitwidth"
+#endif
 #else
 #error Please add support for your architecture in build/build_config.h
 #endif
diff --git a/toolchain/linux/BUILD.gn b/toolchain/linux/BUILD.gn
index fa8b17e9d..f275e4dc5 100644
--- a/toolchain/linux/BUILD.gn
+++ b/toolchain/linux/BUILD.gn
@@ -65,6 +65,24 @@ gcc_toolchain("arm") {
   }
 }

+gcc_toolchain("riscv64") {
+  toolprefix = "riscv64-unknown-linux-gnu-"
+
+  cc = "${toolprefix}gcc"
+  cxx = "${toolprefix}g++"
+
+  ar = "${toolprefix}ar"
+  ld = cxx
+  readelf = "${toolprefix}readelf"
+  nm = "${toolprefix}nm"
+
+  toolchain_args = {
+    current_cpu = "riscv64"
+    current_os = "linux"
+    is_clang = false
+  }
+}
+
 clang_toolchain("clang_x86") {
   # Output linker map files for binary size analysis.
   enable_linker_map = true
@@ -136,6 +154,14 @@ clang_toolchain("clang_x64_v8_arm64") {
   }
 }

+clang_toolchain("clang_x64_v8_riscv64") {
+  toolchain_args = {
+    current_cpu = "x64"
+    v8_current_cpu = "riscv64"
+    current_os = "linux"
+  }
+}
+
 clang_toolchain("clang_x64_v8_mips64el") {
   toolchain_args = {
     current_cpu = "x64"
```

Now, apply this patch:

```bash
cd $V8_ROOT/v8/build
git apply build.patch
```