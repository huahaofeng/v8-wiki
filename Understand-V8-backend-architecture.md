[[_TOC_]]

This wiki documents our understanding on the platform-specific components of V8. The information is of interest to anyone who is adding a new machine platform to V8.

# V8 Target-specific Backend Overview

V8 currently supports multiple architectures: `x64`, `arm`, `arm64`, `ia32`, `mips`, `mips64`, `ppc`, `s390`. Most architecture-specific codes are organized under architecture directories, such as `compiler/backend/ppc` or `codegen/mips64/`. Source files under architecture-specific directories are included by the build only if its corresponding CPU target is selected. 

While most source codes outside architecture-specific directories are not specific to any architecture, but there are places where architecture-specific codes may be inserted in the common code paths. In those cases, the architecture-specific codes are guarded by macros such as `V8_TARGET_ARCH_ARM`.

Since the majority of architecture-specific supports are organized in architecture-specific directories, let's take a look at those directories. The following list summarizes all architecture-specific source code directories in V8 (using `PPC` architecture as an example).

- Assemblers (`codegen/ppc/`): platform-specific assemblers such as `Assembler`, `TurboAssembler`, `MacroAssembler`; their APIs take registers as input and emit instructions to the instruction buffer;
- TF backends (`compiler/backend/ppc/`): Instruction selection (from TF machine node to PPC instructions), instruction type classification for register allocator 
- V8 builtin functions (`builtin/ppc/`): implementation of builtin on target platform using PPC Turbo Assembler APIs
- V8 execution engine (`execution/ppc`): frame-constant, simulator (for simulator build)
- Diagnostic output (`diagnostics/ppc`): disassembler for PPC codes for diagnostic output
- Debugging support (`debug/ppc`): ppc code for `GenerateFrameDropperTrampoline()` using PPC turbo assembler APIs
- JIT Deoptimizer (`deoptimizer/ppc`): `GenerateDeoptimizationEntries()` using PPC turbo assembler APIs
- Regx (`regexp/ppc`): RegExp macro assembler using PPC turbo assembler APIs
- WebAssembly (`wasm/baseline/ppc`): LiftoffAssembler ()

There are also a small number of places that use the inline assembly may b.

# V8 Architecture-specific Components

## Assemblers

The directory `codegen/` defines several important types of assemblers used throughout the V8 code generation. Interestingly, the `CodeGenerator` class is in fact declared under `compiler/backend/code-generator.cc` not under `codegen/`.
- Code assembler (defined under `compiler/`): platform-independent, APIs reflect TF machine-node IR, APIs create TFNodes
- Code stub assembler: platform-independent, macro-assembler built on top of `CodeAssembler`, APIs have JS semantics, APIs create TFNodes
- `Assembler`: platform-specific, APIs similar to target machine instructions
- TurboAssembler: platform-specific, built on top of `TurboAssemblerBase` (which is on top of `Assembler`), APIs write instructions to an instruction buffer
- MacroAssembler: platform-specific, macro-assembler built on top of `TurboAssembler` (w/ JS semantics?), APIs write instructions to instruction buffer 

### Code Assembler (`compiler/code-assembler.cc`)

Code assembler APIs are based on TurboFan (TF) machine-node IR. These APIs are close to machine-level instructions but are platform-neutral. Its API interfaces typically take `TNode` as inputs and returns `TNode`.

Below are examples of code assembler APIs defined in `compiler/code-assembler.cc`.

```C++
  TNode<WordT> WordShl(SloppyTNode<WordT> value, int shift);
  TNode<WordT> WordShr(SloppyTNode<WordT> value, int shift);
  TNode<WordT> WordSar(SloppyTNode<WordT> value, int shift);
  TNode<IntPtrT> WordShr(TNode<IntPtrT> value, int shift) {
    return UncheckedCast<IntPtrT>(WordShr(static_cast<Node*>(value), shift));
  }
  TNode<IntPtrT> WordSar(TNode<IntPtrT> value, int shift) {
    return UncheckedCast<IntPtrT>(WordSar(static_cast<Node*>(value), shift));
  }
  TNode<Word32T> Word32Shr(SloppyTNode<Word32T> value, int shift);
  TNode<Word32T> Word32Sar(SloppyTNode<Word32T> value, int shift);
```

Note that `CodeAssembler` APIs create `TFNode`.

### Code Stub Assembler (`codegen/code-stub-assembler.cc`)

Code Stub Assembler (aka CSA) Provides JavaScript-specific "macro-assembler" functionality on top of `CodeAssembler` (where `CodeAssembler` is defined under `compiler/code-assembler.cc`). Unlike `TurboAssembler` and `MacroAssembler`, CSA is platform-neutral and the APIs of `CodeStubAssembler` consrtucts `TNode` graphs. Its API interfaces typically take `TNode` as inputs and returns `TNode`.

Below are some CSA assembler interfaces defined in `codegen/code-stub-assembler.cc`.

```C++
  // Smi | HeapNumber operations.
  TNode<Number> NumberInc(SloppyTNode<Number> value);
  TNode<Number> NumberDec(SloppyTNode<Number> value);
  TNode<Number> NumberAdd(SloppyTNode<Number> a, SloppyTNode<Number> b);
  TNode<Number> NumberSub(SloppyTNode<Number> a, SloppyTNode<Number> b);
  void GotoIfNotNumber(TNode<Object> value, Label* is_not_number);
  void GotoIfNumber(TNode<Object> value, Label* is_number);
```

CSA and CodeAssembler APIs are used to implement V8 builtin functions, Ignition bytecode handlers, that will be later compiled by TF backend to target-specific binaries.

### Assembler (`codegen/ppc/assembler-ppc.cc`)

An assembler generates instructions and relocation information to a buffer, w/ the instructions starting from the beginning and relocation information beginning from the end. Unlike CSA, `Assembler` generates instructions to instruction buffer, its API interfaces usually take `Register` as inputs and `void` as output. 

`Assembler` defines APIs that look very much like the assembly code-names of the target-platforms.  So each target-platform will specify its list of such APIs (one for each target-architecture instruction). 

Some of the PPC `Assembler` APIs are shown below. These APIs are named after PPC instruction names, thus closely resembles the programming interfaces of a typical assembler.
 
```C++
  void sub(Register dst, Register src1, Register src2, OEBit s = LeaveOE,
           RCBit r = LeaveRC);

  void subc(Register dst, Register src1, Register src2, OEBit s = LeaveOE,
            RCBit r = LeaveRC);
  void sube(Register dst, Register src1, Register src2, OEBit s = LeaveOE,
            RCBit r = LeaveRC);

  void subfic(Register dst, Register src, const Operand& imm);

  void add(Register dst, Register src1, Register src2, OEBit s = LeaveOE,
           RCBit r = LeaveRC);

  void addc(Register dst, Register src1, Register src2, OEBit o = LeaveOE,
            RCBit r = LeaveRC);
  void adde(Register dst, Register src1, Register src2, OEBit o = LeaveOE,
            RCBit r = LeaveRC);
  void addze(Register dst, Register src1, OEBit o = LeaveOE, RCBit r = LeaveRC);

  void mullw(Register dst, Register src1, Register src2, OEBit o = LeaveOE,
             RCBit r = LeaveRC);
```

### Turbo Assembler (`codegen/ppc/macro-assembler.cc`)

A `TurboAssembler` is the primary interface to platform-specific assembler and is often referred to as `tasm` by `CodeGenerator`. It is a subclass of `TurboAssemblerBase`, which in turn is a subclass of `Assembler`. 

