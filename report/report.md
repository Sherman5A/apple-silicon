---
title: "IS1S482 - Analysing Apple's Swap to In-House Processor Design"
author: [Jake Real - 23056792]
date: "20/06/2024"
toc-own-page: false
titlepage: true
table-use-row-colors: true
titlepage-background: "title-background.pdf"
bibliography: "references.bib"
csl: "university-of-south-wales-harvard.csl"
nocite: |
...

# Abstract

Apple announced their two-year transition plan to internally-designed Apple
Silicon processors, ending the use of Intel x86-64 processors, at the
World Wide Developers Conference (WWDC) 2020 [@apple-announcement]. This report will
explore the transition's motives and results through investigating
the history and characteristics of other contemporary processor families,
comparing them to Apple's M1 series. Information was sourced mainly through
secondary literature, such as, academic reports, press releases, news articles.
The report found two common reasons for Apple's processor transitions: supply issues
and dissatisfaction with processor performance and direction. Apple's move to
in-house designs allow control over both these issues. Furthermore, in-house
processors suit Apple's control over hardware and software, achieving further
vertical integration.

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

All processor families have instruction set architectures (ISAs).
ISAs define how software interfaces with the processor's hardware, specifying
available instructions, types, addressing modes, and encodings [@isa-def]. ISAs
belong to two architectures, reduced instruction set computers (RISC),
and complex instruction set computers (CISC). These design philosophies affect
the family's architecture and operation greatly; therefore, the two philosophies
principles and differences must be explored to better analyse the upcoming
processors details and differences.

John Mashey [-@risc-userpages], a contributor to the MIPS RISC
architecture [@john-mashey], defines RISC as having: a load-store
model that separates instructions into distinct memory access and logical operations -
'no operations that combine load/store with arithmetic i.e., like
add from memory, or add to memory', no more than one operand that accesses a
memory address per instruction, standard fixed instruction sizes, simple
encoding, and a small amount of address modes. Crucially, number of
instructions does not decide if a processor is RISC. Instead, RISC is
a loose set of architectures designed at a similar time, sharing some
of the above features, which aim to achieve efficient execution by
a pipelined processor using an optimising compiler [@what-risc].
Whereas, CISC's definition is looser as CISC was retroactively
defined to mean non-RISC [@john-mashey]. However, typical CISC processors
would have variable length instructions, more addressing modes such as indirect
addressing, and instructions requiring processor cycles [@power-struggles].

Though RISC and CISC prioritise different principles. Today, neither
are inherently more efficient, designed for mobile, or faster. These
characteristics are affected far more by a processor's design and its
microarchitecture; not the instruction set architecture. @power-struggles
conducted a study comparing four contemporary processors, two implementing
ARM ISA and two implementing x86, finding that 'Balancing power and
performance leads to energy-efficient cores, regardless of the
ISA: A9 (ARM) and Atom (x86-64) processor energy requirements are
within 24% of each other and use up to 50% less energy than other cores'.
Overall, processors target different performance and efficiency goals and
that is reflected far more in their microarchitecture than ISA.

# PowerPC

![PowerPC Genealogy [@powerpc-hist]](images/powerpc-gena.png){height=30%}

PowerPC traces back to IBM's creation of the 801 processor. It was an experimental
design that used many RISC's principles, though the  philosophy had
not been created yet, making 801 amongst the first RISC processors [@ibm-risc-evo].
IBM 801 was a specialised design for telephone switching, needless instructions
were removed, and instructions were fixed width, using load store architecture
and other RISC principles [@801-computer]. The project was cancelled in 1975
however, the 801's potential as a general-purpose processor was promising.
IBM 801's research was developed further. Originally 801 was scalar, executing
one instruction every cycle, however future processors used three independent
execution units: a branch, fixed-point, and floating-point processor.
Multiple instructions could be executed per cycle, making the processor
superscalar, also known as instruction-level parallelism [@system-6000-arch].
The developments became IBM RISC System/6000, using an ISA dubbed
POWER.

