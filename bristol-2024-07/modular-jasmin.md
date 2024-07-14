Goals:
1. make reg alloc less fragile (e.g., we don't want a tiny change in SHA-3 to break Dilithium build)
2. be able to swap implementation of a function "at link time" (e.g., externally-provided SHA-3 library with a custom
+assembly wrapper to match the C ABI, assembly stub that implement syscalls)


Main idea: define "jexport" functions, that follow a Jasmin-defined ABI for Jasmin -> Jasmin calls.

Concrete plan:
1. Define the "jexport" ABI. - This solves goal 1. (although the lack of private functions would make it possible for Jasmin programmers to mess up - but that's not a blocking issue).
2. Have separate compilation units, and have a file format for compiled units. - That unlocks goal 2.: we can generate such a file by hand/ with another tool. (This generated file is trusted.)

Open questions:
* How to put speculation flags in the ABI?
* Caller-saved vs callee-saved registers
* How/when do we do the RSB-protection (rewriting of returns)
* jexport function would not benefit of reg optimization when returning reg ptr. We can probably work without this optimization.
* stack alignment: can we specify it to be constant (per platform), like 16 (x86_64), 4 (arm-m4)? otherwise it would have to be part of each function ABI
* stack allocation: if done by the caller, need to include the "own" stack usage in ABI
* stack clearing: can be done by adding total stack usage in the ABI ?
* file format: meta-data in comments of assembly file?
* how to define and manage compilation units (syntax? per-file?)
* is there a relationship between compilation units and namespaces?

