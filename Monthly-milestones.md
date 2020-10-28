This wiki documents our monthly project milestones. Please refer to [here](Project-Roadmap) for overall project roadmap and porting strategies.

- [Milestones (Due 10/09/2020)](#milestones--due-10-09-2020-)
  * [Upstream to V8](#upstream-to-v8)
  * [Performance Optimizations](#performance-optimizations)
  * [NodeJS to Fedora preparation](#nodejs-to-fedora-preparation)
- [Milestones (due 09/04/2020)](#milestones--due-09-04-2020-)
  * [Native-run enablement (milestone label: `native-run (09/04/2020)`)](#native-run-enablement--milestone-label---native-run--09-04-2020---)
  * [Upstream preparation (milestone label: `upstream-prep (09/04/2020)` )](#upstream-preparation--milestone-label---upstream-prep--09-04-2020----)
  * [Initial NodeJS bring-up (milestone label: `nodejs (09/04/2020)`)](#initial-nodejs-bring-up--milestone-label---nodejs--09-04-2020---)

<small><i><a href='http://ecotrust-canada.github.io/markdown-toc/'>Table of contents generated with markdown-toc</a></i></small>

***

## Milestones (Due 11/28/2020)

### Upstream to V8

In this milestone, we will rebase to the latest tip of V8 development branch and try to land our codebase to V8 repo

### C-extension support

Initial support for C-extensions:
- Add simulator, assembler, disasm support for C-extension instructions
- Generate C instructions in macro-assembler
- Develop tools to estimate the percentage of instructions that can be converted to C-extensions (limit study)

Future optimizations on C-extension instruction support can be handled in the next milestone
 
### Initial performance analysis

The goal of this milestone is to figure out where optimization opportunities are. We will focus on:
- Developing tools to identify inefficient code-gen (https://github.com/v8-riscv/v8/issues/291 and https://github.com/v8-riscv/v8/issues/195)
- A detailed analysis on the performance gaps identified
 
## Milestones (Due 10/09/2020)

We will focus on steps to bringing V8/Nodes to the community and start on optimizing performance

### Upstream to V8

We have satisfied most of the requirements from the previous meeting w/ V8 team (e.g., rebase, stress-opt, release build testing, refactoring). Need to go through the upstream (code review) process and resolve any issues raised. Not sure if V8 upstream requires us to cross-build using LLVM. And the actual timing of the upstream depends on input from V8 team.

V8 upstream is also the prerequisite for releasing NodeJS to Fedora.

### Performance Optimizations

Start to investigate performance and track performance numbers. We do not have specific performance goals for this month's milestone, but would like to setup the system for performance tuning and optimization.

### NodeJS to Fedora preparation

Focus on bringing up the native build (as opposed to the cross-compiled build). If there are additional dependencies, this task may take some time to complete.

***

## Milestones (due 09/04/2020)

This is the first milestone since the open-sourcing of v8-riscv. 

### Native-run enablement (milestone label: `native-run (09/04/2020)`)

**Status: Done**. Qemu/HiFive success-rate is on-part w/ simulated build and all the additional failures are reported.

The goal is to achieve a high success rate (95%+) on `cctest` when running v8-riscv in QEMU or HiFive board. Currently, v8-riscv simulated build achieves 94%+ success rate, but, for native/qemu runs, the success rate is much lower (see [HiFive testing status](https://github.com/v8-riscv/v8/wiki/Testing-Status#running-on-hifive-unleashed-board)). In particular, `cctest` only achieves 64% success rate on HiFive. We choose `cctest` success rate as the target because most bugs can be exposed by `cctest` and the test cases in `cctest` are much easier to debug than other test suites.

The milestone is the top priority for this month and is tracked on the project board [here](https://github.com/v8-riscv/v8/projects/1).

### Upstream preparation (milestone label: `upstream-prep (09/04/2020)` )

**Status: Done**

Our goal is to upstream to V8 in the next two months. This milestone consists of: 
1. [x] rebase to the latest V8 main branch; current code base was forked from (tag: 8.1.268, Date: Thu Jan 23 11:38:01)
2. [x] test release build with stress-opt options as well as performance benchmarks
3. [x] some code refactoring 

Item 1 is the main work. Items 2 and 3 are mostly done and not blockers.

The milestone is tracked on the project board [here](https://github.com/v8-riscv/v8/projects/3).

### Initial NodeJS bring-up (milestone label: `nodejs (09/04/2020)`)

** Status: Done** NodeJS passes 2000 Node tests with only 13 additional failures.

Since NodeJS is one major application to show-case v8-riscv, we will kick-off the enablement of NodeJS this month. The goal is to 
- [x] enable some demos of NodeJS using v8-riscv (for RISC-V Global Forum Talk)
- [x]setup NodeJS repo and proper documentation for others to try it out
- [x]run NodeJS test cases and create work items
 
We will define a success-rate target for this milestone after running test cases. 

The milestone is tracked on the project board [here](https://github.com/v8-riscv/v8/projects/4).