Meanwhile, Apple's CPU supplier, Motorola, struggled to provide competitive
68000 series processors amidst competitive Microsoft-Intel PCs. Therefore,
Apple started experimenting with RISC in the Aquarius project [@powerpc-history].
Noticing this, IBM offered Apple to create a family of POWER processors to use in
desktops. Apple also included Motorola in the agreement due to their long relationship,
and Motorola's experience in processor manufacturing. The result was the Apple,
IBM, and Motorola alliance, AIM.

+-------------+------+------------------+-----------------+---------+--------+----------------------------+
| Processor   | Year | Clock rate (MHz) | L2 Cache (KB)   | SPECint | SPECfp | Reference                  |
+=============+======+==================+=================+=========+========+============================+
| Pentium     | 1993 | 66               | 256 (off-chip)  | 65.1    | 63.6   | @netlib-pentium            |
+-------------+------+------------------+-----------------+---------+--------+----------------------------+
| PPC 601     | 1993 | 66               | None            | 62.6    | 76.1   | @netlib-601                |
+-------------+------+------------------+-----------------+---------+--------+----------------------------+
| PPC 603     | 1994 | 66               | 256 (off-chip)  | 60.6    | 60.7   | @netlib-603                |
+-------------+------+------------------+-----------------+---------+--------+----------------------------+
| PPC 604     | 1994 | 120              | 1024 (off-chip) | 118.1   | 150.4  | @netlib-604                |
+-------------+------+------------------+-----------------+---------+--------+----------------------------+
| Pentium     | 1995 | 120              | 1024 (off-chip) | 172.2   | 108.4  | @netlib-pentium-120        |
+-------------+------+------------------+-----------------+---------+--------+----------------------------+
| Pentium Pro | 1995 | 150              | 256 (off-chip)  | 274.3   | 209.8  | @netlib-pentium-pro        |
+-------------+------+------------------+-----------------+---------+--------+----------------------------+

Table: SPEC 92 Base Benchmark Results for a selection of PowerPC and Intel Processors [@netlib]

+--------------+------+------------------+---------+--------+-------------+
| Processor    | Year | Clock rate (MHz) | SPECint | SPECfp | Reference   |
+============+=+======+==================+=========+========+=============+
| PPC 604      | 1994 | 120              | 4.7     | 4.4    | @mac-info   |
+--------------+------+------------------+---------+--------+-------------+
| PPC 603e     | 1994 | 117              | 3.5     | 2.7    | @mac-info   |
+--------------+------+------------------+---------+--------+-------------+
| Pentium      | 1995 | 120              | 3.7     | 2.8    | @mac-info   |
+--------------+------+------------------+---------+--------+-------------+
| Pentium Pro  | 1995 | 150              | 6.1     | 5.4    | @mac-info   |
+--------------+------+------------------+---------+--------+-------------+
| PPC 604e     | 1996 | 166              | 7.7     | 6.3    | @mac-info   |
+--------------+------+------------------+---------+--------+-------------+
| PPC G3 (750) | 1997 | 233              | 11.0    | 8.1    | @mac-info   |
+--------------+------+------------------+---------+--------+-------------+
| Pentium Pro  | 1997 | 200              | 11.0    | 8.1    | @mac-info   |
+--------------+------+------------------+---------+--------+-------------+
| Pentium II   | 1997 | 233              | 9.5     | 6.4    | @mac-info   |
+--------------+------+------------------+---------+--------+-------------+

Table: SPEC 95 Peak Benchmark Results for a selection of PowerPC and Intel Processors [@mac-info]

