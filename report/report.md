---
title: "IS1S482 A Review of Processor Technologies"
author: [Jake Real - 23056792]
date: "03/06/2024"
toc-own-page: false
titlepage: true
table-use-row-colors: true
titlepage-background: "title-background.pdf"
bibliography: "references.bib"
csl: "university-of-south-wales-harvard.csl"
nocite: |
...

# Abstract

Apple announced their two year transition plan to internally-designed Apple
Silicon processors, ending the use of Intel x86-64 processors, at the
World Wide Developers Conference 2020 [@apple-announcement]. This report will
explore the transition's motives and results through investigating
the history and characteristics of other contemporary processor families,
comparing them to Apple's M1 series. Information was sourced through academic
reports, press releases, news articles, and the processor designer's
documentation.

**RESULTS to add**

**CONCLUSIONS to add**

# Reduced Instruction Set Computers & Complex Instruction Set Computers

+------------+-------------------------------------------------+---------------------------------------------------------+
|            | RISC                                            | CISC                                                    |
+============+:===============================================:+:=======================================================:+
| Format     | - Fixed length instructions                     | - Variable length instructions                          |
|            | - Simple encoding                               | - Common instructions are shorter                       |
|            | - e.g ARM 4 bytes                               | - Special instructions are longer                       |
|            |                                                 | - e.g x86 1 byte to 16 bytes long                       |
+------------+-------------------------------------------------+---------------------------------------------------------+
| Operations | - Simple operations                             | - Transcedental functions                               |
|            | - Single cycle instructions                     | - Multicycle instructions                               |
+------------+-------------------------------------------------+---------------------------------------------------------+
| Operands   | - Memory and logical instructions are separate  | - Logical instructions operands can be memory addresses |
|            | - Few addressing modes                          | - Many addressing modes                                 |
+------------+-------------------------------------------------+---------------------------------------------------------+

Table: Summary of RISC and CISC Principles [@power-struggles]

All of the following processor families belong to two distinct computer architectures,
reduced instruction set computers (RISC), and complex instruction set computers
(CISC). These design philosophies affect the families architectures and
operation greatly; therefore, the two philosophies principles and differences must
be explored to better analyse the families details, differences, and practices.

As defined by John Mashey [-@risc-userpages], a contributor to the MIPS RISC
architecture [@john-mashey], most RISC architectures have: a load-store
model that separates instructions into distinct memory access and logical operations -
'no operations that combine load/store with arithmetic i.e., like
add from memory, or add to memory', no more than one operand that accesses a
memory address per instruction, standard fixed instruction sizes, simple
encoding, and a small amount of address modes. Crucially, the number of
instructions does not determine whether a processor is RISC. Instead, RISC is
a loose set of architectures, sharing some of the above features, that aim to
achieve efficient execution by a pipelined processor using an optimising
compiler [@what-risc].

**RISC is not inherently lower power, or a mobile ISA. It is just collection of
instruction set characterises .

# PowerPC

# Intel

# AMD

# Apple Silicon ARM

# Conclusion

# Bibliography
