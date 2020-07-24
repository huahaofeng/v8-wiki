This page describes how to build V8 for execution on risc-v hardware or risc-v qemu.
### Cross compiled
#### Build a riscv-toolchain
```
$ git clone https://github.com/riscv/riscv-gnu-toolchain
$ cd riscv-gnu-toolchain
$ git submodule update --init --recursive

./configure --prefix=/opt/riscv
make linux
```

#### Apply build patch
```
cp ./patch/build.patch ./build/
cd ./build/
git apply build.patch
```
note: if prefix name of your riscv-toolchain is not riscv64-linux-gnu, please modify `toolprefix` of `gcc_toolchain("riscv64")` in `./build/toolchain/linux/BUIID.gn`.

#### Configure with GN
  The following commands build an riscv64 executable for v8 . We use gn to configure the debug build as such: 
```
gn gen out/riscv64.native.debug --args='is_component_build=false is_debug=true target_cpu="riscv64" v8_target_cpu="riscv64" use_goma=false goma_dir="None" treat_warnings_as_errors=false'
```   
If the binary is too large. You can add `symbol_level = 1` or `symbol_level = 0` to `./out/riscv64.native.debug/arg.gn`. If symbol_level is 0, it means there is no debug-symbol in binary. Default value is 2.


#### build 
```
ninja -j4
```

### Run on QEMU
#### build qemu-system-riscv64
clone the qemu:
```
git clone git@github.com:qemu/qemu.git
git checkout v5.0.0
```
#### build it for riscv64:
```
./configure --target-list=riscv64-softmmu && make -j 4
sudo make install # if you want. Optional. 
```
#### download a fedora-riscv64-image and run it:
PLCT lab provide a script to run fedora-riscv64:[deploy_riscv64fedora_qemu.sh](https://github.com/isrc-cas/PLCT-Toolbox/blob/master/deploy_riscv64fedora_qemu.sh).
run this script:
```
./deploy_riscv64fedora_qemu.sh 
```

If can't download or download slowly fedora-riscv64 from https://mirror.iscas.ac.cn/, you can get it from https://dl.fedoraproject.org/pub/alt/risc-v/repo/virt-builder-images/images/ .  
Download `Fedora-Developer-Rawhide-*.raw.xz` as well as the matching `Fedora-Developer-Rawhide-*-fw_payload-uboot-qemu-virt-smode.elf`   
uncompress it before it can be used:
```
unxz Fedora-Developer-Rawhide-*.raw.xz
```
#### run it on qemu:
```
## ${VER} is version number of Fedora-Developer-Rawhide-*.raw.xz and Fedora-Developer-Rawhide-*-fw_payload-uboot-qemu-virt-smode.elf.
## I use ${VER}=20191123.n.0
## default `root` passwd is `fedora_rocks!`
qemu-system-riscv64 \
  -nographic \
  -machine virt \
  -smp 4 \
  -m 2G \
  -kernel Fedora-Developer-Rawhide-${VER}-fw_payload-uboot-qemu-virt-smode.elf \
  -object rng-random,filename=/dev/urandom,id=rng0 \
  -device virtio-rng-device,rng=rng0 \
  -device virtio-blk-device,drive=hd0 \
  -drive file=Fedora-Developer-Rawhide-${VER}-sda.raw,format=raw,id=hd0 \
  -device virtio-net-device,netdev=usernet \
  -netdev user,id=usernet,hostfwd=tcp::3333-:22
```
**the SSH port number 22 of fedora is mapped to 3333.**  
use `scp -P 3333 your-file-want-to-copy-into root@localhost:~/` to copy files into/out from.'  


**Note: more info about Fedora-riscv ,please visit https://fedoraproject.org/wiki/Architectures/RISC-V/Installing**

