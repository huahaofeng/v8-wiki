Our work on porting V8 to RISC-V is intended to be upstreamed to the [core project](https://github.com/v8/v8). This page outlines the progress in making that happen.

We need to follow the guidelines laid out in [Contributing to V8](https://v8.dev/docs/contribute).
* [x] We have been communicating with team members from Google about this porting work. They are supportive of our work and eager to get it upstream.
* [x] All contributors to our repository have signed the [Google Individual Contributor License Agreement](https://cla.developers.google.com/about/google-individual)
* [ ] The presubmit script, `git cl presubmit`, <s>runs successfully for our branch</s>
  * It seems this tool was not running completely before
  * I have run it correctly now and resolved most issues in #301, which is <s>pending review</s> now merged
  * Issues #299 and #300 must also be resolved
* [x] The full set of [tests](https://v8.dev/docs/test) have been run on all other required architectures
* [x] Rebase to the latest tip of branch (in progress, ported as of Oct 14, working through remaining issues)
* [x] Add all contributors to AUTHORS (see #302)
* [ ] Upload to V8's [code review tool](https://v8.dev/docs/contribute#upload-to-v8%E2%80%99s-codereview-tool)

## Related issues

### Outstanding issues
* [#299](https://github.com/v8-riscv/v8/issues/299): cfenv is unapproved
* [#300](https://github.com/v8-riscv/v8/issues/300): Spike license

### Resolved issues
* [#287](https://github.com/v8-riscv/v8/issues/287): Check failed: expect == mt.Call() (2 vs. 0)
  - Fixed
* [#288](https://github.com/v8-riscv/v8/issues/288): Fatal error in ../../src/objects/tagged-impl.h, line 92
  - Fails on MIPS also; skipped for now
* [#289](https://github.com/v8-riscv/v8/issues/289): RuntimeError: unreachable
  - Fixed
* [#290](https://github.com/v8-riscv/v8/issues/290): Seg fault
  - Fails on MIPS also; skipped for now
* [#302](https://github.com/v8-riscv/v8/issues/302): AUTHORS file
  - Fixed

## Information

The significant change since the last rebase that caused the many bugs that showed up from this rebase effort were caused by changes to the standard frame, specifically, adding the argument count to the frame, and reversing the order of the arguments / receiver.

## Upstream Issues
* [Issue 10991: Landing RISC-V in the V8 tree](https://bugs.chromium.org/p/v8/issues/detail?id=10991)
* [Issue 1138584: Depot tools support for linux-riscv64 compile](https://bugs.chromium.org/p/chromium/issues/detail?id=1138584#c1)