Our work on porting V8 to RISC-V is intended to be upstreamed to the [core project](https://github.com/v8/v8). This page outlines the progress in making that happen.

We need to follow the guidelines laid out in [Contributing to V8](https://v8.dev/docs/contribute).
* [x] We have been communicating with team members from Google about this porting work. They are supportive of our work and eager to get it upstream.
* [x] All contributors to our repository have signed the [Google Individual Contributor License Agreement](https://cla.developers.google.com/about/google-individual)
* [x] The presubmit script, `git cl presubmit`, runs successfully for our branch
* [x] The full set of [tests](https://v8.dev/docs/test) have been run on all other required architectures
* [ ] Rebase to the latest tip of branch (in progress, ported as of Oct 14, working through remaining issues)
* [ ] Upload to V8's [code review tool](https://v8.dev/docs/contribute#upload-to-v8%E2%80%99s-codereview-tool)

## Outstanding issues

* [#287](https://github.com/v8-riscv/v8/issues/287): Check failed: expect == mt.Call() (2 vs. 0)
* [#288](https://github.com/v8-riscv/v8/issues/288): Fatal error in ../../src/objects/tagged-impl.h, line 92
* [#289](https://github.com/v8-riscv/v8/issues/289): RuntimeError: unreachable
* [#290](https://github.com/v8-riscv/v8/issues/290): Seg fault

## Information

The significant change since the last rebase that caused the many bugs that showed up from this rebase effort were caused by changes to the standard frame, specifically, adding the argument count to the frame, and reversing the order of the arguments / receiver.

## Chromium Issues
* [Issue 10991: Landing RISC-V in the V8 tree](https://bugs.chromium.org/p/v8/issues/detail?id=10991)
* [Issue 1138584: Depot tools support for linux-riscv64 compile](https://bugs.chromium.org/p/chromium/issues/detail?id=1138584#c1)