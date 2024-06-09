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
| Operations | - Simple operations                             | - Transcendental functions                              |
|            | - Single cycle instructions                     | - Multicycle instructions                               |
+------------+-------------------------------------------------+---------------------------------------------------------+
| Operands   | - Memory and logical instructions are separate  | - Logical instructions operands can be memory addresses |
|            | - Few addressing modes                          | - Many addressing modes                                 |
+------------+-------------------------------------------------+---------------------------------------------------------+

Table: Summary of RISC and CISC Principles [@power-struggles]

All of the following processor families' have instruction set architectures (ISAs).
ISAs define how software interfaces with the processors hardware, through specifying
available instructions, types, addressing modes, and encoding [@isa-def]. The ISAs
belong to two distinct architectures, reduced instruction set computers (RISC),
and complex instruction set computers (CISC). These design philosophies affect
the families' architecture and operation greatly; therefore, the two philosophies
principles and differences must be explored to better analyse the upcoming
processors details and differences.

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
compiler [@what-risc]. Whereas, CISC has a looser definition as the term
was retroactively defined to mean non-RISC[@john-mashey]. However, a typical CISC processor
would have: variable length instructions, more addressing modes such as indirect
addressing, and instructions that require multiple processor cycles [@power-struggles].

Though RISC and CISC processors prioritise different design principles, neither
are inherently more efficient, designed for mobile, or faster. These
characteristics are affected far more by the design of the processor and its
microarchitecture; not the instruction set architecture. et AL [-@power-struggles]
conducted a study comparing 4 contemporary processors, 2 implementing the ARM ISA and 2
implementing x86, finding that 'Balancing power and performance leads to
energy-efficient cores, regardless of the ISA: A9 (ARM) and Atom (x86-64)
processor energy requirements are within 24% of each other and use up to 50%
less energy than other cores'. Overall, these processors are targetting
different performance and efficiency goals and that is reflected far more
in their core designs than ISA.

# PowerPC

![PowerPC Genealogy [@powerpc-hist]](images/powerpc-gena.png)

PowerPC originated with IBM's creation of the 801 processor. It was an experimental
design that used many of RISC's principles, although RISC architecture had
not been created yet, making 801 the first RISC processor [@ibm-risc-evo]. IBM 801
was designed to manage large scale telephone switching. Therefore the design was
specialised, needless instructions were removed, with fixed width instructions,
load store architecture and other RISC design principles [@801-computer].
The switching project was cancelled in 1975 however, the 801's potential as a
general-purpose, high-performance processor was promising. The research from
the IBM 801 was further developed. The 801 was scalar, designed to execute
one instruction every cycle, however derived processors had three independent
execution units: a branch, fixed-point, and floating-point processors.
Multiple instructions could be executed per cycle, making the processor
superscalar, also known as instruction-level parallelism [@system-6000-arch].
These developments evolved into the IBM RISC System/6000, using an ISA dubbed
POWER.

Meanwhile, Apple's CPU supplier, Motorola, was struggling to provide
competitive 68000 series processors whilst Microsoft-Intel based PCs were
highly competitive; therefore, Apple started experimenting with RISC processors
in the Aquarius project [@powerpc-history]. Noticing this,
IBM offered Apple an opportunity to create a family of POWER processors
to use in Apple's desktops. Apple proceeded to include Motorola in the
agreement due to their long relationship, and Motorola's experience in
large-scale processor manufacturing. The result was the Apple, IBM, and Motorola
alliance, AIM.

+-------------+------+------------------+-----------------+---------+--------+----------------------------+
| Processor   | Year | Clock rate (MHz) | L2 Cache (KB)   | SPECint | SPECfp | Reference                  |
+=============+======+==================+=================+=========+========+============================+
| Pentium     | 1993 | 66               | 256 (off-chip ) | 65.1    | 63.6   | [@netlib-pentium]          |
+-------------+------+------------------+-----------------+---------+--------+----------------------------+
| PPC 603     | 1994 | 66               | 256 (off-chip ) | 60.6    | 60.7   | [@netlib-603]              |
+-------------+------+------------------+-----------------+---------+--------+----------------------------+
| PPC 601     | 1993 | 66               | None            | 62.6    | 76.1   | [@netlib-601]              |
+-------------+------+------------------+-----------------+---------+--------+----------------------------+
| PPC 604     | 1994 | 120              | 1024 (off-chip) | 118.1   | 150.4  | [@netlib-604]              |
+-------------+------+------------------+-----------------+---------+--------+----------------------------+
| Pentium     | 1995 | 120              | 1024 (off-chip) | 108.4   | 172.2  | [@netlib-pentium-120]      |
+-------------+------+------------------+-----------------+---------+--------+----------------------------+
| Pentium Pro | 1995 | 150              | 256 (off-chip)  | 274.3   | 209.8  | [@netlib-pentium-pro]      |
+-------------+------+------------------+-----------------+---------+--------+----------------------------+

Table: SPEC 92 Benchmark Results for PowerPC and Intel Processors [@netlib]

+-------------+------+------------------+-----------------+---------+--------+----------------------------+
| Processor   | Year | Clock rate (MHz) | L2 Cache (KB)   | SPECint | SPECfp | Reference                  |
+=============+======+==================+=================+=========+========+============================+
| PPC 604 Pro | 1993 | 66               | 256 (off-chip ) | 65.1    | 63.6   | [@netlib-pentium]          |
+-------------+------+------------------+-----------------+---------+--------+----------------------------+
| Pentium Pro | 1994 | 66               | 256 (off-chip ) | 60.6    | 60.7   | [@netlib-603]              |
+-------------+------+------------------+-----------------+---------+--------+----------------------------+
| PPC 604e    | 1993 | 66               | None            | 62.6    | 76.1   | [@netlib-601]              |
+-------------+------+------------------+-----------------+---------+--------+----------------------------+

Table: SPEC 95 Benchmark Results for PowerPC and Intel Processors [@mac-info]

POWERPC POWER HUNGRY - REAFFIRMING THE STATEMENT MADE EARLIER, ITS ABOUT
PROCESSOR DESIGN, NOT INSTRUCTION ARCHITECTURE
application demand was low so software was not made for it except for macOS

# Intel

# AMD

# Apple Silicon ARM

# Conclusion

# Bibliography
