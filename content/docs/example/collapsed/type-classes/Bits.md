---
bookCollapseSection: false
title: "Bits"
weight: 20
---

Bits defines the class of types that can be converted to bit vectors and back. Membership in this class is required for a data type to be stored in a state, such as a Register or a FIFO, or to be used at a synthesized module boundary. Often instance of this class can be automatically derived using the deriving statement.

```verilog
typeclass Bits #(type a, numeric type n);
    function Bit#(n) pack(a x);
    function a unpack(Bit#(n) x);
endtypeclass
```