Table two shows PowerPC benchmarking well against Intel processors.
The 601 was very competitive with Pentiums, having Petter floating-point
performance whilst suffering in integer workloads. However,
the Pentiums were running at higher wattages than comparable PowerPC processors.
The 66 MHz 601 dissipated seven watts compared to the 66 MHz Pentium's
thirteen watts, resulting in 85% more power consumption [@power-mac-book].
The PowerPC 603, performing worse than the Pentium in both workloads, is a
major anomaly due to its design as a portable processor, drawing only
three watts and sacrificing features [@power-mac-book]. PowerPC's performance
remained competitive throughout the AIM alliance's life. However, PowerPC
saw little usage in the Intel-Microsoft dominated PC market, therefore,
third-party applications and infrastructure were lacking outside of macOS.
Apart from Apple's customers, PowerPC was largely ignored [@powerpc-to-intel].
Moreover, complications arose within the AIM; Motorola exited the
processor industry, creating Freescale Semiconductor, and IBM's focus drifted
from desktops to severs; the alliance was strained. Moreover, the PowerPC G5,
despite benchmarking well, struggled to meet Apple's demands. Steve Jobs
states at WWDC [-@jobs-intel], 'Now, I stood up here two years
ago in front of you and I promised you [a 3 GHz Power Macintosh G5], and we
haven't been able to deliver that to you yet. I think a lot of you would like a
G5 in your PowerBook and we haven't been able to deliver that to you yet'.
AIM was unable to take the G5, a high-performance desktop processor, and
scale it upwards towards GHz, or downwards to meet laptop efficiency targets.
In the growing laptop market, this convinced Apple to transition to
Intel, who offered the efficient mobile Core series.

# Intel

Intel's contemporary processors use a 64-bit extension of x86. x86
originating in 1978 when Intel released the 16-bit 8086, using x86.
Also released, the 8088, a cheaper alternative [@intel-timeline]. The 8088
caught IBM's attention who wanted to enter the growing consumer microcomputer
market. IBM typically designed all their hardware but used the Intel 8088
due to the competitive price and time pressure. The PC Greatly exceeded sales expectations, and
underwent reverse-engineering, leading to clones [@ibm-pc]. The popularity grew Intel
into an industry giant. x86 was continuously improved throughout the
eighties, adding 32-bit support in the 80386, on-processor cache, an integrated
maths coprocessor, and instruction pipelining in the 80486
[@intel-over-the-years; @upgrading-repairing-pcs], and superscalar processing in
the Pentium. In the 14 years between 8088 and Pentium, Intel had increased
x86's speed by 300 times [@intel-timeline]. However, the RISC philosophy's
popularity was increasing, RISC designs outperformed CISC. Despite this,
Intel further invested into x86, creating the Pentium Pro's P6
microarchitecture. To improve pipelining and avoid wasting clock cycles, P6
split CISC instructions into several RISC-like fixed-length micro-operations
These operations are executed out-of-order and in parallel to avoid stalling,
and achieve superscalar execution [@intel-operations]. P6's has been used as
a basis for future x86 microarchitectures, Intel Core; Atom;
Pentium, excluding Pentium 4; and Celeron.  64-bit support was added in Merom
after AMD's success at a 64-bit extension. Simultaneous multithreading (SMT),
branded Hyper-Threading, was added in the Nehalem microarchitecture. SMT when a
superscalar processor's core(s) execute multiple independent processes (threads)
simultaneously. When a thread stalls its resources are used by other threads,
reducing wasted cycles and resources.

After difficulties scaling PowerPC towards higher clocks, and down
for mobile efficiency, Apple would transition to Intel processors in 2006,
completing the transfer by late 2007 [@jobs-intel]. As seen in
Table 4, Intel processors at the time were very competitive in
productivity, an area that Apple targeted heavily. New MacBooks and
iMacs would use the dual-core Core Duo, a substantial improvement to the
single core Pentium-M. Anandtech [-@core-duo] states, 'for a notebook
processor, the Core Duo will be nothing short of amazing for professionals.
Looking at the performance improvements offered everywhere from media
encoding to 3D rendering, you're going to be able to do a lot more on
your notebook than you originally thought possible'. Developments in
mobile processors expanded the capabilities of MacBooks to equal their
desktop equivalents. Similarly to PowerPC, relations with Intel deteriorated.
First, Apple desired to use Intel modems in their smartphones;
however, in 2017 the modems suffered issues, requiring multiple redesigns,
delaying the project, and disappointing Apple's executives [@intel-modem].
Furthermore, Intel encountered supply issues in 2018 and 2019,
forcing prioritisation of high performance Xeon and i9 / i7 processors
instead of lower-end offerings [@intel-cpu-shortages]. Apple suffered from
these shortages, 'For our Mac business overall we faced some processor
constraints in the March quarter, leading to a 5 percent revenue decline
compared to last year. But we believe that our Mac revenue would have been
up compared to last year without these constraints' [@apple-q2-2019].
Therefore, Apple, having developed processors for their iPhone and
Apple Watch lines, decided to transition to in-house Apple Silicon.

