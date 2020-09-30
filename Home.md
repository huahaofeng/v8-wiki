# V8-RISCV
**Welcome to the v8-riscv wiki**

This is an on-going project to develop the RISC-V backend for the V8 JavaScript Engine. After a few months of intensive development, we have built a sufficient MVP (Minimal Viable Product) for the RISC-V64 port, which currently passes over 94% (15,000+) standard V8 test cases using v8-riscv64 simulated build. We have also established a sustainable porting methodology and development best practices that we feel confident invite a broader community participation. We welcome you joining our development effort. Plenty of supports are still needed for a complete and high-performing V8 on RISC-V.

This repo will be the community home for some time before the code-base is upstream to the V8 community. For general V8 information, see [V8 Dev](https://v8.dev/). The rest of the wiki is specific to the RISC-V V8 backend.

RISC-V ISA specification is found [here](https://riscv.org/specifications/), and RISC-V standard ABI can be found [here](https://github.com/riscv/riscv-elf-psabi-doc/).

## Getting Started
- [Get the source](Get-the-Source)
- [Cross-compiled build (running on QEMU/HiFive)](Cross-compiled-Build)
- [Simulator build (for developers running on x86)](Simulator-Build)
- [Run tests](Run-Tests)

## Project Management
- [Project roadmap](Project-Roadmap)
- [Testing status](Testing-Status)
- [Work groups](Work-groups)

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
   | Next meeting | 09/30/2020 (US) |
   | Time | every other Wednesdays 6-7pm Pacific Time (Thursdays 9am Beijing Time) |
   | Meeting ID| 876 4151 0603 |
   | Passcode | 714793 |
   | Meeting agenda | [Meeting agenda (09/30)](Developer-syn-up-meeting-agenda) |
   | Last meeting minutes | [Meeting minutes (09/16)](Sync-up-meeting-minutes)|
- [Content sharing](Content-sharing)
