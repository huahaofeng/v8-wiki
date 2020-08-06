Date: 08/05/2020

Agenda:
- New attendee introduction
- Follow-up from last meeting
- [August milestones](https://github.com/v8-riscv/v8/wiki/Monthly-milestones) & project operation (Peng)
- Cross-compiled and native build progress (Wei et al)
- Work-group status update (all)

Discussions:
- Github wiki and RIOS documentation site: 1) Wei mentioned that RTS and markdown can be intermixed in documents, need to figure out whether 
it is possible to have markdown document working w/ RIOS's RTS documentations; 2) find tools to automatically convert from markdown to reST format
- RISCV32 support: Wei's team has experimented with building 32-bit V8 on Yocto/OpenEmbedded and encountered major toolchain issues (gdb and gcc?)

Action items:
- Resolve the synchronization of the github wiki and RIOS documentation site  (Ye)
- Run simulated build failures in real hardware to see if they also fail in real hardware (Wei)
- Set-up a mechanism to update testing status wiki (simulated, qemu, HiFive) with latest results before the bi-weekly developers' meeting (Wei) 
- NodeJS demo video fro Global Forum talk (Yahan & Brice)

--------------------------------------------
Date: 07/22/2020 (6pm PT/9am Beijing)

Agenda:
- Brief introduction of participant (all)
- Information sharing (Peng et al)
- Github/Slack operation rules (Brice)
- Testing status for simulated build (Peng)
- Introduction of work-groups (Peng)
- Suggestions & discussions (all)

Information sharing:
- Our talk proposal was accepted to RISC-V Global Forum (September)
- Got feedback towards v8 upstreaming: our simulated build testing results are quite promising, suggest to do additional stress-test on compiler and optimization, release-build tests, and run the performance suite  

Action items:
- Define milestones for early September (Peng)
- CI efficiency issues not fully resolved, need help from anyone w/ experience of setting up CI (?)
- Address permissions of adding labels or re-assign issues (Brice)
- Wei's team present cross-compiled build status next time (Wei)
- Resolve how to setup wiki repo that is most convenient to us (Ye, Brice, Peng)