# AMD

Similarly to Intel, AMD uses the x86 ISA because their continued
cross-licensing deals. AMD and Intel had been cross-licencing since 1976 after
AMD reverse-engineered the Intel 8080. However, the partnership changed
when IBM offered to use Intel's processors in the IBM PC,but had
Intel to licence manufacturing to a second-source to ensure competition
and supply. The agreement allowed Intel and AMD to licence produce each
other's product, if they exchanged production rights to a product of similar
status. This agreement began in 1982 [@litigation] and AMD created various
clones. Relations strained Intel denied AMD access to the latest 80386 processor
designs. Instead, AMD reverse-engineered the 80386 and Intel began litigation.
However, AMD won the case, allowing them to sell the Am386, a widely popular
CPU which competed with Intel [@31-years-86]. However, AMD could not
use Intel's future designs.

AMD's next processor, the K5 was AMD's first in-house design.
Similar to the Intel P6, AMD's microarchitecture split CISC instructions into
several RISC-like micro-operations, achieving better superscalar and
out-of-order processing. However, the processor had design
issues and delays. Eventually, the K5 was competitive with the Pentium,
but weaker in floating-point applications like games [@anandtech-k5].
K6's, the next microarchitecture, release was better; AMD continued to compete
however, floating-point processing remained weak [@anandtech-k6]. The K7 broke
the floating-point streak. Table 4 shows considerable improvements to
floating-point, competing with Intel at 18% of the cost. Moreover, integer
performance was very competitive at its price. A 650 MHz K7 outperformed a
Pentium III 550 MHz by 27.5%. Then, AMD created K8, the first 64-bit extension
to x86. K8 kept compatibility with 32-bit and 16-bit x86, therefore no
software migration was necessary. Intel's 64-bit processor, Itanium, was not backwards
compatible with x86, and Itanium's software requirements limited availability to
enterprise servers. Therefore, Intel had to adopt AMD's 64-bit extension.
K8 was competitive with Intel, competing very well in gaming and 3D applications,
but suffering in productivity as seen on Table 4 [@athlon-fx-pentium-4]. K10 introduced
dual-core processor to AMD's line-up, but continued K8's performance trends,
AMD's next microarchitecture called Bulldozer, used an entirely new design.
Bulldozer used clustered multithreading (CMT), a technique similar to SMT.
In SMT, each core has its own execution resources and frontend
(fetch and decode process) and two threads are assigned to the core's resources.
In CMT a core consists of two modules, each has one thread. A module
has its own integer, address units and level 1 cache, however they
share the frontend, and floating-point units [@anandtech-fx].
Bulldozer performed badly; it required highly-threaded workloads to perform
well, as seen in table 4's single threaded benchmarks. Finally,
AMD's latest family is Zen. Zen is a completely new design. Zen implements SMT,
and most notably moves to using 'core-complexes' (CCX). A CCX contains four cores.
The processor's die can have several (CCXs) that communicate through an interconnect.
Then, other necessary components such as memory, and USB controllers are added.
This allows the production of low-end four core and high-end 32+ core processors
using the same components. Table 4 shows that Zen is competitive in multithreaded
scenarios, and later versions, compete in both.

