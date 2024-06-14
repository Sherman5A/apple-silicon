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

Though RISC and CISC processors prioritise different design principles, today neither
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
| Pentium     | 1993 | 66               | 256 (off-chip)  | 65.1    | 63.6   | [@netlib-pentium]          |
+-------------+------+------------------+-----------------+---------+--------+----------------------------+
| PPC 601     | 1993 | 66               | None            | 62.6    | 76.1   | [@netlib-601]              |
+-------------+------+------------------+-----------------+---------+--------+----------------------------+
| PPC 603     | 1994 | 66               | 256 (off-chip)  | 60.6    | 60.7   | [@netlib-603]              |
+-------------+------+------------------+-----------------+---------+--------+----------------------------+
| PPC 604     | 1994 | 120              | 1024 (off-chip) | 118.1   | 150.4  | [@netlib-604]              |
+-------------+------+------------------+-----------------+---------+--------+----------------------------+
| Pentium     | 1995 | 120              | 1024 (off-chip) | 172.2   | 108.4  | [@netlib-pentium-120]      |
+-------------+------+------------------+-----------------+---------+--------+----------------------------+
| Pentium Pro | 1995 | 150              | 256 (off-chip)  | 274.3   | 209.8  | [@netlib-pentium-pro]      |
+-------------+------+------------------+-----------------+---------+--------+----------------------------+

Table: SPEC 92 Base Benchmark Results for a selection of PowerPC and Intel Processors [@netlib]

+--------------+------+------------------+---------+--------+-------------+
| Processor    | Year | Clock rate (MHz) | SPECint | SPECfp | Reference   |
+============+=+======+==================+=========+========+=============+
| PPC 604      | 1994 | 120              | 4.7     | 4.4    | [@mac-info] |
+--------------+------+------------------+---------+--------+-------------+
| PPC 603e     | 1994 | 117              | 3.5     | 2.7    | [@mac-info] |
+--------------+------+------------------+---------+--------+-------------+
| Pentium      | 1995 | 120              | 3.7     | 2.8    | [@mac-info] |
+--------------+------+------------------+---------+--------+-------------+
| Pentium Pro  | 1995 | 150              | 6.1     | 5.4    | [@mac-info] |
+--------------+------+------------------+---------+--------+-------------+
| PPC 604e     | 1996 | 166              | 7.7     | 6.3    | [@mac-info] |
+--------------+------+------------------+---------+--------+-------------+
| PPC G3 (750) | 1997 | 233              | 11.0    | 8.1    | [@mac-info] |
+--------------+------+------------------+---------+--------+-------------+
| Pentium Pro  | 1997 | 200              | 11.0    | 8.1    | [@mac-info] |
+--------------+------+------------------+---------+--------+-------------+
| Pentium II   | 1997 | 233              | 9.5     | 6.4    | [@mac-info] |
+--------------+------+------------------+---------+--------+-------------+

Table: SPEC 95 Peak Benchmark Results for a selection of PowerPC and Intel Processors [@mac-info]

As seen in table two, early PowerPC benchmarked well against
Intel processors. Initial PowerPC processors, like the 601, were very competitive
with Pentiums, beating them in floating-point performance whilst losing in
integer benchmarks. However, the Pentiums were running at a far higher wattage than comparable
PowerPC processors, the 66 MHz 601 dissipated seven watts whereas the 66 MHz Pentium
dissipated thirteen watts resulting in 85% more power consumption [@power-mac-book].
However, The PowerPC 603 is a major anomaly; the 66 MHz Pentium performs better in
integer and floating-point performance. This is because, 603 was designed as
a portable processor, drawing 3 watts and sacrificing features. Moreover, poor
Motorola 68000 emulation combined with early, emulation-reliant macOS led to
poor stability and performance [@power-mac-book]. PowerPC continued to remain
competitive in performance throughout the AIM alliances life. However, it
never saw major usage in the Intel-Microsoft dominated PC market, therefore,
third-party application support and infrastructure was lacking external of macOS.
Apart from Apple customers, the architecture was ignored [@powerpc-to-intel].
Furthermore, complications arose within the AIM alliance; Motorola exited the
processor industry, creating Freescale Semiconductor, and IBM started to focus
on PowerPC in game consoles, complicating the alliance.
Moreover, the PowerPC G5, despite great benchmarks, struggled to meet Apple's demands.
Steve Jobs explained at WWDC [-@jobs-intel], 'Now, I stood up here two years
ago in front of you and I promised you [a 3 GHz Power Macintosh G5], and we
haven't been able to deliver that to you yet. I think a lot of you would like a
G5 in your PowerBook and we haven't been able to deliver that to you yet'.
AIM was unable to take the G5, a high-performance desktop processor, and
scale it upwards towards GHz, or downwards to meet laptop efficiency targets.
In the growing laptop market, this convinced Apple to transition to
Intel, which who offered the mobile Core series.

# Intel

Most of Intel's contemporary processors use the CISC Intel 64 instruction set;
Intel's' implementation of x86-64 ISA which is a 64 bit extension to x86. x86
has a long history, originating in 1978 when Intel released the 8086 and 8088,
the two first x86 processors. The 8086 8088 were 16-bit processors, with, the
8088 being a cheaper alternative using a reduced data bus [@intel-timeline]. The
8088 caught the attention of IBM, a business computer system manufacturer who
wanted to enter the growing consumer microcomputer market. IBM, who typically
designed all their hardware, used the Intel 8088 due to the competitive price
and time pressure. The IBM PC greatly exceeded sales expectations. Due to the
use of third-party components, after reverse-engineering the PC's firmware
clones of the PC arose [@ibm-pc]. These also used Intel processors, growing
Intel into an industry giant. Intel continued improving x86 throughout the
eighties, adding 32-bit support, on-processor cache, and superscalar processing
to x86. However, the RISC philosophy's popularity was increasing, with RISC
designs outperforming CISC. Despite this, Intel further invested into the CISC
x86, creating the Pentium Pro's P6 microarchitecture. P6 has been the basis
for nearly all of Intel's future processors, including Intel Core, Atom, Pentium
and Celeron, except Pentium 4.

# AMD

# Apple Silicon ARM

# Conclusion

# Bibliography
