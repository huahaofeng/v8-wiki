For an evolving ISA like RISC-V, it is common that we will have support for new instructions or new ISA extensions to the backend. In this post, we explain the steps to add a new instruction to an existing backend. In this case, we use the riscv64 backend as an example, but the process is similar to other backends as well.

1. Add constants (`constants-riscv64.h`)

`constants-riscv64.h` defines constants that are related to instruction classes and encodings that are shared by the `Assembler`, `Disasm`, and `Simulator`. You will need to add new opcode to `enum Opcode_t` and other constants if it uses a new instruction format.  Sometimes, a new instruction uses a new class of registers. Then one needs to add new register classes to `register-riscv64.h`.  

2. Add to the assembler (`assembler-riscv64.h/cc`)

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

3. Add to the simulator (`simulator-riscv64.h/cc`)

Implement the behavior of the instruction in the simulator. 
```

```