+---------------------+---------------+------+------------------+----------+-----------------+--------+-------------------+
| Processor           | µarch         | Year | Clock rate (MHz) | Cost ($) | Benchmark       | Result | Reference         |
+=====================+===============+======+==================+==========+=================+========+===================+
| AMD Athlon          | K7            | 1999 | 650              | 699      | SPECint95       | 27.5   | @mac-info         |
|                     |               |      |                  |          +-----------------+--------+-------------------+
|                     |               |      |                  |          | SPECfp95        | 23.2   | @mac-info         |
+---------------------+---------------+------+------------------+----------+-----------------+--------+-------------------+
| Pentium III         | P6            | 1999 | 550              | 700      | SPECint95       | 22.3   | @mac-info         |
|                     |               |      |                  |          +-----------------+--------+-------------------+
|                     |               |      |                  |          | SPECflt95       | 15.1   | @mac-info         |
+---------------------+---------------+------+------------------+----------+-----------------+--------+-------------------+
| AMD Athlon          | K7            | 1999 | 800              | ~800     | SPECflt95       | 35.0   | @mac-info         |
|                     |               |      |                  |          +-----------------+--------+-------------------+
|                     |               |      |                  |          | SPECint95       | 25.4   | @mac-info         |
+---------------------+---------------+------+------------------+----------+-----------------+--------+-------------------+
| Pentium III/E       | P6            | 1999 | 800              | 851      | SPECint95       | 38.3   | @mac-info         |
|                     |               |      |                  |          +-----------------+--------+-------------------+
|                     |               |      |                  |          | SPECflt95       | 24.5   | @mac-info         |
+---------------------+---------------+------+------------------+----------+-----------------+--------+-------------------+
| AMD Athlon 64 FX-51 | K8            | 2003 | 2200             | 733      | PC Mark 2004    | 4177   | @2004-cpu-charts  |
+---------------------+---------------+------+------------------+----------+-----------------+--------+-------------------+
| Pentium 4 Extreme   | NetBurst      | 2003 | 3200             | 925      | PC Mark 2004    | 4854   | @2004-cpu-charts  |
|                     |               |      |                  |          +-----------------+--------+-------------------+
|                     |               |      |                  |          | PC Mark 2005    | 4169   | @2005-cpu-charts  |
+---------------------+---------------+------+------------------+----------+-----------------+--------+-------------------+
| Athlon 64 X2 3800+  | K8            | 2005 | 2000             | 354      | PC Mark 2005    | 3769   | @2005-cpu-charts  |
+---------------------+---------------+------+------------------+----------+-----------------+--------+-------------------+
| Pentium D 830       | NetBurst      | 2005 | 3000             | 316      | PC Mark 2005    | 4955   | @2005-cpu-charts  |
+---------------------+---------------+------+------------------+----------+-----------------+--------+-------------------+
| Core 2 Duo E6850    | Core          | 2007 | 3000             | 266      | PC Mark 2005    | 7648   | @2008-cpu-charts  |
+---------------------+---------------+------+------------------+----------+-----------------+--------+-------------------+
| Phenom X4 9500      | K10           | 2007 | 2200             | 283      | PC Mark 2005    | 6509   | @2008-cpu-charts  |
+---------------------+---------------+------+------------------+----------+-----------------+--------+-------------------+
| Core i5-2500K       | Sandy Bridge  | 2011 | 3300             | 216      | Cinebench R10   | 20381  | @anandtech-fx     |
|                     |               |      |                  |          +-----------------+--------+-------------------+
|                     |               |      |                  |          | Cinebench R10   | 5860   | @anandtech-fx     |
|                     |               |      |                  |          | Single Threaded |        |                   |
+---------------------+---------------+------+------------------+----------+-----------------+--------+-------------------+
| FX-8150             | Bulldozer     | 2011 | 3600             | 245      | Cinebench R10   | 20254  | @anandtech-fx     |
|                     |               |      |                  |          +-----------------+--------+-------------------+
|                     |               |      |                  |          | Cinebench R10   | 3938   | @anandtech-fx     |
|                     |               |      |                  |          | Single Threaded |        |                   |
+---------------------+---------------+------+------------------+----------+-----------------+--------+-------------------+
| Core i5-7600        | Kaby Lake     | 2017 | 3000             | 213      | Geekbench 6     | 1380   | @geekbench        |
|                     |               |      |                  |          +-----------------+--------+-------------------+
|                     |               |      |                  |          | Geekbench 6     | 3825   | @geekbench        |
|                     |               |      |                  |          | Single Threaded |        |                   |
+---------------------+---------------+------+------------------+----------+-----------------+--------+-------------------+
| Ryzen 5 1600        | Zen 1         | 2017 | 2200             | 219      | Geekbench 6     | 1070   | @geekbench        |
|                     |               |      |                  |          +-----------------+--------+-------------------+
|                     |               |      |                  |          | Geekbench 6     | 4582   | @geekbench        |
|                     |               |      |                  |          | Single Threaded |        |                   |
+---------------------+---------------+------+------------------+----------+-----------------+--------+-------------------+
| Core i5-10600       | Comet Lake    | 2020 | 3300             | 213      | Geekbench 6     | 6205   | @geekbench        |
|                     |               |      |                  |          +-----------------+--------+-------------------+
|                     |               |      |                  |          | Geekbench 6     | 1587   | @geekbench        |
|                     |               |      |                  |          | Single Threaded |        |                   |
+---------------------+---------------+------+------------------+----------+-----------------+--------+-------------------+
| Ryzen 5 5600        | Zen 3         | 2020 | 3500             | 199      | Geekbench 6     | 8554   | @geekbench        |
|                     |               |      |                  |          +-----------------+--------+-------------------+
|                     |               |      |                  |          | Geekbench 6     | 2023   | @geekbench        |
|                     |               |      |                  |          | Single Threaded |        |                   |
+---------------------+---------------+------+------------------+----------+-----------------+--------+-------------------+