`TurboAssembler` APIs are used to express target-specific "assembly" throughout V8 backend such as the use of `TurboAssembler::Ld/Lw/And/Jump(...)` in the following code snippet from `compiler/backend/mips64/code-generator-mips64.cc`:
```C++
void CodeGenerator::BailoutIfDeoptimized() {
  int offset = Code::kCodeDataContainerOffset - Code::kHeaderSize;
  __ Ld(kScratchReg, MemOperand(kJavaScriptCallCodeStartRegister, offset));
  __ Lw(kScratchReg,
        FieldMemOperand(kScratchReg,
                        CodeDataContainer::kKindSpecificFlagsOffset));
  __ And(kScratchReg, kScratchReg,
         Operand(1 << Code::kMarkedForDeoptimizationBit));
  __ Jump(BUILTIN_CODE(isolate(), CompileLazyDeoptimizedCode),
          RelocInfo::CODE_TARGET, ne, kScratchReg, Operand(zero_reg));
}
```

While a significant part of `TurboAssembler` is platform-specific, some platform-independent APIs are separated into `TurboAssemblerBase` (defined in the common path of `codegen/turbo-assembler.h` and shared across different machine target). 

Some of the target-independent APIs of `TurboAssemblerBase` are listed below.
```C++
  virtual void Jump(const ExternalReference& reference) = 0;

  // Calls the builtin given by the Smi in |builtin|. If builtins are embedded,
  // the trampoline Code object on the heap is not used.
  virtual void CallBuiltinByIndex(Register builtin_index) = 0;

  // Calls/jumps to the given Code object. If builtins are embedded, the
  // trampoline Code object on the heap is not used.
  virtual void CallCodeObject(Register code_object) = 0;
  virtual void JumpCodeObject(Register code_object) = 0;

  // Loads the given Code object's entry point into the destination register.
  virtual void LoadCodeObjectEntry(Register destination,
                                   Register code_object) = 0;

  // Loads the given constant or external reference without embedding its direct
  // pointer. The produced code is isolate-independent.
  void IndirectLoadConstant(Register destination, Handle<HeapObject> object);
  void IndirectLoadExternalReference(Register destination,
                                     ExternalReference reference);

  virtual void LoadFromConstantsTable(Register destination,
                                      int constant_index) = 0;

  // Corresponds to: destination = kRootRegister + offset.
  virtual void LoadRootRegisterOffset(Register destination,
                                      intptr_t offset) = 0;
```

The more target-specific interfaces of `TurboAssembler` is defined in `codegen/ppc/macro-assembler.h` as shown for this PPC `TurboAssembler`:
```C++
  void LoadP(Register dst, const MemOperand& mem, Register scratch = no_reg);
  void LoadPU(Register dst, const MemOperand& mem, Register scratch = no_reg);
 // Push caller saved registers on the stack, and return the number of bytes
  // stack pointer is adjusted.
  int PushCallerSaved(SaveFPRegsMode fp_mode, Register exclusion1 = no_reg,
                      Register exclusion2 = no_reg,
                      Register exclusion3 = no_reg);
  // Restore caller saved registers from the stack, and return the number of
  // bytes stack pointer is adjusted.
  int PopCallerSaved(SaveFPRegsMode fp_mode, Register exclusion1 = no_reg,
                     Register exclusion2 = no_reg,
                     Register exclusion3 = no_reg);

  // Load an object from the root table.
  void LoadRoot(Register destination, RootIndex index) override {
    LoadRoot(destination, index, al);
  }
  void LoadRoot(Register destination, RootIndex index, Condition cond);
```

### Macro Assembler (`codegen/ppc/macro-assembler-ppc.cc`)

`MacroAssembler` is built on top of `TurboAssembler` to provide APIs that implement a collection of frequently used macros, as shown below.

```C++
  void CallRecordWriteStub(Register object, Register address,
                           RememberedSetAction remembered_set_action,
                           SaveFPRegsMode fp_mode);
  void CallRecordWriteStub(Register object, Register address,
                           RememberedSetAction remembered_set_action,
                           SaveFPRegsMode fp_mode, Address wasm_target);
  void CallEphemeronKeyBarrier(Register object, Register address,
                               SaveFPRegsMode fp_mode);

  void MultiPush(RegList regs, Register location = sp);
  void MultiPop(RegList regs, Register location = sp);

  void MultiPushDoubles(RegList dregs, Register location = sp);
  void MultiPopDoubles(RegList dregs, Register location = sp);
```

Like `TurboAssembler`, `MacroAssembler` is defined in the target-specific directory, and the APIs differ from different architectures.

### Porting summary

- Porting of `Assembler` to implement machine-specific assembly APIs (significant porting effort)
- Porting of `TurboAssembler` and `MacroAssembler` for higher-level APIs (considerable porting effort)

## Compiler Backend

Defined under `compiler/backend`, the compiler backend deals with instruction selection, register allocation, a few other optimizations (e.g., move-optimizer), and finally, assemble codes to architecture-specific instructions. 

Compiler backend can be entered through 3 different routes:
- From the compiler pipeline (`compiler/pipeline.cc`)
- From Ignition engine bytecode handler generation (`ignition/interpreter-generator.cc`)
- From V8 builtin initialization (`builtins/setup-builtins-internal.cc`)

The compiler-backend can be triggered by compilation pipeline (`compiler/pipeline.cc`) via `SelectInstruction()` and `AssembleCode()`steps as shown below.
```C++
  // Step A.1. Serialize the data needed for the compilation front-end.
  void Serialize();
  // Step A.2. Run the graph creation and initial optimization passes.
  bool CreateGraph();
  // Step B. Run the concurrent optimization passes.
  bool OptimizeGraph(Linkage* linkage);
  // Alternative step B. Run minimal concurrent optimization passes for 
  // mid-tier.
  bool OptimizeGraphForMidTier(Linkage* linkage);
  // Substep B.1. Produce a scheduled graph.
  void ComputeScheduledGraph();
  // Substep B.2. Select instructions from a scheduled graph.
  bool SelectInstructions(Linkage* linkage);
  // Step C. Run the code assembly pass.
  void AssembleCode(Linkage* linkage, std::unique_ptr<AssemblerBuffer> buffer = {});
  // Step D. Run the code finalization pass.
  MaybeHandle<Code> FinalizeCode(bool retire_broker = true);
```

The backend can also be entered from Ignition engine through `CodeAssembler::GenerateCode()` invoked from `GenerateBytecodeHandler()` in `ignition/interpreter-generator.cc` as shown below:
```C++
Handle<Code> GenerateBytecodeHandler(Isolate* isolate, const char* debug_name,
                                     Bytecode bytecode,
                                     OperandScale operand_scale,
                                     int builtin_index,
                                     const AssemblerOptions& options) {
  ...
  switch (bytecode) {
#define CALL_GENERATOR(Name, ...)                     \
  case Bytecode::k##Name:                             \
    Name##Assembler::Generate(&state, operand_scale); \
    break;
    BYTECODE_LIST(CALL_GENERATOR);
#undef CALL_GENERATOR
  }
  Handle<Code> code = compiler::CodeAssembler::GenerateCode(&state, options); // trigger code generation
  ...
  return code;
}
```

Finally the backend can be entered from builtin initialization via `SetupBuiltinsInternal()` from `setup-builtins-internal.cc` that in turn calls `CodeAssembler::GenerateCode()`.

### Instruction Selection

The entry-point to instruction selection is `InstructionSelector::SelectInstructions()` as defined in `compiler/backend/instruction-selector.cc`. This common-path algorithm recursively traverses all the blocks (`VisitBlock()`) and eventually all the nodes (`VisitNode()`) in the TFGraph.

It is in `VisitNode()` that the transitioning from a common path algorithm to a target-specific instruction selector happens. As shown below, `VisitNode()` consists of a large switch-statement (~900 LOC) that dispatches to different `VisitXXX()` methods based on the `IrOpcode` of the incoming node.
```C++
void InstructionSelector::VisitNode(Node* node) {
  tick_counter_->DoTick();
  DCHECK_NOT_NULL(schedule()->block(node));  // should only use scheduled nodes.
  switch (node->opcode()) {
    ...
    case IrOpcode::kDelayedStringConstant:
      return MarkAsTagged(node), VisitConstant(node);
    case IrOpcode::kCall:
      return VisitCall(node);
    case IrOpcode::kDeoptimizeIf:
      return VisitDeoptimizeIf(node);
    case IrOpcode::kDeoptimizeUnless:
      return VisitDeoptimizeUnless(node);
    case IrOpcode::kTrapIf:
      return VisitTrapIf(node, TrapIdOf(node->op()));
    case IrOpcode::kTrapUnless:
      return VisitTrapUnless(node, TrapIdOf(node->op()));
    ...
  }
  ...
} 
```  

