This page describes how to build V8 for execution on RISC-V hardware or RISC-V QEMU.  It is assumed that you have already followed the steps to [get the source](get-the-source).

## RISC-V Toolchain
### Install Prerequisites
```
sudo apt install gawk texinfo zlib1g-dev
```

### Build Toolchain
```
$ git clone https://github.com/riscv/riscv-gnu-toolchain
$ cd riscv-gnu-toolchain
$ git submodule update --init --recursive

./configure --prefix=/opt/riscv
make linux
```
Be sure to add the path to this new toolchain to your path:

```
export PATH="/opt/riscv/bin:$PATH"
```

## V8 Build

### Install prerequisites

The following script will use `apt` to install any packages required for building v8. You will need `sudo` access to install the packages.

```bash
cd $V8_ROOT/v8
./build/install-build-deps.sh
```

Note: If the prefix of your RISC-V toolchain is not `riscv64-linux-gnu-`, please modify `toolprefix` of `gcc_toolchain("riscv64")` in `$V8_ROOT/v8/build/toolchain/linux/BUILD.gn`.

### Build v8 for RISC-V Simulation of an x86-64 Host

#### Configure with GN
The following commands build an riscv64 executable for v8 . We use gn to configure the debug build as such:

```
gn gen out/riscv64.native.debug --args='is_component_build=false is_debug=true target_cpu="riscv64" v8_target_cpu="riscv64" use_goma=false goma_dir="None" treat_warnings_as_errors=false'
```

If the binary is too large. You can add `symbol_level = 1` or `symbol_level = 0` to `./out/riscv64.native.debug/arg.gn`. If symbol_level is 0, it means there is no debug-symbol in binary. Default value is 2.


#### Build with Ninja
```
ninja -C out/riscv64.native.debug -j8
```

Note, -j8 specifies to use 8 cores for the build and should be adjusted for your build machine.

### Run on QEMU

#### Download QEMU

Clone QEMU:
```
cd $V8_ROOT/
git clone git@github.com:qemu/qemu.git
cd $V8_ROOT/qemu
git checkout v5.0.0
```

#### Build QEMU for riscv64

```
./configure --target-list=riscv64-softmmu && make -j 4
sudo make install # Optional
```

#### Download Fedora and U-Boot Images

From https://dl.fedoraproject.org/pub/alt/risc-v/repo/virt-builder-images/images/, download `Fedora-Developer-Rawhide-*.raw.xz` as well as the matching `Fedora-Developer-Rawhide-*-fw_payload-uboot-qemu-virt-smode.elf`:

```
wget https://dl.fedoraproject.org/pub/alt/risc-v/repo/virt-builder-images/images/Fedora-Developer-Rawhide-20191123.n.0-sda.raw.xz

unxz -k Fedora-Developer-Rawhide-20191123.n.0-sda.raw.xz

wget https://dl.fedoraproject.org/pub/alt/risc-v/repo/virt-builder-images/images/Fedora-Developer-Rawhide-20191123.n.0-fw_payload-uboot-qemu-virt-smode.elf
```

#### Run QEMU

In the command below, `VER` is the version number from the files you downloaded above, for example, `20191123.n.0`.

The default root password for this image is `fedora_rocks!`.

```
export VER=20191123.n.0
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

#### Copy to QEMU

First, you'll need to enable root login over ssh with password. Add the following line to _/etc/ssh/sshd_config_:

```
PermitRootLogin=yes
```

Then restart the ssh server:

```
systemctl restart sshd.service
```

Now, you can use `scp` to copy files to the emulated machine. Note that the SSH port of the machine is mapped to 3333.

```
scp -P 3333 $V8_ROOT/v8/out/riscv64.native.debug/d8 $V8_ROOT/v8/out/riscv64.native.debug/snapshot_blob.bin root@localhost:~/.
```

Now, you are ready to run `d8` inside of the emulated RISC-V platform:

```
[root@fedora-riscv ~]# cat hello.js
console.log("hello, world!");
[root@fedora-riscv ~]# ./d8 hello.js
hello, world!
```

**For more info about Fedora on RISC-V, please visit https://fedoraproject.org/wiki/Architectures/RISC-V/Installing**

#### Script for Developers in China

For developers within China, the PLCT lab provides a script to retrieve and run fedora-riscv64 from a local mirror: [deploy_riscv64fedora_qemu.sh](https://github.com/isrc-cas/PLCT-Toolbox/blob/master/deploy_riscv64fedora_qemu.sh).

Run this script:
```
./deploy_riscv64fedora_qemu.sh
```
