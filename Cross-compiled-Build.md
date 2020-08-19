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
sudo make linux -j8
```
Note `-j8` specifies parallel build with 8 processes, and should be adjusted to the number of cores on your machines. 

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

### Configure `toolprefix`

Make sure you have followed the [Patch the Chromium Toolchain step](https://github.com/v8-riscv/v8/wiki/get-the-source#patch-the-chromium-build-toolchain) on the [Get the Source](https://github.com/v8-riscv/v8/wiki/get-the-source) page.

The patch defines  `riscv64` toolchain configurations in `$V8_ROOT/v8/build/toolchain/linux/BUILD.gn`. By default, the `toolprefix`  of `gcc_toolchain("riscv64")` is set to `riscv64-linux-gnu-`. If your system uses a different RISC-V toolchain prefix, the setting for `toolprefix` needs to be manually updated. For instance, if your RISCV toolchain has the prefix of `riscv64-unknown-linux-gnu`, then make the following changes to `$V8_ROOT/v8/build/toolchain/linux/BUILD.gn`
```
gcc_toolchain("riscv64") {
  toolprefix = "riscv64-unknown-linux-gnu"
  ...
```

### Cross-compile v8 as RISC-V binary on an x86-64 Host

#### Configure with GN

The following commands build an riscv64 executable for v8 . We use `gn` to configure the debug build as such:

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

Once qemu is brought up, you will see the following prompt:
```
Welcome to the Fedora/RISC-V disk image
https://fedoraproject.org/wiki/Architectures/RISC-V

Build date: Sat Nov 23 12:47:19 UTC 2019

Kernel 5.4.0-0.rc7.git0.1.1.riscv64.fc32.riscv64 on an riscv64 (ttyS0)

The root password is 'fedora_rocks!'.
root password logins are disabled in SSH starting Fedora 31.
User 'riscv' with password 'fedora_rocks!' in 'wheel' group is provided.

To install new packages use 'dnf install ...'

To upgrade disk image use 'dnf upgrade --best'

If DNS isn’t working, try editing ‘/etc/yum.repos.d/fedora-riscv.repo’.

For updates and latest information read:
https://fedoraproject.org/wiki/Architectures/RISC-V

Fedora/RISC-V
-------------
Koji:               http://fedora.riscv.rocks/koji/
SCM:                http://fedora.riscv.rocks:3000/
Distribution rep.:  http://fedora.riscv.rocks/repos-dist/
Koji internal rep.: http://fedora.riscv.rocks/repos/
fedora-riscv login: 
```

The default root password for this image is `fedora_rocks!`.

#### Copy V8 to QEMU

Inside the QEMU console, you'll need to enable root login over ssh with password. Add the following line to _/etc/ssh/sshd_config_:

```
PermitRootLogin=yes
```

Then restart the ssh server:

```
[root@fedora-riscv ~]# systemctl restart sshd.service
```

Now, you can use `scp` from a regular terminal on the same machine (not the qemu terminal) to copy files to the emulated machine. Note that the SSH port of the machine is mapped to 3333.

```
[joesmith@your-local-terminal] scp -P 3333 $V8_ROOT/v8/out/riscv64.native.debug/d8 $V8_ROOT/v8/out/riscv64.native.debug/snapshot_blob.bin root@localhost:~/.
```

#### Run V8 on QEMU

Now, you are ready to run `d8` inside the qemu console:

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