The `VisitXXX()` methods invoked by the common-path `VisitNode()` are implemented by target-specific instruction schedulers. For instance,
- `compiler/backend/ppc/instruction-selector-ppc.cc` implements 307 `VisitXXX()` methods
- `compiler/backend/mips64/instruction-selector-mips64.cc` implements 176 `VisitXXX()` methods
- `compiler/backend/x64/instruction-selector-x64.cc` implements 151 `VisitXXX()` methods
- `compiler/backend/arm64/instruction-selector-arm64.cc` implements 111 `VisitXXX()` methods

**Question**
- Why different targets implement different numbers of `VisitXXX()` methods?

Here is an example of `VisitXXX()` method implemented by `instruction-selector-mips64.cc`. Note that opcodes such as `kMips64Or32` are target-specific machine-node IRs.
```C++
void InstructionSelector::VisitWord32Or(Node* node) {
  VisitBinop(this, node, kMips64Or32, true, kMips64Or32);
}
```

### Code Generation

Code generation is triggered by invoking `CodeGenerator::AssembleCode()` from the compilation pipeline (as shown at the beginning of the compiler backend section).

The common-path code-generator traverses all the blocks (via `CodeGenerator::AssembleBlock()`) and eventually every node (via `CodeGenerator::AssembleInstruction()` of the incoming TFNode graph to generate machine codes. The transitioning from common-path algorithm to architecture-specific code generation is triggered by `CodeGenerator::AssembleInstruction()` calling `CodeGenerator::AssembleArchInstruction()`. The latter is implemented by architecture-specific code generators.

For instance, the following snippet is from `compiler/backend/ppc/code-generator-ppc.cc`. It consists a big switch statement that for each type of `instr->opcode()` emits a sequence of machine codes using architecture-specific `TurboAssembler` (note: `#define __ tasm()->`) that implements the semantics of the `ArchOpcode`.

```C++
// Assembles an instruction after register allocation, producing machine code.
CodeGenerator::CodeGenResult CodeGenerator::AssembleArchInstruction(
    Instruction* instr) {
  PPCOperandConverter i(this, instr);
  ArchOpcode opcode = ArchOpcodeField::decode(instr->opcode());

  switch (opcode) {
    case kArchCallCodeObject: {
      v8::internal::Assembler::BlockTrampolinePoolScope block_trampoline_pool(
          tasm());
      if (HasRegisterInput(instr, 0)) {
        Register reg = i.InputRegister(0);
        DCHECK_IMPLIES(
            HasCallDescriptorFlag(instr, CallDescriptor::kFixedTargetRegister),
            reg == kJavaScriptCallCodeStartRegister);
        __ CallCodeObject(reg);
      } else {
        __ Call(i.InputCode(0), RelocInfo::CODE_TARGET);
      }
      RecordCallPosition(instr);
      DCHECK_EQ(LeaveRC, i.OutputRCBit());
      frame_access_state()->ClearSPDelta();
      break;
    }
  ...
 }
 ...
```

CodeGenerator::AssembleArchInstruction() needs to handle all `ArchOpcodes` defined in `compiler/backend/instruction-codes.h` (~100 opcodes) and target-specific `ArchOpcodes` defined in `compiler/backend/ppc/instruction-codes-ppc.h` (~150 opcodes).

Besides `CodeGenerator::AssembleArchInstruction()`, there are other `AssembleXXX()` methods that need be ported to: `AssembleArchBoolean()`, `AssembleArchBinarySearchSwitch()`, `AssembleArchLookupSwitch()`, `AssembleArchTableSwitch()`, `AssembleArchJump()`, `AssembleArchBranch()`, `AssembleDeoptBranch()`, and `AssembleArchTrap()`. 

### Instruction Scheduler

Instruction scheduling is happening mostly on the common path. However, if a TFGraph node uses target-specific opcodes (e.g., `KPPC_xxx`, then the scheduler needs to know the properties of such opcodes.`InstructionScheduler::GetTargetInstructionFlags()` in `compiler/backend/ppc/instruction-scheduler-ppc.cc` determines whether a KPPC_xxx instruction is load, store, atomic, or has a side-effect.

### Register Allocator

There is no target-specific implementation for the register allocator.

### Porting summary

For a new backend, need to
- Implementing all `InstructionSelector::VisitXXX()` methods (major porting efforts)
- Implementing all `CodeGenerator::AssembleXXX()` methods (major porting efforts)
- Others: `InstructionScheduler::GetTargetInstructionFlags()` (minimal porting efforts)

## Simulator

Data structures used in V8 runtime environments such as `frame`, `thread`, `isolate` are defined under `execution/`. However, the main target-specific implementation of the execution environment is the simulator. For example, simulator for MIPS platform is defined under `execution/mips64/simulator-mips64.cc`. The MIPS simulator is invoked if V8 is not generating a native binary, and it will allow V8 to run and debug the MIPS binary on the host machines.

The simulator supports multiple threads (i.e., each `isolate` is a thread and `Monitor` (?)),  an ICache, a lightweight debugger (`MipsDebugger`) that supports breakpoints, get values, and get register values,

V8 calls into generated code via the `GeneratedCode` wrapper, which will start execution in the simulator or forwards to the real entry on a MIPS platform.
```
template <typename Return, typename... Args>
class GeneratedCode {
 public:
  using Signature = Return(Args...);
  ...
#ifdef USE_SIMULATOR
  Return Call(Args... args) {
#if defined(V8_TARGET_OS_WIN) && !defined(V8_OS_WIN)
    FATAL("Generated code execution not possible during cross-compilation.");
#endif  // defined(V8_TARGET_OS_WIN) && !defined(V8_OS_WIN)
    return Simulator::current(isolate_)->template Call<Return>(
        reinterpret_cast<Address>(fn_ptr_), args...);
  }
#else
  DISABLE_CFI_ICALL Return Call(Args... args) {
    // When running without a simulator we call the entry directly.
#if defined(V8_TARGET_OS_WIN) && !defined(V8_OS_WIN)
    FATAL("Generated code execution not possible during cross-compilation.");
#endif  // defined(V8_TARGET_OS_WIN) && !defined(V8_OS_WIN)
#if V8_OS_AIX
    ...
#else
    return fn_ptr_(args...);
#endif  // V8_OS_AIX
  }
#endif  // USE_SIMULATOR
```

### Target-specific Simulator Implementation

The entry-point to the simulator is `Simulator::Call()`, which is a wrapper to the target-specific implementation of `Simulator::CallImpl()`. `Simulator::CallImpl()` setup arguments and return values in registers and save/restore volatile registers, then trigger `Simulator::Execute()`.  

`Simulator::Execute()` executes instructions (via `Simulator::InstructionDecode()`) from the current PC until it reaches the `end_sim_pc` or at `FLAG_STOP_sim_at` (for debugging). The actual implementations of instruction simulation is at `DecodeXXXX()` functions.

```
// Executes the current instruction.
void Simulator::InstructionDecode(Instruction* instr) {
  if (v8::internal::FLAG_check_icache) {
    CheckICache(i_cache(), instr);
  }
  pc_modified_ = false;
  v8::internal::EmbeddedVector<char, 256> buffer;
  if (::v8::internal::FLAG_trace_sim) {
    SNPrintF(trace_buf_, "%s", "");
    disasm::NameConverter converter;
    disasm::Disassembler dasm(converter);
    dasm.InstructionDecode(buffer, reinterpret_cast<byte*>(instr));
  }

  instr_ = instr;
  switch (instr_.InstructionType()) {
    case Instruction::kRegisterType:
      DecodeTypeRegister();
      break;
    case Instruction::kImmediateType:
      DecodeTypeImmediate();
      break;
    case Instruction::kJumpType:
      DecodeTypeJump();
      break;
    default:
      UNSUPPORTED();
  }
  if (::v8::internal::FLAG_trace_sim) {
    PrintF("  0x%08" PRIxPTR "  %-44s   %s\n",
           reinterpret_cast<intptr_t>(instr), buffer.begin(),
           trace_buf_.begin());
  }
  if (!pc_modified_) {
    set_register(pc, reinterpret_cast<int32_t>(instr) + kInstrSize);
  }
}
```

### Porting Summary

- `execution/simulator-mips64.cc` is a major porting effort

## Builtin Functions

Implementations of V8 built-in functions are defined under `builtins/`. The following lists different types of builtin functions depending based on different calling conventions and where they can be called (e.g., from C++, ASM, JS, or from internal V8 components) as defined in `builtin/builtins_definitions.h`.
```C++
// CPP: Builtin in C++. Entered via BUILTIN_EXIT frame.
//      Args: name
// TFJ: Builtin in Turbofan, with JS linkage (callable as Javascript function).
//      Args: name, arguments count, explicit argument names...
// TFS: Builtin in Turbofan, with CodeStub linkage.
//      Args: name, explicit argument names...
// TFC: Builtin in Turbofan, with CodeStub linkage and custom descriptor.
//      Args: name, interface descriptor
// TFH: Handlers in Turbofan, with CodeStub linkage.
//      Args: name, interface descriptor
// BCH: Bytecode Handlers, with bytecode dispatch linkage.
//      Args: name, OperandScale, Bytecode
// ASM: Builtin in platform-dependent assembly.
//      Args: name, interface descriptor
```

The `BUILTIN_LIST_xxx` macros define hundreds of built-ins, as shown below:
```C++
#define BUILTIN_LIST_BASE(CPP, TFJ, TFC, TFS, TFH, ASM)                        \
  /* GC write barrirer */                                                      \
  TFC(RecordWrite, RecordWrite)                                                \
  TFC(EphemeronKeyBarrier, EphemeronKeyBarrier)                                \
                                                                               \
  /* Adaptor for CPP builtin */                                                \
  TFC(AdaptorWithBuiltinExitFrame, CppBuiltinAdaptor)   
  ...
```

### Builtin Function Implementation

V8 built-in functions are implemented in a mostly platform-independent way. There are total 61 `builtin-xxx.cc` files and 98 `xxx.tq` source files. 

There are three types of builtin implementations:
- `TF_BUILTIN` (i.e., Turbo-fan Builtins): builtin functions are implemented as TFNode graphs using CodeStubAssembler
- `IC_BUILTIN` (i.e., Inlin-caching Builtins): builtin functions implemented as TFNode graphs using `AccessorAssembler` (subclass of `CodeStubAssembler`)
- `BUILTIN`: builtin function that are implemented as C++ codes directly accessing objects in V8 internal representation

Note that both `TF_BUILTIN` and `IC_BUILTIN` require the TF backend to generate the final binaries of the builtins.

#### `BUILTIN` implementation

C++ builtin functions are implemented mostly using internal V8 interfaces,
```C++
// ----------------------------------------------------------------------------
//
// A builtin function is defined by writing:
//
//   BUILTIN(name) {
//     ...
//   }
//
// In the body of the builtin function the arguments can be accessed
// through the BuiltinArguments object args.
// TODO(cbruni): add global flag to check whether any tracing events have been
// enabled.
#define BUILTIN(name)                                                       \
  V8_WARN_UNUSED_RESULT static Object Builtin_Impl_##name(                  \
      BuiltinArguments args, Isolate* isolate);                             \
                                                                            \
  V8_NOINLINE static Address Builtin_Impl_Stats_##name(                     \
      int args_length, Address* args_object, Isolate* isolate) {            \
    BuiltinArguments args(args_length, args_object);                        \
    RuntimeCallTimerScope timer(isolate,                                    \
                                RuntimeCallCounterId::kBuiltin_##name);     \
    TRACE_EVENT0(TRACE_DISABLED_BY_DEFAULT("v8.runtime"),                   \
                 "V8.Builtin_" #name);                                      \
    return CONVERT_OBJECT(Builtin_Impl_##name(args, isolate));              \
  }                                                                         \
                                                                            \
  V8_WARN_UNUSED_RESULT Address Builtin_##name(                             \
      int args_length, Address* args_object, Isolate* isolate) {            \
    DCHECK(isolate->context().is_null() || isolate->context().IsContext()); \
    if (V8_UNLIKELY(TracingFlags::is_runtime_stats_enabled())) {            \
      return Builtin_Impl_Stats_##name(args_length, args_object, isolate);  \
    }                                                                       \
    BuiltinArguments args(args_length, args_object);                        \
    return CONVERT_OBJECT(Builtin_Impl_##name(args, isolate));              \
  }                                                                         \
                                                                            \
  V8_WARN_UNUSED_RESULT static Object Builtin_Impl_##name(                  \
      BuiltinArguments args, Isolate* isolate)
```
The following is an implementation of builtin `StringPrototypeNormalize`.
```C++
// ES6 section 21.1.3.12 String.prototype.normalize ( [form] )
//
// Simply checks the argument is valid and returns the string itself.
// If internationalization is enabled, then intl.js will override this function
// and provide the proper functionality, so this is just a fallback.
BUILTIN(StringPrototypeNormalize) {
  HandleScope handle_scope(isolate);
  TO_THIS_STRING(string, "String.prototype.normalize");

  Handle<Object> form_input = args.atOrUndefined(isolate, 1);
  if (form_input->IsUndefined(isolate)) return *string;

  Handle<String> form;
  ASSIGN_RETURN_FAILURE_ON_EXCEPTION(isolate, form,
                                     Object::ToString(isolate, form_input));

  if (!(String::Equals(isolate, form, isolate->factory()->NFC_string()) ||
        String::Equals(isolate, form, isolate->factory()->NFD_string()) ||
        String::Equals(isolate, form, isolate->factory()->NFKC_string()) ||
        String::Equals(isolate, form, isolate->factory()->NFKD_string()))) {
    Handle<String> valid_forms =
        isolate->factory()->NewStringFromStaticChars("NFC, NFD, NFKC, NFKD");
    THROW_NEW_ERROR_RETURN_FAILURE(
        isolate,
        NewRangeError(MessageTemplate::kNormalizationForm, valid_forms));
  }

  return *string;
}
```

#### `TF_BUILTIN` and `IC_BUILTIN` Implementations

Both `TF_BUILTIN` and `IC_BUILTIN` are implemented as `TFNode` graphs constructed via (subclasses of) `CodeStubAssembler` API interfaces. These `TFNode` graphs will be eventually compiled to platform-specific codes by the TF backend.

The following snippet is from `builtin/builtins-intl-gen.cc` where `IntlBuiltinsAssembler` is a subclass of `CodeStubAssembler` and methods such as `GotoIf()` or `GotoIfNot()` are defined by `CodeStubAssembler`.   

```C++
TF_BUILTIN(StringToLowerCaseIntl, IntlBuiltinsAssembler) {
  const TNode<String> string = CAST(Parameter(Descriptor::kString));

  Label call_c(this), return_string(this), runtime(this, Label::kDeferred);

  // Early exit on empty strings.
  const TNode<Uint32T> length = LoadStringLengthAsWord32(string);
  GotoIf(Word32Equal(length, Uint32Constant(0)), &return_string);

  // Unpack strings if possible, and bail to runtime unless we get a one-byte
  // flat string.
  ToDirectStringAssembler to_direct(
      state(), string, ToDirectStringAssembler::kDontUnpackSlicedStrings);
  to_direct.TryToDirect(&runtime);

  const TNode<Int32T> instance_type = to_direct.instance_type();
  CSA_ASSERT(this,
             Word32BinaryNot(IsIndirectStringInstanceType(instance_type)));
  GotoIfNot(IsOneByteStringInstanceType(instance_type), &runtime);
  ...
``` 
Some built-in functions are implemented in `xxx.tq` files (98 files). These built-in functions are written in a DSL called Torque, which will be compiled into C++ codes using the CSA interfaces by the Torque compiler. Torque provides a higher-level abstraction to enable easier developments of builtin functions w/ more rigorous checking.

#### `ASM` Builtin Functions

`ASM` builtin functions are implemented in target-specific ASMs. They are generated using `Builtins::Generate_XXX()` defined under target-specific directories. For instance, `JSEntry()` is an `ASM` builtin function and it is generated via `ppc/Builtins::Generate_JSEntry()`. 

For each target backend, 41 `Builtins::Generated_xxx()` methods must be implemented, e.g,: 
```C++
void Builtins::Generate_Adaptor(MacroAssembler* masm, Address address) {
void Builtins::Generate_JSConstructStubGeneric(MacroAssembler* masm) {
void Builtins::Generate_JSBuiltinsConstructStub(MacroAssembler* masm) {
...
```
Note that the above `Builtins::Generate_XXX()` methods generate target-specific code via `MacroAssembler` APIs. The following example is taken from `builtin/ppc/builtins-ppc.cc`:
```C++
#define __ ACCESS_MASM(masm)

void Builtins::Generate_Adaptor(MacroAssembler* masm, Address address) {
  __ Move(kJavaScriptCallExtraArg1Register, ExternalReference::Create(address));
  __ Jump(BUILTIN_CODE(masm->isolate(), AdaptorWithBuiltinExitFrame),
          RelocInfo::CODE_TARGET);
}
```

### Initialization of Builtin Functions

The startup of V8 VM initializes many of the built-in functions. When `snapshot `is not used, `Genesis::InitializeGlobal()` in `init/bootstrapper.cc` installs many basic builtins like `valueOf`, `toString`. This function explicitly initializes the necessary built-ins one by one. The following shows snippets of such initialization using `SimpleInstallFunction()`.
```C++
{  // --- O b j e c t ---
    Handle<String> object_name = factory->Object_string();
    Handle<JSFunction> object_function = isolate_->object_function();
    JSObject::AddProperty(isolate_, global_object, object_name, object_function,
                          DONT_ENUM);

    SimpleInstallFunction(isolate_, object_function, "assign",
                          Builtins::kObjectAssign, 2, false);
    SimpleInstallFunction(isolate_, object_function, "getOwnPropertyDescriptor",
                          Builtins::kObjectGetOwnPropertyDescriptor, 2, false);
    ...
```

**Observation** 
- To run even a simplest JS code, the new V8 backend still needs to compile all builtin functions used at the V8 startup, thus would require relatively complete support for the new backend (especially platform-specific parts of `codegen/`, `compiler/backend/`, `builtin/`, and `execution/`. 

### Porting Summary

Each backend must implement the following 41 APIs: 
```C++
void Builtins::Generate_Adaptor(MacroAssembler* masm, Address address) {
void Builtins::Generate_JSConstructStubGeneric(MacroAssembler* masm) {
void Builtins::Generate_JSBuiltinsConstructStub(MacroAssembler* masm) {
void Builtins::Generate_ResumeGeneratorTrampoline(MacroAssembler* masm) {
void Builtins::Generate_ConstructedNonConstructable(MacroAssembler* masm) {
void Builtins::Generate_JSEntry(MacroAssembler* masm) {
void Builtins::Generate_JSConstructEntry(MacroAssembler* masm) {
void Builtins::Generate_JSRunMicrotasksEntry(MacroAssembler* masm) {
void Builtins::Generate_JSEntryTrampoline(MacroAssembler* masm) {
void Builtins::Generate_JSConstructEntryTrampoline(MacroAssembler* masm) {
void Builtins::Generate_RunMicrotasksTrampoline(MacroAssembler* masm) {
void Builtins::Generate_InterpreterEntryTrampoline(MacroAssembler* masm) {
void Builtins::Generate_InterpreterPushArgsThenCallImpl(
void Builtins::Generate_InterpreterPushArgsThenConstructImpl(
void Builtins::Generate_InterpreterEnterBytecodeAdvance(MacroAssembler* masm) {
void Builtins::Generate_InterpreterEnterBytecodeDispatch(MacroAssembler* masm) {
void Builtins::Generate_ContinueToCodeStubBuiltin(MacroAssembler* masm) {
void Builtins::Generate_ContinueToCodeStubBuiltinWithResult(
void Builtins::Generate_ContinueToJavaScriptBuiltin(MacroAssembler* masm) {
void Builtins::Generate_ContinueToJavaScriptBuiltinWithResult(
void Builtins::Generate_NotifyDeoptimized(MacroAssembler* masm) {
void Builtins::Generate_InterpreterOnStackReplacement(MacroAssembler* masm) {
void Builtins::Generate_FunctionPrototypeApply(MacroAssembler* masm) {
void Builtins::Generate_FunctionPrototypeCall(MacroAssembler* masm) {
void Builtins::Generate_ReflectApply(MacroAssembler* masm) {
void Builtins::Generate_ReflectConstruct(MacroAssembler* masm) {
void Builtins::Generate_CallOrConstructVarargs(MacroAssembler* masm,
void Builtins::Generate_CallOrConstructForwardVarargs(MacroAssembler* masm,
void Builtins::Generate_CallFunction(MacroAssembler* masm,
void Builtins::Generate_CallBoundFunctionImpl(MacroAssembler* masm) {
void Builtins::Generate_Call(MacroAssembler* masm, ConvertReceiverMode mode) {
void Builtins::Generate_ConstructFunction(MacroAssembler* masm) {
void Builtins::Generate_ConstructBoundFunction(MacroAssembler* masm) {
void Builtins::Generate_Construct(MacroAssembler* masm) {
void Builtins::Generate_ArgumentsAdaptorTrampoline(MacroAssembler* masm) {
void Builtins::Generate_WasmCompileLazy(MacroAssembler* masm) {
void Builtins::Generate_CEntry(MacroAssembler* masm, int result_size,
void Builtins::Generate_DoubleToI(MacroAssembler* masm) {
void Builtins::Generate_CallApiCallback(MacroAssembler* masm) {
void Builtins::Generate_CallApiGetter(MacroAssembler* masm) {
void Builtins::Generate_DirectCEntry(MacroAssembler* masm) {
```

## WASM

WebAssembly can be executed by the WASM interpreter (implemented in `wasm/wasm-interpreter.cc`) or compiled and executed as binaries by the liftoff compiler. 

### WASM Interpreter

Since WASM bytecode is statically typed, the implementation of each bytecode (`wasm/wasm-opcodes.h`) either through the interpreter or by compilation is quite straightforward.

WASM interpreter implements WASM bytecodes in C, so no porting is required.
```
#define FOREACH_SIMPLE_BINOP(V) \
  V(I32Add, uint32_t, +)        \
  V(I32Sub, uint32_t, -)        \
  V(I32Mul, uint32_t, *)        \
  ...

#define EXECUTE_SIMPLE_BINOP(name, ctype, op)               \
  case kExpr##name: {                                       \
    WasmValue rval = Pop();                                 \
    WasmValue lval = Pop();                                 \
    auto result = lval.to<ctype>() op rval.to<ctype>();     \
    possible_nondeterminism_ |= has_nondeterminism(result); \
    Push(WasmValue(result));                                \
    break;                                                  \
  }
        FOREACH_SIMPLE_BINOP(EXECUTE_SIMPLE_BINOP)
#undef EXECUTE_SIMPLE_BINOP
```

#### Liftoff Compiler

WASM codes can also be compiled into binary through liftoff compiler via `ExecuteLiftoffCompilation()` in `wasm/baseline/liftoff-compiler.cc`. The compilation seems to go through each function, statement, operation via `FullDecoder` that invokes the LifoffAssembler APIs to emit target-specific instructions to the instruction buffer.

The following code snippet shows the code-gen for a WASM opcode `kExprI64Popcnt`. Here `__` refers to LiftoffAssembler, and `__ emit_xxx(...)` functions generate target-specific binaries to the instruction buffer.
```
      case kExprI64Popcnt:
        EmitUnOp<kWasmI64, kWasmI64>(
            [=](LiftoffRegister dst, LiftoffRegister src) {
              if (__ emit_i64_popcnt(dst, src)) return;
              // The c function returns i32. We will zero-extend later.
              ValueType sig_i_l_reps[] = {kWasmI32, kWasmI64};
              FunctionSig sig_i_l(1, 1, sig_i_l_reps);
              LiftoffRegister c_call_dst = kNeedI64RegPair ? dst.low() : dst;
              GenerateCCall(&c_call_dst, &sig_i_l, kWasmStmt, &src,
                            ExternalReference::wasm_word64_popcnt());
              // Now zero-extend the result to i64.
              __ emit_type_conversion(kExprI64UConvertI32, dst, c_call_dst,
                                      nullptr);
            });
        break;
```

The only platform-specific part of Lifoff compiler is `wasm/baseline/mips64/liftoff-assembler-mips64.cc`, which implements Liftoff assembler APIs using target-specific `TurboAssembler` APIs. For instance, the follow snippets implements `LiftoffAssembler::emit_type_conversion()` using `TurboAssembler` APIs.

```
bool LiftoffAssembler::emit_type_conversion(WasmOpcode opcode,
                                            LiftoffRegister dst,
                                            LiftoffRegister src, Label* trap) {
  switch (opcode) {
    case kExprI32ConvertI64:
      TurboAssembler::Ext(dst.gp(), src.gp(), 0, 32);
      return true;
    case kExprI32SConvertF32: {
      LiftoffRegister rounded =
          GetUnusedRegister(kFpReg, LiftoffRegList::ForRegs(src));
      LiftoffRegister converted_back =
          GetUnusedRegister(kFpReg, LiftoffRegList::ForRegs(src, rounded));
      ...
```

In some cases, a LiftoffAssembler API is implemented via target-specific `CodeAssembler` APIs, as shown below:
```
#define I32_BINOP(name, instruction)                                 \
  void LiftoffAssembler::emit_i32_##name(Register dst, Register lhs, \
                                         Register rhs) {             \
    instruction(dst, lhs, rhs);                                      \
  }

// clang-format off
I32_BINOP(add, addu)
I32_BINOP(sub, subu)
I32_BINOP(and, and_)
I32_BINOP(or, or_)
I32_BINOP(xor, xor_)
// clang-format on
```

### Porting Summary

- WASM interpreter requires no porting
- Liftoff Assembler requires very little porting because the assembler interfaces are implemented using `TurboAssembler` APIs and a few simple `CodeAssembler` APIs. Since we have implemented MIPS64 `TurboAssembler` APIs and a subset of `CodeAssembler` APIs using RISCV instructions for the JS engine anyway, very few additional porting is necessary. 

## Other V8 Internals

### MIPS64 Register Usage Convention

See [Registers](Registers/)

### Calling Convention

A calling convention specifies how functions receive arguments from their callers and how to return results. In this section, we are concerned with calling conventions for callee functions whose binaries are generated by V8.

There are several ways to invoke a V8 generated code:
- Callee: JS functions (compiled by V8), invoked through `Execution::Call/CallBuiltin/New/TryCall/...()`;
- Callee: WASM functions (compiled by V8), invoked through `Execution::CallWasm`;
- Callee: any V8 compiled code wrapped in `GeneratedCode<...>` (JS/WASM functions, V8 internal functions, Codes generated by directly invoking MacroAssembler APIs from C codes), invoked through `GeneratedCode<...>.Call(...)` ; Caller: simulator or direct native code execution

#### `GeneratedCode<...>.Call` via C varargs calling convention

All V8 generated codes (incl., JS, and WASM functions generated by V8 or codes constructed by directly invoking macro-assembler as in `cctest/test-assembly-xxx.cc`) are wrapped in generic `GeneratedCode<...>` objects (as defined in `execution/simulator.h`). And invoking any V8 generated codes always involves `GeneratedCode<...>.Call(...)`. 

The `Call()` interface is a simple wrapper to dispatch between simulated execution and native execution as shown below:
```
#ifdef USE_SIMULATOR
  // Defined in simulator-base.h.
  Return Call(Args... args) {
    return Simulator::current(isolate_)->template Call<Return>(
        reinterpret_cast<Address>(fn_ptr_), args...);
  }
#else
  DISABLE_CFI_ICALL Return Call(Args... args) {
    ...
    // When running without a simulator we call the entry directly.
    return fn_ptr_(args...);
  }
#endif  // USE_SIMULATOR
```

##### Native Call Setup

In the non-simulator mode, the interface directly invokes the generated code as a native function, i.e., `fn_ptr_(args...)`. Since `fn_ptr_(args...)` is invoked from C, it means that `GeneratedCode<...>.Call(...)` follows C varargs function calling convention on the target machine. 

For instance, on MIPS, the callee expects that arguments are passed in `a0`, ..., `a7` first w/ the rest spilled onto the stack, and it passes the result back to `v0`.



##### Simulated Execution via `Simulator::CallImpl()`

When a generated coded is invoked via `GeneratedCode<...>.Call(...)`, under the simulator mode, it gets dispatched to `Simulator::CallImpl`, defined as follows:
```
intptr_t Simulator::CallImpl(Address entry, int argument_count, const intptr_t* arguments)
```

Note that the native execution and simulated execution must follow the same calling convention when invoking `GeneratedCode`. 
Since `GeneratedCode` uses C's vararg calling convention on target machine, the simulated path must follow the same convention of arguments and result passing  as shown below:
```
  // First four arguments passed in registers in both ABI's.
  int reg_arg_count = std::min(kRegisterPassedArguments, argument_count);
  if (reg_arg_count > 0) set_register(a0, arguments[0]);
  if (reg_arg_count > 1) set_register(a1, arguments[1]);
  if (reg_arg_count > 2) set_register(a2, arguments[2]);
  if (reg_arg_count > 3) set_register(a3, arguments[3]);

  // Up to eight arguments passed in registers in N64 ABI.
  // TODO(plind): N64 ABI calls these regs a4 - a7. Clarify this.
  if (reg_arg_count > 4) set_register(a4, arguments[4]);
  if (reg_arg_count > 5) set_register(a5, arguments[5]);
  if (reg_arg_count > 6) set_register(a6, arguments[6]);
  if (reg_arg_count > 7) set_register(a7, arguments[7]);
```

`Simulator::CallImpl()` invokes `Simulator::CallInternal()`. The latter is mainly for checking whether callee-saved-registers are preserved during the simulated execution of `entry`. Note that callee-saved registers should be preserved by the callee. So I think the register handlings in `Simulator::CallInternal()` is probably for checking and debugging purposes.

#### Calling JS Functions

Invoking a JSFunction involves a sequence of builtin functions: `JSEntry` builtin -> `JSEntryTrampoline` builtin -> `CallFunction` builtin -> compiled JS codes. 

When invoking a JS function, the following needs to be set up or passed into the callee function in certain registers:
- A root register (defined as `kRootRegister`) points to a sub-region of the `Isolate` object of the current execution environment. It can be used to access isolate-specific data such as roots, external references, builtins, etc.; 
- A context register (defined as `cp`) that points to the current context of the execution environment (some times may need to create a new script context);
- The receiver object (could be `NULL);
- The starting binary address of a non-constructor callee function (i.e., `target`);
- The starting binary address of a constructor (w/ `new`)  callee function (i.e., `new_target`);
- Argument lists;
- Setup JS frame for the callee;
- Exception handling: prepare meta-info needed for exception handling, and checking for exception between and after invoking the code

##### Platform-independent `Invoke(...)` Helper 

The entry-point to invoke a JS or builtin function is via the helper function `Invoke()` (defined in `execution/execution.cc`).
```
V8_WARN_UNUSED_RESULT MaybeHandle<Object> Invoke(Isolate* isolate, const InvokeParams& params);
```
where `Invoke()` is used to implement pretty much every times of JS calls, e.g., `Execution::Call()`, `Execution::CallBuiltin()`, `Execution::New()`, `Execution::TryCall()`, `Execution::TryRunMicrotasks()`.

`Invoke()` consists of the following steps:
- Fastpath for `Builtins::InvokeApiFunction()`
- setup script context (if needed be)
- Check for exception and callbacks
- Invoke built-in function `JSEntry` through `GeneratedCode<...>.Call(...)` interface: 
  ```
  Handle<Code> code = JSEntry(isolate, params.execution_target, params.is_construct);
  {
    // Save and restore context around invocation and block the
    // allocation of handles without explicit handle scopes.
    ...
    if (params.execution_target == Execution::Target::kCallable) {
      // {new_target}, {target}, {receiver}, return value: tagged pointers
      // {argv}: pointer to array of tagged pointers
      using JSEntryFunction = GeneratedCode<Address(
          Address root_register_value, Address new_target, Address target,
          Address receiver, intptr_t argc, Address** argv)>;
      JSEntryFunction stub_entry = JSEntryFunction::FromAddress(isolate, code->InstructionStart());

      Address orig_func = params.new_target->ptr();
      Address func = params.target->ptr();
      Address recv = params.receiver->ptr();
      Address** argv = reinterpret_cast<Address**>(params.argv);
      RuntimeCallTimerScope timer(isolate, RuntimeCallCounterId::kJS_Execution);
      value = Object(stub_entry.Call(isolate->isolate_data()->isolate_root(),
                                     orig_func, func, recv, params.argc, argv));
    } else {
      // return value: tagged pointers
      // {microtask_queue}: pointer to a C++ object
      using JSEntryFunction = GeneratedCode<Address(Address root_register_value, MicrotaskQueue* microtask_queue)>;
      JSEntryFunction stub_entry = JSEntryFunction::FromAddress(isolate, code->InstructionStart());
      value = Object(stub_entry.Call(isolate->isolate_data()->isolate_root(), params.microtask_queue));
    }
  }
  ```

##### Target-specific: `JSEntry()` Builtin

All JS codes are invoked through `JSEntry` variant builtins (i.e., `JSEntry`, `JSConstructorEntry`, `JSRunMicrotasksEntry`), as shown in the implementation of `Invoke()`. A`code` object called `JSEntry` is created that is eventually is used in `GeneratedCode<...>.Call(...)`):
```
Handle<Code> code = JSEntry(isolate, params.execution_target, params.is_construct);
```
where `JSEntry` is defined as follows
```
Handle<Code> JSEntry(Isolate* isolate, Execution::Target execution_target,
                     bool is_construct) {
  if (is_construct) {
    return BUILTIN_CODE(isolate, JSConstructEntry);
  } else if (execution_target == Execution::Target::kCallable) {
    return BUILTIN_CODE(isolate, JSEntry);
  } else if (execution_target == Execution::Target::kRunMicrotasks) {
    return BUILTIN_CODE(isolate, JSRunMicrotasksEntry);
  }
  UNREACHABLE();
}
```
The `JSEntry()` has the following prototype:
```
// Called with the native C calling convention. The corresponding function
// signature is either:
//
//   using JSEntryFunction = GeneratedCode<Address(
//       Address root_register_value, Address new_target, Address target,
//       Address receiver, intptr_t argc, Address** args)>;
// or
//   using JSEntryFunction = GeneratedCode<Address(
//       Address root_register_value, MicrotaskQueue* microtask_queue)>;
```

The `JSEntry()` binary expects arguments are passed in to argument registers of MIPS64 (i.e., `a0` - `a5`) as follows:
```
 // Registers:
    //  either
    //   a0: root register value
    //   a1: entry address
    //   a2: function
    //   a3: receiver
    //   a4: argc
    //   a5: argv
    //  or
    //   a0: root register value
    //   a1: microtask_queue
    //
    // Stack:
    // 0 arg slots on mips64 (4 args slots on mips)
```

As shown in `Builtins::Generate_JSEntryVariant()`, the builtin function implements common target-specific (i.e., those involving machine registers) callee-side handling:
- save callee saved registers on the stack
  ```
    // Save callee saved registers on the stack.
    __ MultiPush(kCalleeSaved | ra.bit());
    // Save callee-saved FPU registers.
    __ MultiPushFPU(kCalleeSavedFPU);
  ```
- move `a0` to `kRootRegister` () 
- build an `EntryFrame`
- set `js_entry_sp` value (if it is outermost JS call)
- setup for exception handling
- invoke `JSEntryTrampoline()` builtin
   ```
     // Invoke the function by calling through JS entry trampoline builtin and
     // pop the faked function when we return.
     Handle<Code> trampoline_code = masm->isolate()->builtins()->builtin_handle(entry_trampoline);
     __ Call(trampoline_code, RelocInfo::CODE_TARGET);
  ```
  Note that `JSEntryTrampoline()` is "inovoked" via `__ Call(trampoline_code,...)` (where `Call` is defined in `TurboAssembler` as a couple of branch-and-link instructions). Unlike invoking through C function calls, there is no additional caller-side prologue and epilogue handling. In fact, `kRootRegister` and argument registers such as `a1-a5` are preserved (or passed) from `JSEntry` to `JSEntryTrampoline`.

- restore the frame and callee-saved registers

##### Target-specific: `JSEntryTrampoline()` builtin

`JSEntryTrampline()` performs more setups for JS function calls according to JS calling convention. These trampolines are generated via `Builtins::Generate_JSEntryTrampolineHelper` (in `builtins/mips64/builtins-mips64.cc`). 

Since `JSEntryTrampoline()` was "invoked" by `JSEntry()` by a couple of branch-and-link instructions (generated by `TurboAssembler::Call()`) instead of via a C function call. We have to deduce the states being passed from `JSEntry()` to `JSEntryTrampoline()`:  
```
  // ----------- S t a t e -------------
  //  -- a0: root_register_value (unused)
  //  -- a1: new.target
  //  -- a2: function
  //  -- a3: receiver_pointer
  //  -- [fp + kPushedStackSpace + 0 * kPointerSize]: argc
  //  -- [fp + kPushedStackSpace + 1 * kPointerSize]: argv
  // -----------------------------------
```

`JSEntryTrampoline()` performs the following actions:
- Set up JS context register `cp`, re-arrange the order of `a0`, `a1`, `a3` (for later call to `CallFunction()` builtin)
- Push all arguments on the JS stack through `sp` 
- Initialize all JS callee-saved registers (since they will be seen by the GC as part of handlers)
   ```
    __ LoadRoot(a4, RootIndex::kUndefinedValue);
    __ mov(a5, a4);
    __ mov(s1, a4);
    __ mov(s2, a4);
    __ mov(s3, a4);
    __ mov(s4, a4);
    __ mov(s5, a4);
   ```
- "Invoke" `CallFunction` builtin variant `kCallFunction_ReceiverIsAny` (Note: there are other flavors of `CallFunction` such as `kCallFunction_ReceiverIsNullOrUndefined` or `kCallFunction_ReceiverIsNotNullOrUndefined`).
   ```
   // a0: argc
   // a1: function
   // a3: new.target
   Handle<Code> builtin = is_construct ? BUILTIN_CODE(masm->isolate(), Construct) : masm->isolate()->builtins()->Call();
   __ Call(builtin, RelocInfo::CODE_TARGET);
   ```

##### Target-specific: `CallFunction()` Builtins

`CallFunction()` Builtin is generated by `Builtins::Generate_CallFunction()`. It does the final preparation for a JS call following JS calling convention. 

Similar to `JSTrampoline()`, `CallFunction()` is "invoked" by a couple of branch-and-link instructions (generated by `TurboAssembler::Call()` instead of via a C function call. Therefore, we must deduce the states passed into `CallFunction()`. 
```
  // ----------- S t a t e -------------
  //  -- a0 : the number of arguments (not including the receiver)
  //  -- a1 : the function to call (checked to be a JSFunction)
  // -----------------------------------
```
Note that:
- Besides `a0` and `a1`, `kRootRegister`, `cp` (context register), `sp` (stack register) are probably still valid
- And arguments have been pushed to the JS frame (see `JSEntryTrampoline()` description above)

`CallFunction()` performs the following JS-specific setups:
- Check that the JS function is not a constructor (which requires a different interface)
- Enter the context of the function. `ToObject` has to run in the function context, and we need to the global proxy from the function context in case of conversion
- Invoke the JS function (i.e., `a1`)
  ```
  // ----------- S t a t e -------------
  //  -- a0 : the number of arguments (not including the receiver)
  //  -- a1 : the function to call (checked to be a JSFunction)
  //  -- a2 : the shared function info.
  //  -- cp : the function context.
  // -----------------------------------
  __ Lhu(a2,
         FieldMemOperand(a2, SharedFunctionInfo::kFormalParameterCountOffset));
  __ InvokeFunctionCode(a1, no_reg, a2, a0, JUMP_FUNCTION);
  ```
- Error handling if JSFunction is a constructor

#### Calling a JS function to be Interpreted

As defined in `builtins/mips64/builtins-mips64.cc`, `Builtins::Generate_InterpreterEntryTrampoline()` generates codes for entering a JS function with the interpreter.
```
// Generate code for entering a JS function with the interpreter.
// On entry to the function the receiver and arguments have been pushed on the
// stack left to right.  The actual argument count matches the formal parameter
// count expected by the function.
//
// The live registers are:
//   o a1: the JS function object being called.
//   o a3: the incoming new target or generator object
//   o cp: our context
//   o fp: the caller's frame pointer
//   o sp: stack pointer
//   o ra: return address
//
// The function builds an interpreter frame.  See InterpreterFrameConstants in
// frames.h for its layout.
void Builtins::Generate_InterpreterEntryTrampoline(MacroAssembler* masm)
```

#### Calling WASM Functions: `Execution::CallWasm()`

(TO BE ADDED)

### Linkage

#### Internal Linkage Data Structures

In `compiler/linkage.cc` and `compiler/linkage.h`, V8 defines several linkage data structures such as
- class `LinkageLocation`: describes the location for a parameter or a return value to a call 
- class `CallDescriptor`: describes a call to various parts of the compiler (e.g., compiler, codegen, . Every call has the notion of a "target" which is the first input to the call, e.g.,
    ```C
    enum Kind {
      kCallCodeObject,         // target is a Code object
      kCallJSFunction,         // target is a JSFunction object
      kCallAddress,            // target is a machine pointer (a C function call)
      kCallWasmCapiFunction,   // target is a Wasm C API function
      kCallWasmFunction,       // target is a wasm function
      kCallWasmImportWrapper,  // target is a wasm import wrapper
      kCallBuiltinPointer,     // target is a builtin pointer
    };
   ```
   Can see an example of using `CallDescriptor` in `CodeGenerator::AssembleConstructFrame()` and `CodeGenerator::AssembleReturn()` in `compiler/backend/mips64/code-generator-mips64.cc`.

- class `Linkage`:  it describes 4 different call prototypes, i.e., `CodeStub`, `JS`, `Runtime`, `BytecodeDispatch`
  ```
  // Defines the linkage for a compilation, including the calling conventions
  // for incoming parameters and return value(s) as well as the outgoing calling
  // convention for any kind of call. Linkage is generally architecture-specific.
  //
  // Can be used to translate {arg_index} (i.e. index of the call node input) as
  // well as {param_index} (i.e. as stored in parameter nodes) into an operator
  // representing the architecture-specific location. The following call node
  // layouts are supported (where {n} is the number of value inputs):
  //
  //                        #0          #1     #2     [...]             #n
  // Call[CodeStub]         code,       arg 1, arg 2, [...],            context
  // Call[JSFunction]       function,   rcvr,  arg 1, [...], new, #arg, context
  // Call[Runtime]          CEntry,     arg 1, arg 2, [...], fun, #arg, context
  // Call[BytecodeDispatch] address,    arg 1, arg 2, [...]
  ```
- `compiler/c-linkage.cc` defines target-specific configuration for C calling convention. For instance,
  ```
  #elif V8_TARGET_ARCH_MIPS64
    // ===========================================================================
    // == mips64 =================================================================
    // ===========================================================================
    #define PARAM_REGISTERS a0, a1, a2, a3, a4, a5, a6, a7
    #define CALLEE_SAVE_REGISTERS                                                  \
      s0.bit() | s1.bit() | s2.bit() | s3.bit() | s4.bit() | s5.bit() | s6.bit() | s7.bit()
    #define CALLEE_SAVE_FP_REGISTERS \
      f20.bit() | f22.bit() | f24.bit() | f26.bit() | f28.bit() | f30.bit()
  #elif V8_TARGET_ARCH_PPC64
  ```

Linkage classes are mostly used in the platform-independent part of V8 (e.g., TF graph generation).

#### Builtin Functions Built w/ Different Linkage Conventions

As defined in `init/setup-builtins-internal.cc`, builtin functions w/ different linkage are built differently. For instance, 
- JS linkage expects `argc_with_recv` as arguments (see `CodeAssemblerState`)

  ```
  // Builder for builtins implemented in TurboFan with JS linkage.
  Code BuildWithCodeStubAssemblerJS(Isolate* isolate, int32_t builtin_index,
                                  CodeAssemblerGenerator generator, int argc,
                                  const char* name) {
    ...
    compiler::CodeAssemblerState state(
      isolate, &zone, argc_with_recv, Code::BUILTIN, name,
      PoisoningMitigationLevel::kDontPoison, builtin_index);
    generator(&state);
    Handle<Code> code = compiler::CodeAssembler::GenerateCode(
      &state, BuiltinAssemblerOptions(isolate, builtin_index));
    return *code;
  }
  ```

- For CallStub linkage expects `CallInterfaceDescriptor` as arguments (see `CodeAssemblerState`) 
  ```
  // Builder for builtins implemented in TurboFan with CallStub linkage.
  Code BuildWithCodeStubAssemblerCS(Isolate* isolate, int32_t builtin_index,
                                  CodeAssemblerGenerator generator,
                                  CallDescriptors::Key interface_descriptor,
                                  const char* name) {
    ...
    // The interface descriptor with the given key must be initialized at this point
    // and this construction just query the details from the descriptors table.
    CallInterfaceDescriptor descriptor(interface_descriptor);
    // Ensure descriptor is already initialized.
    DCHECK_LE(0, descriptor.GetRegisterParameterCount());
    compiler::CodeAssemblerState state(
      isolate, &zone, descriptor, Code::BUILTIN, name,
      PoisoningMitigationLevel::kDontPoison, builtin_index);
    generator(&state);
    Handle<Code> code = compiler::CodeAssembler::GenerateCode(
      &state, BuiltinAssemblerOptions(isolate, builtin_index));
    return *code;
  }
  ```
- For Bytecode dispatch linkage, expects `InterpreterDispatchDescriptor` (as defined in `interpreter/interpreter-generator.cc`) 

  ```
  Handle<Code> GenerateBytecodeHandler(Isolate* isolate, const char* debug_name,
                                     Bytecode bytecode,
                                     OperandScale operand_scale,
                                     int builtin_index,
                                     const AssemblerOptions& options) {
    Zone zone(isolate->allocator(), ZONE_NAME);
    compiler::CodeAssemblerState state(
      isolate, &zone, InterpreterDispatchDescriptor{}, Code::BYTECODE_HANDLER,
      debug_name,
      FLAG_untrusted_code_mitigations
          ? PoisoningMitigationLevel::kPoisonCriticalOnly
          : PoisoningMitigationLevel::kDontPoison,
      builtin_index);
    ...
    Handle<Code> code = compiler::CodeAssembler::GenerateCode(&state, options);
  ```