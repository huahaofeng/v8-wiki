a hand on guide could be found [here](https://developers.redhat.com/blog/2019/03/18/rpm-packaging-guide-creating-rpm/).

more detailed ref is [rpm-packaging-guide](https://rpm-packaging-guide.github.io/)

for the v8-riscv/v8 project, things are a little different: the source code are cross-compiled, and we just tar the binary files.

## Steps:

1. clone the v8-riscv project and cross compiled using riscv-gnu-toolchain. you get `d8` and `snapshot_blob.bin`.
2. create folders and spec file like [v8-riscv-fedora](https://github.com/isrc-cas/v8-riscv-fedora)
3. put the `d8` and other new compiled files into the destination you want.
4. check the spec file, update release notes if you like.
5. run `rpmbuild -bb d8.spec` in the v8-riscv-fedora folder. you nay need `yum install rpmdevtools` to install rpm tools.

## if you want publish

1. A good place is github release actions. you can download the rpm after the github Actions done.
2. Anyone could upload the rpm files to HTTP/FTP mirroring sites. You may need to create a sign-key to sign the files on the mirror site.
