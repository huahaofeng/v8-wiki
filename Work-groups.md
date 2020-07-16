There is still a long way to go before we can deliver a high-performing V8 that supports a variety of RISC-V hardware with a wide range of ISA extensions. We define the following areas of work. Among them, **Testing & Tools** and **Native build enablement** are most urgently needed.

### Testing & Tools

Since debugging generated codes in V8 can be extremely time-consuming, it is especially important to expose bugs in simpler and smaller tests. V8 already provides an extensive set of test suites and testing tools (including fuzzing tools). However, we currently only test the default configuration of `tools/run_tests.py`. We should leverage the different test options of existing tools to stress tests our port to expose bugs earlier. 

On the tooling side, we have only explored a few of the tools provided by V8 such as `run_test.py` and the debugger interface. We shall also look into enabling or developing debugging tools, trace analysis tools (e.g., `tools/riscv/analyze.py`), profiling tools to further improve our productivity. Some of the tools are important to later performance optimizations as well. For instance, we consider to extend our trace analysis tools to identify inefficient sequences of instructions from the simulator traces. 

This work is immediately needed as it impacts the quality of our code-base and our daily productivity.

### Native build enablement

This work will focus on enabling RISC-V port using the native build (instead of the simulated build). We would like to first bring up native build v8-riscv in QEMU, then on real RISC-V board. The goal is to achieve a similar test success rate on the native build in on the simulated build.

This work is immediately needed because a functioning native build is the real Minimal Viable Product (MVP) that an early adopter of v8-riscv will want to try out. While the simulated build makes a lot of sense for us V8 developers, it is not the case for users of V8.

The native build also enables us to bring up more interesting applications that may not be possible under the simulated build (e.g., Node)

### Upstreaming preparation

This work focuses on bringing the quality of v8-riscv to meet V8's upstreaming criteria. We also need to rebase to the latest V8 before upstreaming.

### RISC-V 32-bit support

This work focuses on adding a new backend for RISC-V 32-bit. Following the convention of V8, 32- and 64-bit backends are completely separate backends. It involves a lot of backend development, but can leverage (copy) much of the RISC-V 64-bit backend code-base. Therefore, it better happens after we have a stable 64-bit native build. 

This work requires good developers with strong software engineering skills and be reasonably familiar with the current RISC-V backend codebase. The priority of this work depends on community needs. If there is a strong need for a 32-bit backend, we may move up the priority. 

### Performance & optimization

The optimization work involves code generation optimization to improve either performance or code size. And for performance, we can start by improving dynamic instruction counts in the simulated build, and then moving onto improving actual execution time in the native build. Code size optimization is also critical for some of the smaller devices and sometimes may make or break a particular application bring-up.

The performance work involves performance analysis and benchmarking. This is the work that would allow us to quote the performance numbers of our v8-riscv port and will continue forever as there is always a need for better performance. We may need to develop or enable tools to aid our performance debugging.

### Other ISA extensions

Currently, v8-riscv only supports the basic instruction set (RV64I/32I and M, A, F, D, Zicsr extensions) that is absolutely needed for a functioning V8 backend. Beyond that, the `C` extension (compressed instruction set) is critical in producing smaller binaries. And `V` (vector) extensions are critical for supporting interesting multi-media or ML workload through V8's WebAssembly engine.

We may also need to design the software architecture to support certain vendor-specific hardware extensions while preserving the portability of V8.

### Application bring up

This work focuses on bringing up important JS or Web Assembly workload for demo, benchmarking, or product purposes. One possible first major application to bring up is NodeJS. Certain Web Assembly applications may also be good candidate for early bring-up.
