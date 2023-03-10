---
weight: 1
bookFlatSection: true
title: "Bluespec Libraries"
---
Bluespec SystemVerilog (BSV) is aimed at hardware designers who are using or expect to use Verilog IEE01 [^1], VHDL IEE02[^2], or SystemVerilog Acc04[^3] to design ASICs or FPGAs. BSV is based on a synthesizable subset of SystemVerilog, including SystemVerilog types, modules, module instantiation, interfaces, interface instantiation, parameterization, static elaboration, and “generate” elaboration. BSV can significantly improve the hardware designer’s productivity with some key innovations:

 - It expresses synthesizable behavior with Rules instead of synchronous always blocks. Rules are powerful concepts for achieving correct concurrency and eliminating race conditions. Each rule can be viewed as a declarative assertion expressing a potential atomic state transition. Although rules are expressed in a modular fashion, a rule may span multiple modules, i.e., it can test and affect the state in multiple modules. Rules need not be disjoint, i.e., two rules can read and write common state elements. The BSV compiler produces efficient RTL code that manages all the potential interactions between rules by inserting appropriate arbitration and scheduling logic, logic that would otherwise have to be designed and coded manually. The atomicity of rules gives a scalable way to avoid unwanted concurrency (races) in large designs.
 - It enables more powerful generate-like elaboration. This is made possible because in BSV, actions, rules, modules, interfaces and functions are all first-class objects. BSV also has more general type parameterization (polymorphism). These enable the designer to “compute with design fragments,” i.e., to reuse designs and to glue them together in much more flexible ways. This leads to much greater succinctness and correctness.
 - It provides formal semantics, enabling formal verification and formal design-by-refinement. BSV rules are based on Term Rewriting Systems, a clean formalism supported by decades of theoretical research in the computer science community [Ter03][4]. This, together with a judicious choice of a design subset of SystemVerilog, makes programs in BSV amenable to formal reasoning.

This manual is meant to be a stand-alone reference for BSV, i.e., it fully describes the subset of Verilog and SystemVerilog used in BSV. It is not intended to be a tutorial for the beginner. A reader with a working knowledge of Verilog 1995 or Verilog 2001 should be able to read this manual easily. Prior knowledge of SystemVerilog is not required.

[^1]: IEEE. IEEE Standard Verilog (R) Hardware Description Language, March 2001. IEEE Std 1364-2001.
[^2]: IEEE. IEEE Standard VHDL Language Reference Manual, IEEE Std 1076-1993, 2002.
[^3]: Accellera. SystemVerilog 3.1a Language Reference Manual: Accellera’s Extensions to Ver- ilog (R), 2004. See: www.accelera.org, www.systemverilog.org.