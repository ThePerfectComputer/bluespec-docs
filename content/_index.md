---
title: Introduction
type: docs
---

# Hello!

This is my attempt to make documentation for Bluespec more accessible. If this is your first exposure to Bluespec, I highly recommend learning Bluespec Haskell as most of my documentation efforts here are biased towards Bluespec Haskell.

This documentation is mostly manual tex -> md a port of the docs from [bsc docs](https://github.com/B-Lang-org/bsc/tree/main/doc).

There is a lot to document, feel free to contribute [here](https://github.com/ThePerfectComputer/bluespec-docs).

I have a dayjob, so I'll port what I can when I get a chance. Yes, I [already tried](https://github.com/B-Lang-org/bsc/issues/377#issuecomment-1378079031) using pandoc.

I'e also begun looking through the bsc compiler sources, and may be able to write a pass that pulls out a bunch of the bsc docs automatically. More on this soon.

# Why Bluespec?

There are a lot of High Level HDLs(HLHDLs). Whilst many of the HLHDLs focus on offering a better type system and offering parameterization, BlueSpec goes a step further and helps the digital logic designer avoid mistakes at a formal compiler level. To this end, I've made the following table to help the reader get a feel for where BlueSpec might sit amongst peers in HDL land.

| Hardware Language | Software Language |
|-------------------|-------------------|
| Bluespec          | Rust              |
| Chisel            | C                 |
| Verilog           | Assembly          |
| Netlist           | Opcodes           |
| Clash             | Haskell           |

# Technical Strengths

Bluespec has been traditionally advertised as being quite powerful due to it's one at a time semantics and its term-rewriting-system. If you are coming from Verilog and have no idea what that is, don't worry - I try and cover the best parts of Bluespec in the following list:

1. It has a fantastic library ecosystem that includes FIFOs, aynchronous FIFOs, arbiters, and more.

2. The compiler has a lot of fancy features such as algebraic data types, guarded atomics, and one at a time semantics that basically help you ensure and sometimes can even prove that you've correctly hooked up your various arbiters, FIFOs, aFIFOs amongst other things.

3. It has tagged unions and full blown pattern matching, which make implementing certain pieces of logic such as a CPU decoder incredibly easy. Consider the following from Bluespec Haskell, where we specify how to map a decoded instruction to a memory stage instruction, just by performing pattern matching on types.

   Check out [Algebraic Data Types](https://wiki.haskell.org/Algebraic_data_type) if the following snippet feels foreign.

   Check out the [alternate translation](./#bluespec-classichaskell-to-bluespec-verilog) if you find the following example hard to grok.

```haskell
-- Decoded instruction
data DInstr
        = Oload  {              dest  :: CPUReg; addr :: Value; offs :: Value }
        | Ostore {              datum :: Value;  addr :: Value; offs :: Value }
        | Oarith { aop :: AOp;  dest  :: CPUReg; v1   :: Value; v2   :: Value }
        | Owback {                             dest :: CPUReg; datum :: Value }
    deriving (Bits)

type DBuffer = FoldFIFO DInstr

-- Memory stage operation
data MInstr
        = Mload  { dest :: CPUReg; addr  :: Value; }
        | Mstore { datum :: Value; addr  :: Value; }
        | Mwback { dest :: CPUReg; datum :: Value; }
    deriving (Bits)

 -- Since Bluespec borrows a lot of concepts from purely functional programming
 -- languages such as haskell, where a function maps a set to another set, we
 -- can, loosely speaking, specify the full behavior of the function following 
 -- function execute, by specifying what happens when it maps subsets of DInstr
 -- (Decoded Instructions) to subsets of MInstr(Memory Instructions).
execute :: DInstr -> MInstr
execute (Oload  { dest;  addr;  offs }) = Mload  { dest  = dest;  addr = addr + offs }
execute (Ostore { datum; addr;  offs }) = Mstore { datum = datum; addr = addr + offs }
execute (Oarith { dest; aop; v1; v2; }) = Mwback { dest = dest; datum = computeOp aop v1 v2 }
execute (Owback { dest;        datum }) = Mwback { dest = dest; datum = datum }
```

4. Guards on module methods. For example, the default behavior of a FIFO is that if the FIFO is full, anything that depends on placing it's result in a FIFO will stall until the FIFO has room. Bluespec accomplishes this using something called gaurded atomics.

5. Comes with it's own simulator(Bluesim), but also works with iVerilog, Verilator, and others.


# Bluespec Classic/Haskell to Bluespec Verilog

For simpler logic, Bluespec Verilog is easier to write than Bluespec Haskell, as the following snippet(a Bluespec Verilog translation of the Bluespec Haskell snippet from above) would seem to suggest, however, I would argue that for more complex systems, Bluespec Haskell is more readable.

```verilog
typedef union tagged {
    DInstr_Oload Oload;
    DInstr_Ostore Ostore;
    DInstr_Oarith Oarith;
    DInstr_Owback Owback;
} DInstr deriving (Bits);

typedef struct {
    CPUReg dest;
    Value addr;
    Value offs;
} DInstr_Oload deriving (Bits);

typedef struct {
    Value datum;
    Value addr;
    Value offs;
} DInstr_Ostore deriving (Bits);

typedef struct {
    AOp aop;
    CPUReg dest;
    Value v1;
    Value v2;
} DInstr_Oarith deriving (Bits);

typedef struct {
    CPUReg dest;
    Value datum;
} DInstr_Owback deriving (Bits);

typedef FoldFIFO#(DInstr) DBuffer;

typedef union tagged {
    MInstr_Mload Mload;
    MInstr_Mstore Mstore;
    MInstr_Mwback Mwback;
} MInstr deriving (Bits);

typedef struct {
    CPUReg dest;
    Value addr;
} MInstr_Mload deriving (Bits);

typedef struct {
    Value datum;
    Value addr;
} MInstr_Mstore deriving (Bits);

typedef struct {
    CPUReg dest;
    Value datum;
} MInstr_Mwback deriving (Bits);

execute :: function MInstr f(DInstr x1);

function execute(Oload { dest; addr; offs; });
  return ((Mload { dest : dest, addr : addr + offs}));
endfunction: execute

function execute(Ostore { datum; addr; offs; });
  return ((Mstore { datum : datum, addr : addr + offs}));
endfunction: execute

function execute(Oarith { dest; aop; v1; v2; });
  return ((Mwback { dest : dest, datum : computeOp(aop, v1, v2)}));
endfunction: execute

function execute(Owback { dest; datum; });
  return ((Mwback { dest : dest, datum : datum}));
endfunction: execute
```
