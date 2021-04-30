_Developer meetings held every other week on Wednesday 6pm Pacific / 9pm Eastern, Thursday 9am China. All are welcome._

**[Zoom link](https://us02web.zoom.us/j/87641510603?pwd=d2NDcWZtdlJhdG5pQ2ZBZHl4Uk1Ndz09)**

---
## Date: 4/28/2021

### Agenda

- Announcements
- Community updates
- Upstreaming updates
- Action item followups
- Open PR Discussion
- Presentations for any recent/planned developments

### Minutes
- V8 upstreaming is progressing normally after Brice got the review+
- About Node.js
  - V8 in Nodes.js is now updated to V9.0.257.21. Its build is still fail. It's expected a fine CI would be set up until the next version update.(After Brice got the review+ on Apr 14, we now can do daily maintain for V8.)
  - Node.js needs native test other than simutaor test, so the final CI still depends on sutained stable hardware. Yahan and QiuJi is trying the Allwinner D1.
  - The workflow still depends on the pratice result of the above two points.(QiuJi todo)
- QiuJi had submitted a lightening talk proposal for the RISC-V Forums, also a talk proposal for RVSC in Shanghai.

### Action Items

- [ ] Update wiki with Node.js workflow (Qiuji)
- [ ] Update wiki with new upstreaming workflow (Brice)

## Date: 4/14/2021

### Agenda

- Announcements
- Community updates
- Upstreaming updates
- Action item followups
- Open PR Discussion
- Presentations for any recent/planned developments

### Minutes

- A member from the MIPS V8 backend team joined us today to listen in
- Node.js now has a RISC-V target!
- QiuJi plans to submit a talk for the RISC-V Forums and is also preparing another talk for a RISC-V event in Shanghai
- Follow up on action items from last time
  - [x] Move 2021 goals in the issue to wiki (Jiqiu)
    - [2021 Goals](2021-Goals)
  - [x] Arrange a zoom meeting w/ V8 MIPS RISCV backend to share tips and connect community (Jiqiu)
    - They recommended that the best way to gain privileges in the V8 upstream is to just continue to submit useful patches
    - One member joined us today
  - Update process workflow w/ consideration of NodeJS's dependency on V8 release branch (Jiqiu)
    - Node.js seems relatively easier to get patches accepted
    - They only test x64, so there are no guarantees for other architectures
    - We need to clarify the process to update V8 within Node.js, but we assume we need to get a patch upstream in V8, then get that pulled in to Node.js
    - Qiuji will work on setting up our own CI for this
    - Qiuji will update the workflow on the wiki
  - Outreach to users of V8 to arrange individual social hours for community outreach (Wei)
    - Wei is working on this
    - Has posted a new job opening, but not much response yet
- PLCT is preparing a technical training session and would like other team members to do a guest lecture
  - This is a series of lectures, starting now and going every 2 weeks
  - Typical discussion is 1 hour, so a 40-50 minute talk followed by discussion would be good
- Upstreaming workflow
  - Since we can now quickly approve our own PRs upstream, the old process using our GitHub is no longer needed
  - We should maintain this repo to ensure that there is always a stable branch with RISC-V support, since upstream will often break RISC-V support
  - We will use the `riscv64` branch for this
  - Others may want to continue to keep development branches in this repo while in progress, but this is not a requirement
  - Brice will update the wiki with these changes

### Action Items

- [ ] Update wiki with Node.js workflow (Qiuji)
- [ ] Update wiki with new upstreaming workflow (Brice)

## Date: 3/31/2021

### Agenda

- Announcements
- Community updates
- Upstreaming updates
- Action item followups
- Open PR Discussion
- Presentations for any recent/planned developments

### Minutes

- [PLCT updates](media/DeveloperMeeting-20210401.pptx)

### Action Items

- Move 2021 goals in the issue to wiki (Jiqiu)
- Arrange a zoom meeting w/ V8 MIPS RISCV backend to share tips and connect community (Jiqiu)
- Update process workflow w/ consideration of NodeJS's dependency on V8 release branch (Jiqiu)
- Outreach to users of V8 to arrange individual social hours for community outreach (Wei)

## Date: 3/17/2021

### Agenda

- Announcements
- Community updates
- Upstreaming updates
- Action item follow-ups
- Open PR Discussion
- Presentations for any recent/planned developments

### Minutes

- Announcements

  - Initial commits have been merged
  - Sparkplug patch has been commited
  - Work on node.js has been started and initial builds have passed
  - v8 repo has been moved under riscv/v8

- Community Updates

  - Managed runtimes group (java specific) sent out call for chair and co-chair
  - RISC-V foundation has setup a basic infrastructure for the specification and software
  - There is talks about setting up a group for performance modeling and performance profiling tools

- Upstreaming

  - still waiting to get a proper privilege to thumbs up code.

- [PLCT updates](media/DeveloperMeeting-20210318.pptx)

### Action Items

- Changes to the CI in regardes to the master, riscv64, and riscv64-dev branches
- Addition of architecture flags for compilation

### Other

- Working with RISC-V International to get internship positions for v8.

## Date: 3/3/2021

### Agenda

- Announcements
- Community updates
- Upstreaming updates
- Action item follow-ups
- Open PR Discussion
- Presentations for any recent/planned developments

### Minutes

- Announcements

  - The initial support of SparkPlug for RISC-V is [done] (https://github.com/riscv/v8/tree/riscv-sparkplug-dev)
  - tensorflow.js can run a the simple [demo] (https://www.tensorflow.org/js/tutorials/setup) on the [RISC-V node.js] (https://github.com/riscv/node/)

- Upstreaming

  - still waiting to get a proper priviledge to maintain and review+ the RISC-V arch files

- Other updating
  - [PLCT updates](media/DeveloperMeeting-20210304.pptx)
  - [Test status](https://github.com/riscv/v8/wiki/Testing-Status) in wiki page is updated.
  - The github issue and prs had been reviewed and cleaned up
  - C-ext support can run benchmark

### Action Items

- check and fix issue #53, #166, #297, #403, #414
- test result for SparkPlug
- moving on of C extension merge and evaluate

## Date: 2/17/2021

### Agenda

- Announcements
- Community updates
- Discuss workflow after upstreaming (#442)

### Minutes

- Announcements
  - The Chromium team has [released SparkPlug](https://bugs.chromium.org/p/v8/issues/detail?id=11420):
    > Sparkplug is a new baseline, non-optimising second-tier compiler, designed to fit in the compiler trade-off space between Ignition and TurboProp/TurboFan.
- Community updates
  - Chairs for all RISC-V task groups are in a renominating process
  - RISC-V is holding several Forums in April and May - [Events](https://riscv.org/events/category/riscv-events/)
  - RISC-V has some time slot at [ISC](https://www.isc-hpc.com)
  - Software has two new TGs
    - Performance modeling tools
    - Performance profiling and monitoring tools
  - RISC-V is pushing extension/ISA groups to ensure that there are no encumbered (patents, copyrights) items in their proposals
  - Brice is working with Sanhong from Alibaba on a charter for the Managed Runtimes group
    - Will share with the team for input (on Slack)
  - It seems progress on JDK upstreaming is very slow
- Upstreaming status
  - Yahan and Brice now have access to start trybots for our Gerritt reviews
    ```
    # start default trybots
    git cl try
    # start RISC-V trybots
    git cl try -B luci.v8.try -b v8_linux64_riscv64_rel_ng
    ```
  - Brice requested port committer status for some of us, so that we may apply the “Code-Review +1” label (equivalent of a LGTM)
    - Waiting to hear back on that
    - We will need to wait for a full committer to approve changes for now
- Discuss workflow after upstreaming (#442)
  - We will plan to make changes in our GitHub repo (this one), do our own code reviews here, then open upstream pull requests on Gerritt
  - If others submit changes upstream, we should review them, and also encourage the developers to join our group
  - How should we keep up-to-date with upstream?
    - Mirror upstream into master branch automatically
    - Run our regression tests on master to quickly identify problems caused by upstream changes
    - Rebase riscv64 branch regularly on to master
  - Brice will create a wiki page to describe the process of how we should stay in sync with upstream and the process that developers should follow to start new work
  - Qiuji will triage the issues from upstream and assign them to the team as needed
- Derek suggested pushing the C extension changes without jumps as a separate pull request
  - Agreement from the group

### Action Items

- [ ] Create wiki page to describe new process for working with upstream - Brice
- [ ] Open merge request for C extension (without jumps) - Derek

## Date: 1/20/2021

### Agenda

- Announcements
- Community updates
- Upstreaming updates
- Action item follow-ups
- Open PR Discussions
- Presentations for any recent/planned developments
- Miscellaneous

### Minutes

- Community Updates:

  - There will be a RISC-V retreat on 1/27/2021 and 1/28/2021 Beijing time. Derek and Chao will showcase running V8 on PicoRio hardware.

- Upstreaming Updates:

  - Current rebase has some build failures.
  - The planned current PRs that will be pushed in to master will be [#379](https://github.com/riscv/v8/pull/379), [#384](https://github.com/riscv/v8/pull/384), [#393](https://github.com/riscv/v8/pull/393).
  - Let Brice know if there are any PRs you would like to be added.

- Updates from Wei about RVI meetings:

  - There are plans for a performance tracking system on the compiler side.
  - There will be another bi-weekly meeting 1/21/2021 afternoon Beijing time. Wei will update us on what he learns there.
  - J-Extension group work is currently focusing more on security.

- Miscellaneous
  - In future meetings we will be moving presentations to the start.
  - Wei would like to test using Qemu's User Mode for increased regression testing speeds.

### Action Items

- upstreaming: fix build issue and push in PRs.
- Setup PTS for V8 (@ww)
- Test Qemu's User Mode (@ww)
- Continue c-ext support work(@Derek Tu)

## Date: 1/7/2021

### Agenda

- Announcements
- Community updates
- Upstreaming updates
- Action item follow-ups
- Discussion about the Wishlist 2021 for V8 for RISC-V project
- Present recent develop progress status if any(RVV-Wasm support, Performance analyze vs ARM64 etc.)

### Minutes

- Upstreaming issues:

  - need to separate RV32 modifies from the current RV64 modifies(apply to Georg Neis’s comments)

- Updates from Wei about RVI meetings:

  - PLCT will set up the Performance Tracking System(PTS) for the Code speed group. V8 will be the first one put to the target software.

- Action items follow-ups:

  - issue370 need to be checked with OS guys to see how to flush icache for JIT.
  - issue 372/373/375/376/378 need to be clarified(low priority)
  - pr309 need to be closed. pr379 need to be reviewed

- Wishilist for 2021 is discussed and priority confirmed:

  - upstream(1st)
  - node.js support(2nd)
  - wasm and JS speedup(3rd)
  - ISA extension support(4th)

- Yahan introduced progressing for Wasm-RVV. He adds new register type declaration for the non-overlayed vector registers.
- Qiuji reported some result for performance evaluation of V8-RISCV. The result is comparing to ARM64.[slides](media/performance-opt-step1-find-baseline.pptx)

### Action Items

- upstreaming: apply to Georg Neis’s comments

- clean up github issues and prs:

  - issue370(@qiuji)
  - pr309(@taoliqiang)
  - pr379(@luyahan)
  - issue 372/373/375/376/378(@luyahan & @qiuji)
  - issue258 (give a investigate report to it @qiuji & @taoliqiang)

- Setup PTS for V8 (@ww)
- Re-start c-ext support work?(@Derek Tu)

## Date: 12/23/2020

### Agenda

- Announcements
- Community updates
- Upstreaming updates
- Action item followups
- Review open PRs and determine next actions

### Minutes

- We will start rotating hosts for the meeting between PLCT, RIOS, Peng, and Futurewei
  - PLCT lab will host the next one
- Updates from Wei about RISC-V International meetings
  - Code speed group is approved
  - Performance tracking task group will define scripts to run benchmarks and post results to a website
  - Plan to build Java and JS speed task groups
- Yahan shared a roadmap for adding the V extension support to V8 [slides](media/add_rvv.pdf)
  - Defined 2 phases: prepare and implement
  - Discussed timing and testing of this work
  - Probably a subset of the V extension will be sufficient to support the WebAssembly instructions, so be sure to focus on this first
- Brice completed the release of the RPMs
  - We may want to trigger releases for specific tags in addition to the LATEST
  - Perhaps use a special prefix
- Derek announced that RIOS will hold a student led summit on Jan 20 where he will demonstrate V8 on a PicoRio
  - He will pause work on the C extension until after this
- Wei discussed setting goals for 2021
  - Speed up V8 on RV64GC
  - Support V extension, C extension, B extension
  - Node.js
  - Peng brought up real world usage, including browser, ML (tensorflow.js)
- After upstreaming, we will need to update our workflow
  - Will likely need to keep branches for unratified extensions in our repo, not upstream

### Action Items

- Review open issues and close or update outdated ones (all)
- Review open PRs and rebase or address open comments (Nekhlyudov, Yahan, Derek, Liqiang)
- Provide some input to Wei about goals for 2021 (Peng, Reza, Brice)

## Date: 12/09/2020

### Agenda

- Announcement
- V8 re-base update and outstanding issues (Brice)
- RISC-V community news (Wei)
- Follow-up from the last meeting
- Technical presentation: [register allocator study](media/Register-Allocator-in-V8.pdf) (Qiuji)
- Open discussions

### Action items

- Setup GitHub Actions to add release builds - @Brice
- Give Qiuji code review permission - @Brice

## Date: 11/11/2020

### Agenda

- V8 re-base update and outstanding issues (Brice)
- RISC-V community news (Wei)
- Follow-up from the last meeting
- Technical presentation: J-extension group pointer-masking proposal and analysis on V8 (Peng)
- Open discussions

### Highlights

- On the pointer-masking discussion, the intuitive feeling is that V8 (a managed runtime w/ already a lightweight isolation mechanism) may not be a good candidate for pointer-masking (i.e., because the software solution already provides pretty good isolation protection, and using a HW solution is a lot more rigid). We decide to be more explicit in voicing this opinion to the J-extension workgroup.

### Action items

- Add a release wiki page on how people can gain access to pre-built v8 binary
- Give a presentation on the algorithm to extend V8 register allocator for C-extension instructions (qiu ji)

## Date: 10/28/2020 6pm PT

### Agenda

- Announcement
- V8 re-base update (Brice on new problems discovered and outstanding issues)
- Follow-up from the last meeting
- Technical presentation: how does V8's register allocator work? (Derek & Liqiang)
- Technical presentation: how to support C-extension instruction code gen (Reza)
- Technical presentation: preliminary performance analysis (Wei)
- Technical presentation: J-extension group pointer-masking proposal and analysis on V8 (Peng) [if time permits]
- Open discussions

### Action Items

- Participate in the code-speed workgroup meetings (Wei, Brice, Peng)
- Give a presentation to compare the J-extension proposal and V8's compressed-pointer implementation (did not have time to cover this week, Peng)
- Setup 3rd party package distribution of our cross-compiled V8/Node binary (carried over from the last meeting, Wei)
- Write up the next steps for the register allocation study for Liqiang & Derek (Peng)
- Share today's slides to repo wiki (https://github.com/riscv/v8/wiki/Content-sharing) (liqiang, reza, yahan)
- Present the lgorithm to extend V8 register allocator for C-extension instructions (liqiang, reza, derek)

## Date: 10/14/2020 6pm PT

### Agenda

- Announcement (J-extension group)
- V8 upstream update (Brice: rebase, cpid, using gcc tool-chain)
- Follow-up from the last meeting
- Additional C-extension support discussion (Peng et al)
- Open discussions

### Highlights

- Wei updated us the recent reorg/changes in RISC-V international taskgroup organization, there may be opportunities for us to take some initiatives or participate in other workgroups
- Discussed how to generate C-extension instructions in macro-assembler: may need to teach register allocator to generate instructions that satisfy constraints of C-extension instructions (i.e., smaller groups of registers and reusing 1st operand as destination register)

### Action items

- Update testing status for this week (Wei)
- Attend code-size optimization workgroup (Peng, Wei)
- Add a wiki page to summarize how our codebase satisfies the upstream criteria (Brice)
- Give a presentation to compare J-extension proposal and V8's compressed-pointer implementation (carried over from last week, Peng)
- Setup 3rd party package distribution of our cross-compiled V8/Node binary (carried over from last week, Wei)
- Give a presentation on how V8 register allocator works (Liqiang and Derek)
- Define milestone for this month, especially define the performance analysis requirement (Peng)
- Give a presentation on benchmark performance comparison between RISC-V and MIPS64 (Wei?)
- Create issues for the next steps of C-extension implementation and assign (Peng)

## Date: 09/30/2020 6pm PT

### Agenda

- Follow-up from the last meeting
- Open discussion

### Highlights

- Many test failures are resolved compared to status of two-weeks ago
- We discussed how to find a reasonable path to distribute V8/Node binary to end-users

### Action items

- Check w/ V8 team whether building w/ LLVM is a requirement for upstream (Peng)
- Define exactly what needs to be done to solve the tools dependency for native build (required by both Debian and Fedora) and open an issue to track it (Brice)
- Setup a 3rd party package distribution of our cross-compiled V8/Node binary (Wei)
- Present a comparison of J-extension's pointer-masking proposal and V8's compressed pointer feature (Peng)

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
- Technical content: how to debug v8 (Brice) - [slides](media/Debugging.pptx)
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

---

## Date: 08/05/2020

### Agenda:

- New attendee introduction
- Follow-up from last meeting
- [August milestones](https://github.com/riscv/v8/wiki/Monthly-milestones) & project operation (Peng)
- Cross-compiled and native build progress (Wei et al)
- Work-group status update (all)

### Discussions:

- Github wiki and RIOS documentation site: 1) Wei mentioned that RTS and markdown can be intermixed in documents, need to figure out whether
  it is possible to have markdown document working w/ RIOS's RTS documentations; 2) find tools to automatically convert from markdown to reST format
- RISCV32 support: Wei's team has experimented with building 32-bit V8 on Yocto/OpenEmbedded and encountered major toolchain issues (gdb and gcc?)

### Action items:

- Resolve the synchronization of the github wiki and RIOS documentation site (Ye)
- Run simulated build failures in real hardware to see if they also fail in real hardware (Wei)
- Set-up a mechanism to update testing status wiki (simulated, qemu, HiFive) with latest results before the bi-weekly developers' meeting (Wei)
- NodeJS demo video fro Global Forum talk (Yahan & Brice)

---

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

_Find uploaded materials from all meetings [here](Content-sharing)_
