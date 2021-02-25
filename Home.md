# V8-RISCV
**Welcome to the v8-riscv wiki**

This is an on-going project to enhance the RISC-V backend for the V8 JavaScript Engine. The initial port has been upstreamed (https://chromium.googlesource.com/v8/v8.git/). The RISC-V backend is fully functional and is able to run the full test suites as well has common benchmarks, but it still needs improvements for improving performance and adding features. We have established a sustainable porting methodology and development best practices, such that we feel confident to invite broader community participation. We welcome you to join our development effort. Plenty of support is still needed for a complete and high-performing V8 on RISC-V.

This repo will be the community home even though it is now available upstream. This provides us a shared space for developing larger changes here before pushing them upstream, as well as a stable branch that will always work for RISC-V, as upstream may still break the RISC-V port from time to time. For general V8 information, see [V8 Dev](https://v8.dev/). The rest of the wiki is specific to the RISC-V V8 backend.

RISC-V ISA specification is found [here](https://riscv.org/specifications/), and RISC-V standard ABI can be found [here](https://github.com/riscv/riscv-elf-psabi-doc/).

## Getting Started
- [Get the source](Get-the-Source)
- [Cross-compiled build (running on QEMU/HiFive)](Cross-compiled-Build)
- [Simulator build (for developers running on x86)](Simulator-Build)
- [Run tests](Run-Tests)
- [Build RPM packages](build-rpm-packages)

## Project Management
- [Project roadmap](Project-Roadmap)
- [Testing status](Testing-Status)
- [Work groups](Work-groups)
- [[Upstream Workflow]]

## For Developers
- [Setup VSCode](VSCode-Setup)
- [How to contribute](Contributing)
- [How to debug V8](How-to-debug-V8)

## RISC-V Backend Design Doc
- [Understand V8 backend architecture](Understand-V8-backend-architecture)
- [How to add a new instruction](How-to-add-a-new-instruction)
- How to develop a new backend

## Community operation

- [Join our Slack](https://forms.office.com/Pages/ResponsePage.aspx?id=8o_uD7KjGECcdTodVZH-3OiciJKG_BJHrqMNgnsFFqtUNlRUNEQ5QUgxNk0wVEVaTjJBTDNOMDNIQS4u)
- Attend our bi-weekly developer [Zoom Meeting](https://us02web.zoom.us/j/87641510603?pwd=d2NDcWZtdlJhdG5pQ2ZBZHl4Uk1Ndz09)

   | Meeting Info | Description |
   |-|-|
   | Next meeting | 02/03/2021 (US) |
   | Time | every other Wednesdays 5pm PT (Thursdays 9am Beijing Time) |
   | Meeting ID| 876 4151 0603 |
   | Passcode | 714793 |
   | Meeting agenda | [Meeting agenda (02/17)](Developer-sync-up-meeting-agenda) |
   | Last meeting minutes | [Meeting minutes (01/21)](Sync-up-meeting-minutes)|
- [Content sharing](Content-sharing)
