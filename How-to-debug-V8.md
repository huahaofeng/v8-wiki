# Overall strategy

- Simplify the test cases
- Identify the ground truth
- Find the lead
- Map execution trace back to V8 source code

## Build options for debugging

When running `d8` or `cctest` in `gdb`, it will be useful to add the flag `v8_optimized_debug = false` to the GN arguments and rebuild. Without this, the code is optimized by default and will be difficult to debug.

## Useful flags for debugging

- `--single-threaded`: disable background threads
- `--jitless`: disable JIT code generation

# Debugging under simulated build run

## Use simulator trace (`--trace-sim`)

V8's simulated build is not only convenient for developing the backend for a new ISA on a host machine, but also helpful for debugging target binary on the host machine. The latter is enabled by `--trace-sim`, which dumps out all instructions executed using V8's built-in simulator as well as the register states.

```
cctest test-bytecode-generator/StaticClassFields --trace-sim > trace.log
```

A typical trace looks like the following. The number in `()` (e.g., `(49)`) indicates the instruction count. And the hex number before the instruction count is the value in the target register (e.g., `s3` in this instruction) in hex. And the `type:value` pair following the instruction count is the value of target register in decimal (for integer types) or floating-point (for float).

```
  0x55b90866a9e0   00200993       li        s3, 2                 0000000000000002    (49)    int64:2 uint64:2
  0x55b90866a9e4   00000463       beq       zero_reg, zero_reg, 8    
  0x55b90866a9ec   ff810113       addi      sp, sp, -8            00007fe83b181ee0    (51)    int64:140635400576736 uint64:140635400576736
  0x55b90866a9f0   01313023       sd        s3, 0(sp)                                 (52)    int64:2 uint64:2 --> [addr: 7fe83b181ee0]
  0x55b90866a9f4   01c0006f       j         28                    0000000000000000    (53)    int64:0 uint64:0
  0x55b90866aa10   00000e13       mv        t3, zero_reg          0000000000000000    (54)    int64:0 uint64:0
```
## Use simulator debugger (`--stop-at-sim`)

The simulator embedded in V8 has a built-in debugger. The debugger can be invoked by `--stop-sim-at <n>`. For instance, the following command will enter the simulator mode after executing 100 RISCV instructions in the simulator (as shown by the `sim>` prompt).
```
cctest test-bytecode-generator/StaticClassFields --stop-sim-at 100
  0x000055937ba80000   009784b3       add       s1, a5, s1
sim> disasm
  0x55937ba80000   009784b3       add       s1, a5, s1
  0x55937ba80004   00000c63       beq       zero_reg, zero_reg, 24
  0x55937ba80008   0007b903       ld        s2, 0(a5)
  0x55937ba8000c   00878793       addi      a5, a5, 8
  0x55937ba80010   00093903       ld        s2, 0(s2)
  0x55937ba80014   ff810113       addi      sp, sp, -8
  0x55937ba80018   01213023       sd        s2, 0(sp)
  0x55937ba8001c   fe9796e3       bne       a5, s1, -20
  0x55937ba80020   fa0b3703       ld        a4, -96(s6)
  0x55937ba80024   00070793       mv        a5, a4
```
One can type `help` under the prompt to get a list of commands supported by the debugger:
```
sim> help
cont
  continue execution (alias 'c')
stepi
  step one instruction (alias 'si')
print <register> (e.g., `print a0`)
  print register content (alias 'p')
  use register name 'all' to print all GPRs
  use register name 'allf' to print all GPRs and FPRs
printobject <register>
  print an object from a register (alias 'po')
stack [<words>] (e.g., `stack 5`)
  dump stack content, default dump 10 words)
mem <address> [<words>] (`mem 0x234567 5`)
  dump memory content, default dump 10 words)
disasm [<instructions>] (e.g., disasm 30)
disasm [<address/register>] (e.g., disasm pc) 
disasm [[<address/register>] <instructions>]
  disassemble code, default is 10 instructions
  from pc (alias 'di')
```

Note: commands `break`, `stop`, and `gdb` are not yet supported.

## Generate debugging code in generated codes

