For an evolving ISA like RISC-V, it is common that we will have support for new instructions or new ISA extensions to the backend. In this post, we explain the steps to add a new instruction to an existing backend. In this case, we use the riscv64 backend as an example, but the process is similar to other backends as well.

### 1. Add constants (`src/codegen/riscv64/constants-riscv64.h`)

`constants-riscv64.h` defines constants that are related to instruction classes and encodings that are shared by the `Assembler`, `Disasm`, and `Simulator`. You will need to add new opcode to `enum Opcode_t` and other constants if it uses a new instruction format.  Sometimes, a new instruction uses a new class of registers. Then one needs to add new register classes to `register-riscv64.h`.  

### 2. Add to the assembler (`src/codegen/riscv64/assembler-riscv64.h/cc`)

Add a new `Assembler` API to `assembler-riscv64.cc` and `assembler-riscv64.h` as the generator for the new instruction. These `Assembler` APIs are typically named after the instruction name unless there is a naming conflict, such as `and` is named as `and_`.
```
// assembler-riscv64.h
void add(Register rd, Register rs1, Register rs2);

// assembler-riscv64.cc
void Assembler::add(Register rd, Register rs1, Register rs2) {
  GenInstrALU_rr(0b0000000, 0b000, rd, rs1, rs2);
}
```
If the instruction uses a new instruction format, one needs to add a new generator function for this new instruction format (e.g., `GenInstrALU_rr`).

### 3. Add to the simulator (`src/codegen/riscv64/simulator-riscv64.h/cc`)

Implement the behavior of the instruction in the simulator as shown blow:
```
void Simulator::DecodeRVRType() {
  switch (instr_.InstructionBits() & kRTypeMask) {
    case RO_ADD: {
      set_rd(sext_xlen(rs1() + rs2()));
      break;
    }
```
Note that constants like `kRTypeMask` is defined in `constants-riscv64.h` and is shared by both `Assembler`, `Simulator`, and `Disasm`.

### 4. Test the instruction

Once an instruction is added to the `Assembler` and the `Simulator`, it can be tested under the [simulator build](simulator-build) run. The new instruction needs to be added `test/cctest/test-assembler-riscv64.cc` 
```
UTEST_R2_FORM_WITH_OP(add, int64_t, LARGE_INT_EXCEED_32_BIT, MIN_VAL_IMM12, +)
```
which is a macro that specifies two input operands to `add` and compare it against the expected result computing by `+` the two operands. 

### 5. Add to the disassembler (`src/diagnostics/riscv64/disasm-riscv64.h/cc`)

Add the disassembler for the new instruction, as shown below:
```
void Decoder::DecodeRType(Instruction* instr) {
  switch (instr->InstructionBits() & kRTypeMask) {
    case RO_ADD:
      Format(instr, "add       'rd, 'rs1, 'rs2");
      break;
```

### 6. Test disassembler

Add a test for disassembly the new instruction in `test/cctest/test-disam-riscv64.cc` as shown below:
```
  COMPARE(add(s6, t0, t4), "01d28b33       add       s6, t0, t4");
```

### 7. Use the new instruction

Simply adding a new instruction to the `Assembler` does not mean that the instruction is generated during code-gen. You have to use these "assembler" APIs in the code-gen routines such as in `TurboAssembler` (`src/codegen/riscv64/macro-assembler-riscv64.cc`), in ASM built-in functions (`src/builtins/riscv64/builtins-riscv64.cc`), during the lowering from TF machine-node IR to target-specific codes (`src/compiler/backend/riscv64/code-generator-riscv64.cc`), in the lowering from WASM IR to target-specific codes in the liftoff-compiler (`src/wasm/riscv64/liftoff-assembler-riscv64.h`), in regexp assembler (`src/regexp/riscv64/regexp-assembler-riscv64.cc`).

Sometimes a sequence of TF machine-node IRs may be represented by the new instruction being added. In this case, you need to introduce a new architecture-specific machine-node IR and add the new instruction to the instruction selector (`src/compiler/backend/riscv64/instruction-selector-riscv64.cc`), and a test case for the instruction selection (`test/unittests/riscv64/instruction-selection-riscv64-unittest.cc`).
 



