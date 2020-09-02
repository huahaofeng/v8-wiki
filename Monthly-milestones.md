This wiki documents our monthly project milestones. Please refer to [here](Project-Roadmap) for overall project roadmap and porting strategies.


***

## Milestones (Due 10/09/2020)

We will focus on steps to bringing V8/Nodes to the community and start on optimizing performance

### Upstream to V8

### NodeJS to Fedora (?)

### Code-gen Optimization

***

## Milestones (due 09/04/2020)

This is the first milestone since the open-sourcing of v8-riscv. 

### Native build enablement (milestone label: `native-build (09/04/2020)`)

The goal is to achieve a high success rate (95%+) on `cctest` when running v8-riscv in QEMU or HiFive board. Currently, v8-riscv simulated build achieves 94%+ success rate, but, for native/qemu runs, the success rate is much lower (see [HiFive testing status](https://github.com/v8-riscv/v8/wiki/Testing-Status#running-on-hifive-unleashed-board)). In particular, `cctest` only achieves 64% success rate on HiFive. We choose `cctest` success rate as the target because most bugs can be exposed by `cctest` and the test cases in `cctest` are much easier to debug than other test suites.

The milestone is the top priority for this month, and is tracked on the project board [here](https://github.com/v8-riscv/v8/projects/1).

### Upstream preparation (milestone label: `upstream-prep (09/04/2020)` )

Our goal is to upstream to V8 in the next two months. This milestone consists of: 
1. rebase to the latest V8 main branch; current code base was forked from (tag: 8.1.268, Date: Thu Jan 23 11:38:01)
2. test release build with stress-opt options as well as performance benchmarks
3. some code refactoring 

Item 1 is the main work. Items 2 and 3 are mostly done and not blockers.

The milestone is tracked on the project board [here](https://github.com/v8-riscv/v8/projects/3).

### Initial NodeJS bring-up (milestone label: `nodejs (09/04/2020)`)

Since NodeJS is one major application to show-case v8-riscv, we will kick-off the enablement of NodeJS this month. The goal is to 
- enable some demos of NodeJS using v8-riscv (for RISC-V Global Forum Talk)
- setup NodeJS repo and proper documentation for others to try it out
- run NodeJS test cases and create work items
 
We will define a success-rate target for this milestone after running test cases. 

The milestone is tracked on the project board [here](https://github.com/v8-riscv/v8/projects/4).