Sometimes we want to see if an execution follows a particular code path. For instance, in the following example, a `Trap` instruction is inserted to the code path following the label `stack_overflow`. If the execution does reach this path, it will enter the simulator debugger (i.e., entering prompt `sim>`).
```
    __ bind(&stack_overflow);
    __ Trap(); // trap if execution reaches here
    // Restore the context from the frame.
    __ Ld(cp, MemOperand(fp, ConstructFrameConstants::kContextOffset));
    __ CallRuntime(Runtime::kThrowStackOverflow);
```

Other debugging APIs from `TurboAssembler` are:
```
void TurboAssembler::Trap();
void TurboAssembler::Assert(Condition cc, AbortReason reason, Register rs, Operand rt);
void TurboAssembler::Check(Condition cc, AbortReason reason, Register rs, Operand rt); 
void TurboAssembler::Abort(AbortReason reason);
```

## Flags summary

`d8` flags needed:
- `--trace-sim`: print out disasm of instructions and content of target register after each simulated instruction
- `--debug-riscv`: Print instructions as they are emitted; print other debug info from assemblers
- `--stop-sim-at <n>`: enter debugger embedded inside the simulator after `<n>` instructions

# Connect simulated trace to generated code

## Use `print-all-code`

This is a very useful flag when debugging built-in functions. It dumps all codes generated by the compiler including built-in functions and user-level codes. Since there are a couple of thousands of built-in functions, it is recommended to redirect output to a file when using the flag (otherwise your VSCode remote window may freeze over too much stdout output).

```
cctest test-bytecode-generator/StaticClassFields --print-all-code > t.all-code
```

Here are some useful flags for tracing V8 internals (complete flags use `d8 --help`):
- `--print-bytecode`: print Ignition interpreter bytecode
- `--print-code` and `--code-comments`: printout generated final code (w/ additional comments)
- `--print-all-code`: will printout all generated codes (incl. builtins) w/ all verbose options, a lot of output so better redirect to a file
- `--use-verbose-printer` (combined w/ `print-code`, `print-builtin-code`): print out 
- `--print-wasm-code`, `--print-wasm-stub-code`, `--code-coments`: printout wasm code generated and wasm native code gen
- `--print-builtin-code` and `--print-builtin-size`: print out disasm of generated builtin functions or their instruction sizes

## Use code comments

Code comments may be useful to identify the locations where specific assembly code came from. When code comments are enabled, comments inserted at the high level will translate into comments in the output with `--print-all-code`. For example, if I want to see the code coming from `CollectionsBuiltinsAssembler::SameValueZeroHeapNumber` (builtins-collections-gen.cc), I may insert the `Comment` shown below at the beginning of the functions definition:

```cpp
void CollectionsBuiltinsAssembler::SameValueZeroHeapNumber(
    TNode<Float64T> key_float, TNode<Object> candidate_key, Label* if_same,
    Label* if_not_same) {
  Comment("SameValueZeroHeapNumber");
  Label if_smi(this), if_keyisnan(this);

  GotoIf(TaggedIsSmi(candidate_key), &if_smi);
  GotoIfNot(IsHeapNumber(CAST(candidate_key)), if_not_same);
  ...
}
```

The code comments will not appear unless the appropriate flag is used. For code compiled at run-time, use the flag `--code-comments`. For code that is pre-compiled in the snapshot, add the following flag to your build configuration (in `args.gn`), then rebuild.

```
 v8_enable_snapshot_code_comments = true
```

Now, when I run my test with `--print-all-code`, I can search for "SameValueZeroHeapNumber" to identify the PC where this line was generated:

```
 0x563e95e60628  4188  0005169b       slliw     a3, a0, 0
 0x563e95e6062c  418c  0006d69b       srliw     a3, a3, 0
                   SameValueZeroHeapNumber
 0x563e95e60630  4190  fad43423       sd        a3, -88(fp)
 0x563e95e60634  4194  0016f993       andi      s3, a3, 0x1
```

These PCs can then be mapped to the output from `--trace-sim`, to track the code and identify the bug.

## Trace Analyzer

We have developed a tool to help analyze a simulator trace. It is available in the repository at [tools/riscv/analyze.py](/swlab/swe/v8/blob/riscv-porting-dev/tools/riscv/analyze.py). It is designed to read the output of a simulation run with the flags `--print-all-code` and `--trace-sim`. Write this output to a file, as in this example:

```
$ ./cctest --print-all-code -trace-sim test-interpreter-intrinsics/Call &> out
```

The tool takes that file as input and uses it to generate a call tree:

```
$ ../../tools/riscv/analyze.py out
### Start in JSEntry
###   Call JSEntryTrampoline 70
###     sp=0x7fd46befbed0 fp=0x7fd46befbf00
###     Args: 0x55b749fba710 0x2e3f480471 0x6789a9c129 0x2269200121 0x0 0x0 ? ?
###   Jump to JSEntryTrampoline 79
###     sp=0x7fd46befbed0 fp=0x7fd46befbf00
###     Args: 0x55b749fba710 0x2e3f480471 0x6789a9c129 0x2269200121 0x0 0x0 ? ?
###     Call Call_ReceiverIsAny 116
###       sp=0x7fd46befbea8 fp=0x7fd46befbec0
###       Args: 0x0 0x6789a9c129 0x6789a9c129 0x2e3f480471 0x2e3f480471 0x2e3f480471 ? ?
###     Jump to Call_ReceiverIsAny 125
###       sp=0x7fd46befbea8 fp=0x7fd46befbec0
###       Args: 0x0 0x6789a9c129 0x6789a9c129 0x2e3f480471 0x2e3f480471 0x2e3f480471 ? ?
###     Jump to CallFunction_ReceiverIsAny 137
###       sp=0x7fd46befbea8 fp=0x7fd46befbec0
###       Args: 0x0 0x6789a9c129 0x6789a9c129 0x2e3f480471 0x2e3f480471 0x2e3f480471 ? ?
###     Jump to CallFunction_ReceiverIsAny 146
...
###       Return from StackCheckHandler 1576
###         Returned:  0x2269203f81 0x50
###     Return from Call_ReceiverIsAny 1596
###       Returned:  0x2269203f81 0xab
### WARNING: Expected stack pointer = 140550320668328, actual = 140550320668336
###   Return from JSEntryTrampoline 1600
###     Returned:  0x2269203f81 0xab
### Return from JSEntry 1650
###   Returned:  0x2269203f81 0x0
```

As you can see in the example output above, the tool detects calls and jumps to functions and reports the name of the function called, then dumps the stack and frame pointers, and argument registers (a0-a7). a `?` indicates that a value has not yet been seen for this register. Upon detecting a return, the comment is printed and the return value registers (a0 and a1) are printed. The following checks are also performed at a return:

* The return address should match the original return address from the call-site
* The stack pointer should be reset to the original stack pointer from the call-site
* The frame pointer should be reset to the original frame pointer from the call-site

You may use the `-help` flag to see the current list of available flags:
```
usage: analyze.py [-h] [--inline] [--target TARGET] [--print-host-calls]
                  logfile

positional arguments:
  logfile

optional arguments:
  -h, --help          show this help message and exit
  --inline            Print comments inline with trace
  --target TARGET     Specify the target architecture
  --print-host-calls  Print info about calls to host functions
```

* `--inline` is useful to see the comments inline with the original lines of the simulator trace, to use the comments as a starting place to dig deeper.
* `--target` allows users to specify the target architecture, which is `riscv` by default, but also supports `mips`. This is useful for comparing the run of RISC-V against MIPS.
* `--print-host-calls` enables tracking calls/returns from host functions. These are ignored by default since we do not have information about the callee.

# Dump TF compiler IR via Turbolizer

Turbolizer is a tool to visualize generated TF IR from `--trace-turbo`. 

## Generate TF sea-of-nodes

`d8` flags needed:
- `--trace-opt`, `--trace-turbo`: trace Turbofan compiler, dump IR/code after each transformation into a generated `.json` or `.cfg` file
- `--trace-turbo-path=xxx` to dump the generated file to a specified directory

V8 (d8) can generate TF IRs to json files when `--trace-turbo` is specified. Each TF compiled function will produce one json file. 

