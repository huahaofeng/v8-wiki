# Overall Approach

## Porting Strategy

When we first started the project back in January, we did a thorough study of the V8 backend architecture and made the following observations
1. V8 is a sophisticated and mature language VM that has gone through several major software architectures refactoring;
2. Most architecture-specific codes constitute only a small fraction of the entire V8 code-base;
3. Unlike other language VMs, V8 code-gen involves a lot of assembly codes, and even the "interpreter" execution requires code-gen;
4. V8 already supports multiple backends (x86, arm/64, mips/64, ppc/64, s390) and all of them share a similar structure;
5. V8 provides a rich set of test cases and can test both at JS level and below JS level (e.g., `cctest` and `unittests`).

We realized the major challenge of developing the RISC-V backend is managing the complexity because 
- it is unlikely we will be able to completely understand V8 in a short time;
- the assembly codes in the backend are especially hard to comprehend; 
- debugging the generated code in V8 is especially hard compared to other compilers. 

To manage such complexity, we progressively define the Minimal Viable Product (MVP) at each phase of the porting. We also leverage as much as possible existing backends. Since MIPS heavily influences the RISC-V ISA, we choose the MIPS64 backend as the base and gradually morph it into the RISC-V backend. And finally, we constantly improve our debugging methods and develop tools (see [how to debug V8 backend](How-to-debug-V8)). 
  
## Testing strategy

Since debugging is a big challenge in developing a new backend in V8, being able to expose bugs in a minimal test case is especially important to development productivity. Therefore, we paid special attention to the order by which test cases are enabled. For instance, even a simple JS test can run tens of thousands or millions of instructions, whereas a simple `cctest` such as `test-run-machop.cc` consists of many tests, each of which runs a short sequence of instructions. So the order of test cases we enable is always `mksnapshot`, `cctest`, simple hello-world, simple `d8` console, `unittests`, `wasm`-tests, others, and finally `mjsunit`. 

# Milestones

## Phase I: basic RISC64 backend running in the simulated build

**Approach**
At this phase, we aim at developing a functioning RISC64 backend (no code-gen optimization) that can pass all V8 test cases in the simulated build. The simulated build is a unique feature of V8 that allows V8 in the X86 binary form to run on the host machine (i.e., x86), but let the code generator in V8 produce RISCV binaries. When running the generated RISCV binary, V8 automatically switches to a built-in RISCV simulator (see `src/execution/riscv64/simulator-riscv64.cc`).

This approach offers tremendous benefits in development productivity:
- The entire development life cycle (coding, testing, debugging, CI) happens on x86 and is a lot faster than running in QEMU or real hardware;
- The built-in simulator can emit disassembled instruction traces (e.g., `--trace-sim`) which combined with `--print-all-code` is our most effective debugging tool;
- It allows us to run CI as part of our DevOps for the enabled tests on a general-purpose server.

**Status** 
We are currently at the end of Phase I (see [testing status](Testing-Status)). Now, most of the known failures are triaged. One of the test cases that we are debugging, `mjsunit/asm/poppler.js`, is a large js file that runs millions of instructions. 

**Known limitations**

- The generated code quality is poor, and code size may be large as we deliberately not do any code-gen optimization to avoid introducing bugs before a working baseline is established
- Simulated build runs on x86, not on QEMU or actual hardware, so it is not yet a completely functioning solution.

## Phase II: enable 64-bit native build

In this phase, we build V8 as RISC-V binary and make V8 code-gen generate RISC-V binary as well. In principle, V8 itself is built by gcc or LLVM. So the porting is mainly to bridge the gap between native ABI and simulator's ABI and possible mismatch between simulated instruction semantics and actual hardware instruction semantics (e.g., corner cases for floating-point NaN and kInvalid bit setting), as well as additional constraints on memory available on the real hardware.

We will follow the same order of test case enablement in this step as that used in developing the simulated build in Phase I.

- Running in QEMU (Linux distribution: Debian)

Following the MPV approach, we will enable native build in QEMU first as the development environment will still be on x86. 

- Running on real board (board specifics to be decided) 

This step is another incremental step from running the native build in QEMU.

## Phase III-a: enable 32-bit support

TO BE ADDED

## Phase III-b: enhancement

- Code-gen optimization
- Support for the compact instruction set
- Support for other RISC-V ISA extensions
- SIMD ISA support especially for WebAssembly 