Table: Comparison of Intel & AMD's In-House Processors with Similar Release Costs and Years

# Apple Silicon

Apple silicon started with the 2008 acquisition of fabless semiconductor
company P.A. Semi, a creator of efficient Power ISA processors. Apple then
acquired an architectural licence from ARM, granting Apple usage of ARM ISA
without using ARM's processor core designs. Apple used the acquisition and
licence for to create system on chips (SOCs) for iPhones, iPads, and other
low-power products. Intel's supply struggles provided Apple with the opportunity
for almost-complete vertical integration of their laptop and desktop processors.
Thus, in WWDC [@apple-announcement] the 'M' processor series was announced.
As stated in the RISC & CISC explanation, in contemporary processors ISA
has little effect on efficiency. Instead, microarchitecture design, technology
node (the size of the transistors), and software integration effect performance
and efficiency far more, and Apple controls all these aspects. Furthermore,
unlike AMD and Intel, Apple's processors are only used in their product;
therefore, the processors can use expensive designs and technology, absorbing
extra costs into the end-product. AMD and Intel are unable to do this as
they must profit from processor sales.

![Firestorm Architecture [@firestorm-arch]](images/firestorm.png)

+---------------------+---------------+--------------------+-----------------+--------+-------------------------+
| Processor           | µarch         | Power Draw (Watts) | Benchmark       | Result | Reference               |
+=====================+===============+====================+=================+========+=========================+
| Ryzen 7 5800U       |               | 25                 | Cinebench 23    | 9248   | @amd-5800u \            |
|                     | Zen 3         +--------------------+-----------------+--------+-------------------------+
|                     |               | \-                 | Cinebench 23    | \-     |                         |
|                     |               |                    | Single Threaded |        |                         |
+---------------------+---------------+--------------------+-----------------+--------+-------------------------+
| Ryzen 9 5900HS      | Zen 3         | 35                 | Cinebench 23    | 11024  | @amd-5800u \            |
|                     |               +--------------------+-----------------+--------+-------------------------+
|                     |               | \-                 | Cinebench 23    | \-     |                         |
|                     |               |                    | Single Threaded |        |                         |
+---------------------+---------------+--------------------+-----------------+--------+-------------------------+
| Core i9-11980HK     | Tiger Lake    | 82.6               | Cinebench 23    | 12830  | @anandtech-power-draw \ |
|                     |               +--------------------+-----------------+--------+-------------------------+
|                     |               | 30.0               | Cinebench 23    | 1604   |                         |
|                     |               |                    | Single Threaded |        |                         |
+---------------------+---------------+--------------------+-----------------+--------+-------------------------+
| Apple M1            | Firestorm     | ~26.8 (wall power) | Cinebench 23    | 7833   | @anandtech-m1 \         |
|                     | / Icestorm    +--------------------+-----------------+--------+-------------------------+
|                     |               | \-                 | Cinebench 23    | 1522   |                         |
|                     |               |                    | Single Threaded |        |                         |
+---------------------+---------------+--------------------+-----------------+--------+-------------------------+
| Apple M1 Max        | Firestorm     | 34.0               | Cinebench 23    | 12375  | @anandtech-power-draw \ |
|                     | / Icestorm    +--------------------+-----------------+--------+-------------------------+
|                     |               | 11.0               | Cinebench 23    | 1529   |                         |
|                     |               |                    | Single Threaded |        |                         |
+---------------------+---------------+--------------------+-----------------+--------+-------------------------+