For instance, the following command will generate `turbo-0x591b1bfd0-0.json` which contains TF IR/nodes generated by different phases of TF compile:
```
> out.gn/riscv.gcc.simulated.debug/cctest test-run-jsops/BinopAdd --trace-turbo
Concurrent recompilation has been disabled for tracing.
---------------------------------------------------
Begin compiling method  using TurboFan
---------------------------------------------------
Finished compiling method  using TurboFan
```
To generate turbo-trace for built-in functions, `--trace-turbo` and `--trace-turbo-path=xxx` need to be specified 'mksnapshot' stage. One can specify a directory for these json files via `--trace-turbo-path=xxx`. For instance:
```
cd out.gn/riscv.build/
mkdir turbo-dump
./mksnapshot -turbo_instruction_scheduling --target_os=linux --target_arch=x64 --embedded_src gen/embedded.S --target_is_simulator  --embedded_variant Default --random-seed 314159265  --native-code-counters --verify-heap --trace-turbo --trace-turbo-path=turbo-dump
```
**NOTE**: if you specify a `trace-turbo-path`, make sure the specified directory is created, otherwise the JSON files are not generated.

## Setup turbolizer

Please follow the instructions [here](https://lukeolney.me/posts/v8-turbolier/), but in a nutshell, it involves two steps
1. Setup turbolizer
  ```
  cd tools/turbolizer
  npm I
  npm run-script build
  ```
  Note that I have to do an `do-release-update` on my server so that my `node` and `npm` met the version requirements of `turbolizer`

2. Start turbolizer as an HTTP server
  ```
  cd tools/turbolizer
  python -m SimpleHTTPServer
  ```
3. Access Turbolizer through your browser, e.g., `http://10.124.206.247:8000/`
This will bring up the web interface of the turbolizer. Use 'Ctrl+L' to load the json file from your local directory.

Note: since my V8 environment is on a remote server, the turbolizer is running on the server, but the json file needs to be loaded from my laptop to the browser, I have to `scp` the generated json file to my local machine, then load it to Turbolizer web interface


# JS-level debugging

## Use `%OptimizeFunctionOnNextCall` to force JIT

To trigger JIT compilation at the first invocation of a JS method insert `%OptimizeFunctionOnNextCall(JSFuncName);` to JS codes and invoke `d8` with `--allow-natives-syntax`.

For example, the following code can be run w/ `v8/out/x64.release/d8 simple.js --trace-opt --allow-natives-syntax –print-bytecode –print-code`.

```JS
// simple.js
const INCREMENT = 1;
function incr(x) { return x + INCREMENT; }
%OptimizeFunctionOnNextCall(incr); 
console.log(incr(5))
```

Here are some useful flags for tracing V8 internals:
- `--jitless`: disable JIT

## DebugPrint

In the JavaScript code, we can insert calls to `DebugPrint`, which will dump various pieces of useful information about objects. To use it, we use a special syntax, prefixed with `%` to access this "native syntax":

```js
  var s = new Set([x]);
  %DebugPrint(s);
```

When we run the code, then we need to use the flag `--allow-natives-syntax`, and then we will see output like the following:

```
DebugPrint: 0x717ba952c1: [JSSet]
 - map: 0x00538e501441 <Map(HOLEY_ELEMENTS)> [FastProperties]
 - prototype: 0x00697d78ac99 <Object map = 0x538e501489>
 - elements: 0x007a63740b29 <FixedArray[0]> [HOLEY_ELEMENTS] - table: 0x00717ba952e1 <OrderedHashSet[13]>
 - properties: 0x007a63740b29 <FixedArray[0]> {}
0x538e501441: [Map]
 - type: JS_SET_TYPE
 - instance size: 32
 - inobject properties: 0
 - elements kind: HOLEY_ELEMENTS
 - unused property fields: 0
 - enum length: invalid
 - stable_map
 - back pointer: 0x007a63740471 <undefined>
 - prototype_validity cell: 0x0025fa080661 <Cell value= 1>
 - instance descriptors (own) #0: 0x007a63740229 <DescriptorArray[0]>
 - prototype: 0x00697d78ac99 <Object map = 0x538e501489>
 - constructor: 0x00697d78ab51 <JSFunction Set (sfi = 0x25fa08ffd1)>
 - dependent code: 0x007a63740289 <Other heap object (WEAK_FIXED_ARRAY_TYPE)>
 - construction counter: 0
```
