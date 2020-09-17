## Date: 09/16/2020 6pm PT

### Agenda
- Announcement (V8 global forum talk, V8 upstream prep)
- Follow-up from the last meeting
- Update on CI and Node native build (Brice)
- C-extension support discussion (Peng et al)
- Open discussion

### Highlights
- Finally, CI is restored
- Cross-building of NodeJS is streamlined, script in the repo, instruction on the wiki 
- Agreed on the partition of C-extension support among Chao, Derek, Liqiang, and Thomas

### Action items
- Prioritize failures exposed by release and stress-opt and identify critical ones (Peng, carried from last week)
- Discuss w/ Fedora team members on the next steps to resolve dependence on other tools (cipd, etc) (Brice)
- Investigate style guide and other requirements for V8 upstreaming (Brice)
- Open an issue on generating optimized codes for atomic constructs (Peng)
- For bi-weekly testing status update on the wiki, add the delta compared to the previous report (Wei)
- For the comprehensive CI tests (after a PR is approved), if there is regression, report it on Slack and notify owners of the PRs involved (Wei)

## Date: 09/02/2020 6pm PT

### Agenda
- Follow-up from the last meeting
- 09/04 milestone summary(latest testing status) (Peng et al)
- Monthly milestones for 09/07 ~ 10/09

### Discussions
- Agreed on Sept-October milestones: focus on V8 upstream, get started on performance work

### Action items
- Enable external CI cloud instance (Wei expects to complete by next Monday, otherwise pass WIP PR to Brice)
- Send instructions on how to run Node tests to Brice (Yahan)
- NodeJS setup (carried over from last meeting, Yahan/Brice)
- Automate generation of testing status and add release build results for QEMU/HiFive next time (Wei)
- Prioritize failures exposed by release and stress-opt and identify critical ones (Peng)

## Date: 08/19/2020 6pm PT

### Agenda
- New attendee introduction & announcement
- Follow-up from last meeting
- Technical content: how to debug v8 (Brice) - [slides](https://github.com/v8-riscv/v8/wiki/media/Debugging.pptx)
- 09/04 milestone progress (latest testing status) (Peng et al)
- Open discussion: optimizations (all)

### Discussions
- Heads up on September goals: optimization, C-extension, V8 upstream, v8-riscv release
- Optimization: two tracks 1) development track will focus on low-hanging performance optimizations (e.g., constant pool, function prologue/epilogue optimization, improve codegen in TurboAssembler and code-generator), 2) performance track will do some performance analysis on benchmarks (e.g., compare against MIPS64) and tools development
- C-extension discussion: 1) RISC-V is a custom modular ISA, how to detect which extensions are supported by the hardware (deal w/ the ISA fragmentation and complexity); 2) estimate the code-size benefit of C-extension

### Action items
- Enable CI on external cloud instance (need to send the result back to github via REST API) (Wei)
- Setup NodeJS repo under V8 (Brice)
- PLCT team sign Google CLA (Wei)
- Capture all qemu & HiFive board failures into issues (Wei)
- Include release build in QEMU and HiFive runs (Wei)

***

## Date: 08/05/2020

### Agenda:
- New attendee introduction
- Follow-up from last meeting
- [August milestones](https://github.com/v8-riscv/v8/wiki/Monthly-milestones) & project operation (Peng)
- Cross-compiled and native build progress (Wei et al)
- Work-group status update (all)

### Discussions:
- Github wiki and RIOS documentation site: 1) Wei mentioned that RTS and markdown can be intermixed in documents, need to figure out whether
it is possible to have markdown document working w/ RIOS's RTS documentations; 2) find tools to automatically convert from markdown to reST format
- RISCV32 support: Wei's team has experimented with building 32-bit V8 on Yocto/OpenEmbedded and encountered major toolchain issues (gdb and gcc?)

### Action items:
- Resolve the synchronization of the github wiki and RIOS documentation site  (Ye)
- Run simulated build failures in real hardware to see if they also fail in real hardware (Wei)
- Set-up a mechanism to update testing status wiki (simulated, qemu, HiFive) with latest results before the bi-weekly developers' meeting (Wei)
- NodeJS demo video fro Global Forum talk (Yahan & Brice)

--------------------------------------------
## Date: 07/22/2020 (6pm PT/9am Beijing)

### Agenda:
- Brief introduction of participant (all)
- Information sharing (Peng et al)
- Github/Slack operation rules (Brice)
- Testing status for simulated build (Peng)
- Introduction of work-groups (Peng)
- Suggestions & discussions (all)

### Information sharing:
- Our talk proposal was accepted to RISC-V Global Forum (September)
- Got feedback towards v8 upstreaming: our simulated build testing results are quite promising, suggest to do additional stress-test on compiler and optimization, release-build tests, and run the performance suite

### Action items:
- Define milestones for early September (Peng)
- CI efficiency issues not fully resolved, need help from anyone w/ experience of setting up CI (?)
- Address permissions of adding labels or re-assign issues (Brice)
- Wei's team present cross-compiled build status next time (Wei)
- Resolve how to setup wiki repo that is most convenient to us (Ye, Brice, Peng)

---
*Find uploaded materials from all meetings [here](Content-sharing)*