Table: Comparison of Apple M1 Family to 2020 AMD and Intel Processors

M1, Apple's first "M" family, has three models: M1, M1 Pro, and M1 Max.
All use Apple's "Firestorm" microarchitectures for performance cores,
"Icestorm" for efficiency cores, and TSMC's 5 nm technology node. The node was
cutting-edge technology in 2020, entering non-risky production the same
year as M1. In smaller technology nodes electrons travel shorter
distances, increasing efficiency, and performance at the expense of cost
[@semiconductor-process]. Furthermore, the microarchitecture targets efficiency.
Each Firestorm core uses an eight-wide decoder, allowing decoding of eight
instructions per clock. This was the widest decoder available in commercial
industry; AMD and Intel's decoders were only four-wide. Continuing
the wide design, Firestorm's out-of-order reorder buffer (ROB) stores 630
instructions. Intel and AMD's ROBs store 352 and 256 instructions respectively.
Large ROBs allow storing out-of-order instructions before reordering them
back to sequential order, avoiding stalling [@anandtech-firestorm]. To
execute the wide microarchitecture, six logic, four floating-point
and, four load-store units are used. Compared to Zen 3, Firestorm has two
more logic units and an extra load-store [@anandtech-zen-3; @firestorm-arch],
raising instructions per clock. To support this, Firestorm's cache is large,
192 KB of instruction cache compared to Zen 3's 32 KB. Level 2 cache
typically exceeds Zen 3 at lower core counts. However, level 3 cache is around
equal [@anandtech-firestorm; @anandtech-zen-3]. Overall, Firestorm has high
instructions-per-clock, which run at lower clock rates, resulting in high
efficiency. However, the design's high width and large cache decreases yields
from fabrication plants, increasing costs. The costs are mitigated
through Apple's vertical integration.

The design's effects are shown in table 5, the M1 Max outperforms AMD's Ryzen 9
by 16% at one watt lower power draw. Though the M1 Max benchmarked ~3.5% lower
than the i9, it consumed 41% of the i9's power. M1's power consumption
was higher than typical as the test used a Mac Mini and independent processor
consumption was not measured. However, it still competes well against the Ryzen 7,
a processor created later after the M1.

# Conclusion

Apple has transitioned to new processors three times, each time due to supply
issues and dissatisfaction with performance. However, Apple silicon provides
Apple with complete control over their hardware, processor supply, and its direction.
Apple now controls all aspects of their devices in a vertical business model
that allows for creation of competitive processors combined with specialised
specialised towards them. This solves Apple's issues with third-party processors.
The statements above show a clear rationale for Apple's move to in-house desktop
and laptop processors.

# Bibliography

