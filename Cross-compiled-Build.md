This page describes how to build V8 for execution on risc-v hardware or risc-v qemu.
### Build a riscv-toolchain

### Configure with GN
The following commands build an riscv64 executable for v8 . We use gn to configure the debug build as such: 
`gn gen out/riscv64.native.debug --args='is_component_build=false is_debug=true target_cpu="riscv64" v8_target_cpu="riscv64" use_goma=false goma_dir="None"'`
If the binary is to large. You can add `symbol_level = 1` or `symbol_level = 0` to `./out/riscv64.native.debug/arg.gn`. If symbol_level is 0, it means there is no debug-symbol in binary. Default value is 2.