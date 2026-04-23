# MLS, MSL, and MILS

Three acronyms that look almost identical but represent fundamentally different approaches to handling multiple classification levels. Understanding the differences is essential -- it shapes every architecture decision in CDS and explains why the field is evolving the way it is.

---

## The short version

**MLS (Multi-Level Security)**
:   One system processes information at multiple classification levels simultaneously. A single machine handles TOP SECRET and UNCLASSIFIED data, relying on mandatory access controls to keep them apart. The original dream. Mostly failed.

**MSL (Multiple Single Level)**
:   Each classification level gets its own physically or logically separated system. A SECRET network and an UNCLASSIFIED network run on entirely separate hardware. CDS guards and diodes sit at the boundaries to mediate transfers. This is what the real world actually uses.

**MILS (Multiple Independent Levels of Security)**
:   A small, verified separation kernel creates isolated partitions on shared hardware, with controlled information flow between them. Each partition runs at a single level (like MSL), but on shared hardware (like MLS), with the kernel providing mathematically verifiable separation. The practical middle ground -- and the most promising foundation for software CDS.

!!! info "Why this matters for software CDS"

    If you are thinking about building a software-based CDS -- particularly in cloud environments -- you need to understand these three approaches. MSL is what you are doing today (separate AWS accounts per classification level). MILS is likely where you are going (verified separation on shared infrastructure). True MLS remains largely out of reach.

---

## Why MSL dominates today

Almost every CDS deployment in the world uses the MSL pattern. The US government's own classified networks -- NIPRNet (UNCLASSIFIED), SIPRNet (SECRET), JWICS (TOP SECRET/SCI) -- are physically separate networks connected only through accredited cross-domain solutions. This is MSL at national scale.

Five reinforcing factors explain why:

### 1. No trusted operating system ever became mainstream

The MLS vision required operating systems that could be mathematically proven to enforce mandatory access controls across multiple classification levels on a single machine. Very few systems achieved the necessary certification, and those that did were expensive, limited in functionality, and incompatible with commodity software. Since the vast majority of government IT runs on Windows and Linux, MSL became the only practical option.

### 2. Certification is dramatically simpler

Certifying a system-high environment means proving that one system protects information at one level. Certifying an MLS system means proving that one system simultaneously protects information at multiple levels, prevents all downward flows (including covert channels), and correctly labels every object. The accreditation burden for MLS is orders of magnitude higher.

### 3. Physical separation is easier to audit

When a SECRET network and an UNCLASSIFIED network run on physically separate hardware, an auditor can verify isolation by inspecting cables, switches, and room layouts. When an MLS system claims to separate SECRET and UNCLASSIFIED data on the same hardware, the auditor must trust the software -- and the history of software vulnerabilities makes that a hard sell.

### 4. The covert channel problem is unsolved

For an MLS system to work perfectly, there must be no way for a TOP SECRET process to signal a SECRET or lower process -- not through shared memory, disk timing, CPU load, power consumption, or any other side effect. As the academic literature acknowledges, "it is extremely difficult to close all covert channels in a practical computing system, and it may be impossible in practice." MSL sidesteps this entirely through physical separation.

### 5. The real-world architecture embraced it

The US Unified Cross Domain Management Office (UCDMO) created categories that are "essentially analogous to MILS" -- which itself pursues an MSL strategy. When the national security establishment builds its own infrastructure on MSL principles, the market follows.

### The MSL tax

MSL works, but it is expensive. Every classification level requires its own hardware, network infrastructure, administration, and often its own physical space. Users with clearances at multiple levels need multiple terminals or KVM switches. Data sharing requires CDS infrastructure at every boundary. This cost drives the interest in MILS and virtualisation as ways to reduce the MSL burden without accepting the MLS risk.

---

## The MLS dream and why it mostly failed

### The original vision

The MLS concept emerged from a specific military problem in the 1960s-70s: the US Department of Defense needed computer systems that could process information at multiple classification levels simultaneously, serving users with different clearances on the same machine.

In 1973, David Bell and Len LaPadula formalised this as the [Bell-LaPadula model](how-it-works/data-flow.md), establishing two rules:

- **No read up** -- a subject cannot read data above its clearance
- **No write down** -- a subject cannot write data below its clearance

The vision was clear: build operating systems with formally verified security kernels, mathematically proven to enforce these rules, allowing a single system to safely handle TOP SECRET and UNCLASSIFIED data at the same time.

### The Orange Book

The Trusted Computer System Evaluation Criteria (TCSEC, 1983) -- known as the Orange Book -- codified security requirements in a hierarchy:

| Level | Name | Key requirement |
|-------|------|----------------|
| D | Minimal Protection | Evaluated but failed higher requirements |
| C1 | Discretionary Security | Basic identification and authentication |
| C2 | Controlled Access | Audit trails, individual accountability |
| B1 | Labelled Security | Mandatory access control, sensitivity labels |
| B2 | Structured Protection | Formal security model, covert channel analysis |
| B3 | Security Domains | Reference monitor, minimised TCB |
| A1 | Verified Design | Formal top-level specification, formal verification |

True MLS required at least B1. High-assurance MLS required B3 or A1.

!!! warning "Only three systems ever achieved A1"

    1. **Honeywell SCOMP** (Secure Communications Processor)
    2. **Aesec GEMSOS** (Gemini Trusted Network Processor)
    3. **Boeing SNS Server**

    All were 1980s-vintage, Intel 80386-based systems. None achieved commercial success. The gap between theoretical possibility and practical deployment proved insurmountable.

### Why it failed

David Bell himself, reflecting in 2005 at the Annual Computer Security Applications Conference, identified several reasons:

**The gap between model and implementation.** The Bell-LaPadula model was mathematically elegant, but translating formal models into working systems proved far more difficult than anticipated. Real systems have properties not captured in abstract models.

**Covert channels.** Unintended information flows through timing, storage allocation, power consumption, and other side effects were discovered after the model was developed and fundamentally undermined the guarantee of complete information flow control.

**Cost-benefit mismatch.** The enormous development and evaluation costs did not justify the returns for most applications.

**Usability.** Bell noted that "we overestimated the willingness of users to accept" the rigid constraints of highly secure systems.

**The sanitisation problem.** There is no reliable mechanism for a TOP SECRET user to edit a document, remove all TOP SECRET content, and deliver it at SECRET. Systems work around this with privileged bypass functions, but these fundamentally undermine the MLS guarantee.

**The Orange Book was too rigid.** The evaluation process was extremely costly and imposed tight configuration control restrictions that stifled innovation.

### What implements MLS today

Despite the broader failure, some products implement MLS features in limited contexts:

| Product | Type | MLS mechanism |
|---------|------|--------------|
| **SELinux** (in MLS policy mode) | OS extension | MAC with MLS policy, evaluated at EAL4+ (LSPP) |
| **Oracle Solaris Trusted Extensions** | OS feature | Labelled security, EAL4 (LSPP) |
| **General Dynamics PitBull** | Enhanced RHEL | Bell-LaPadula + Biba integrity enforcement |
| **BAE Systems XTS-400** | Standalone OS | Predecessor XTS-300 was B3-rated, EAL5+ |
| **Oracle Label Security** | Database feature | Row-level mandatory access control |

The most notable current deployment is Oracle Label Security at US Army INSCOM, forming the foundation of an all-source intelligence database spanning JWICS and SIPRNet. This is one of the few examples of operational MLS in a production intelligence environment.

---

## MILS: the practical middle ground

### The core idea

Rather than building a single operating system that understands all security levels (MLS), use a small, verified separation kernel to create isolated partitions on shared hardware, each running at a single security level. Information flow between partitions is controlled by small, individually certifiable components.

MILS is, in essence, a highly assured form of MSL -- but implemented on shared hardware rather than physically separate systems, using a verified separation kernel rather than air gaps.

!!! info "MILS gives you partitions, not policy"

    MILS provides domain separation and controlled information flow. But the policy logic -- the actual cross-domain guard functionality (content inspection, treatments, security label checking) -- must still be built on top. MILS is an enabler for CDS, not a CDS in itself.

### The Rushby foundation

The intellectual foundation of MILS is John Rushby's 1981 paper "Design and Verification of Secure Systems." Rushby proposed the separation kernel: a small kernel whose sole purpose is to create an environment indistinguishable from a physically distributed system, where each partition behaves as a separate, isolated machine.

This was a radical simplification. Instead of building a kernel that understands complex security policies (Bell-LaPadula, Biba, roles), build a kernel that does only one thing: separate partitions. Move all policy enforcement into applications running within or between those partitions. The smaller the kernel, the easier it is to verify.

### The four NEAT properties

MILS requires that the separation mechanism satisfies four properties:

| Property | What it means |
|----------|--------------|
| **Non-bypassable** | No component can use another path to get around the security monitor |
| **Evaluatable** | Any trusted component can be evaluated to the assurance level required |
| **Always-invoked** | Every access or message is checked by the security monitor |
| **Tamperproof** | The security monitor's code, configuration, and data cannot be modified by untrusted components |

These are conceptually identical to the reference monitor properties from the Orange Book, but applied specifically to the separation kernel rather than the entire operating system. This scoping is what makes MILS tractable where MLS was not.

### Why compositional certification is transformative

The key innovation of MILS is compositional certification. A single MILS system can contain:

- A separation kernel certified at EAL 6-7 (high robustness)
- Content filters certified at EAL 4-5
- Unmodified Linux in a partition alongside TYPE I crypto
- The overall security argument composed from individual component certifications

This is transformative because:

1. **The verification burden is distributed.** Instead of verifying a monolithic MLS OS, you verify a small kernel at the highest level and larger components at lower levels.
2. **Components can be updated independently.** Replacing an EAL4 application does not require re-certifying the EAL6+ kernel.
3. **COTS software can participate.** Unmodified Linux or Windows can run in a partition, with the separation kernel providing the assurance that it cannot escape.
4. **High assurance becomes affordable.** Formally verifying a 10,000-line separation kernel is tractable. Formally verifying a million-line operating system is not.

### European research programmes

Three EU-funded programmes have advanced MILS technology, representing approximately EUR 17.7M of investment:

| Programme | Years | Budget | Focus |
|-----------|-------|--------|-------|
| **EURO-MILS** | 2012--2016 | EUR 8.4M | First European CC evaluation of a MILS system at highest assurance. Partners: Airbus, SYSGO, Thales. Advisory board: BSI and ANSSI. |
| **D-MILS** | 2012--2015 | EUR 3.8M | Extended MILS from single-node to distributed systems using time-triggered Ethernet. Partners: TTTech, Frequentis, INRIA. |
| **certMILS** | 2017--2021 | EUR 5.5M | Developed compositional security certification methodology. Three industrial pilots: smart grid, railway, subway. Partners: SYSGO, Schneider Electric, Hitachi Rail. |

SYSGO (PikeOS) was a partner in all three programmes, making it the central industrial player in European MILS.

---

## Separation kernels: the state of the art

The separation kernel is the foundation of MILS. Five products represent the current state of the art:

### INTEGRITY-178B (Green Hills Software, USA)

The certification leader. INTEGRITY-178B holds the highest Common Criteria rating ever achieved for a commercial operating system: **EAL 6+ / High Robustness**. It is the only separation kernel with **NSA Raise the Bar certification specifically for CDS**.

- Architecture: hardware memory protection creating isolated partitions
- Supports AMP, SMP, and BMP multiprocessing
- Multivisor for running Linux, Windows, and Android as isolated guests
- Platforms: ARM, x86, Power, RISC-V, MIPS
- Deployed in aerospace, defence, and CDS applications
- US-controlled (ITAR restrictions apply)

### PikeOS (SYSGO, Germany)

The European sovereign option. **BSI EAL 5+** certified separation kernel, explicitly marketed as ITAR-free.

- Type 1 hypervisor with strict time and space partitioning
- First RTOS to achieve SIL 4 certification on multi-core platforms
- Supports Rust as a development language within partitions
- Pre-certified: DO-178C DAL A, EN 50716 SIL 4, ISO 26262 ASIL D
- Platforms: ARM, x86, SPARC, Power, RISC-V
- Partner in all three EU MILS research programmes

!!! info "The sovereignty argument"

    PikeOS is owned by a German company with no US export restrictions. For European CDS implementations that cannot depend on US-controlled technology, PikeOS provides a sovereign foundation with BSI certification.

### seL4 (seL4 Foundation, Australia)

The formal verification benchmark. seL4 has the most comprehensive mathematical proofs of any production OS kernel.

**What the proofs cover:**

- **Functional correctness** -- the implementation behaves precisely as its specification says
- **Information flow security** -- proven absence of information leakage between partitions
- **Binary verification** -- proofs extend to the compiled binary, not just source code

**What the proofs do not cover:** assembly code, boot code, hardware correctness, DMA. These require manual validation.

The proofs go beyond what Common Criteria, ISO 26262, and DO-178C require at their most stringent levels. seL4 has been used in DARPA's HACMS programme to build demonstrably unhackable systems. However, it has not yet been certified against a CDS-specific protection profile.

- Platforms: ARM (primary), x86
- Licence: GPL v2

### LynxSecure (Lynx Software Technologies, USA)

Notable for its **information flow modelling language** -- an explicit, traceable way to define how data flows between partitions. The kernel remains "out of the data plane," meaning it cannot be a vector for data leakage.

- Customers: US Navy, US Army, NASA, Lockheed Martin, Northrop Grumman, BAE Systems
- Designed for DO-178C DAL A, NIST, NSA Common Criteria compliance
- Platforms: x86, ARM

### Muen (codelabs, Switzerland)

The open-source option. Written in SPARK 2014 (formally verifiable Ada subset), with proven absence of runtime errors at source code level.

- Uniquely **addresses covert channels explicitly** -- a problem most platforms ignore
- Developed in cooperation with secunet Security Networks (a BSI-evaluated vendor)
- Uses Intel VT-x, VT-d, EPT for separation; fixed cyclic scheduling
- Licence: GPL v3
- Platforms: x86 (primary), ARM64 (preliminary)

### Comparison

| Kernel | Verification | Highest certification | CDS-specific | Origin | Licence |
|--------|-------------|----------------------|-------------|--------|---------|
| INTEGRITY-178B | Traditional V&V | EAL 6+ / NSA RTB | Yes | USA | Proprietary |
| PikeOS | Traditional V&V + MILS | BSI EAL 5+ | No (but BSI separation) | Germany | Proprietary |
| seL4 | Functional correctness + info flow to binary | N/A (exceeds CC) | Not yet | Australia | GPL v2 |
| LynxSecure | Info flow modelling | DO-178C DAL A | No (defence-deployed) | USA | Proprietary |
| Muen | Absence of runtime errors (SPARK) | None (formal proofs) | No | Switzerland | GPL v3 |

---

## Can MILS translate to cloud?

This is the critical question for software CDS. Cloud infrastructure increasingly resembles MILS architecture -- but with significant gaps.

### AWS Nitro: closer than you think

The AWS Nitro System is the most architecturally interesting convergence between cloud infrastructure and separation kernel principles:

- **The Nitro Hypervisor** is a minimised, modified Linux kernel with no networking support and no unnecessary capabilities. Its sole functions are CPU and memory partitioning. Once it sets up partitions, it is "just about always quiescent." This is remarkably close to the separation kernel concept.
- **The Nitro Security Chip** implements hardware root of trust with measured boot. The general-purpose processor cannot change any firmware.
- **Firmware is immutable** -- EC2 servers cannot update their own firmware. Only AWS can update it through the Nitro System.
- **All I/O runs on dedicated Nitro Cards**, off the general-purpose processor. Security group enforcement, encryption, and storage all happen in hardware.

??? question "How does Nitro map to MILS properties?"

    | MILS property | Nitro equivalent | Gap |
    |---------------|-----------------|-----|
    | Non-bypassable | Hardware-enforced partition boundaries | No formal proof of non-bypassability |
    | Evaluatable | Minimal hypervisor is architecturally simple | No CC evaluation, no formal verification |
    | Always-invoked | All I/O mediated by Nitro Cards | No proof of complete mediation |
    | Tamperproof | Immutable firmware, measured boot | No formal tamperproofing verification |

### Confidential computing: hardware memory encryption

AMD SEV-SNP, Intel TDX, and ARM CCA provide hardware-enforced memory encryption that brings cloud closer to MILS:

| Technology | Mechanism | What it gives you |
|-----------|-----------|------------------|
| **AMD SEV-SNP** | Encrypted VMs, protected from hypervisor | Infrastructure operator removed from trust boundary |
| **Intel TDX** | Encrypted "trust domains" | Attestation-gated data release |
| **ARM CCA** | Secure "realms" | Hardware memory isolation between workloads |

**What confidential computing does not provide:** no covert channel mitigation (side-channel attacks have been demonstrated against all platforms), no formal verification, no information flow control, no security labelling, no availability guarantees. The assurance gap between these technologies and a certified separation kernel remains significant.

### Cloud multi-account as MSL

The standard cloud security pattern for classified workloads is essentially MSL replicated in cloud infrastructure:

- Separate AWS accounts per classification level
- Separate VPCs with no cross-level peering
- Separate AWS regions for higher classifications (GovCloud for IL5, Secret Region, Top Secret Region)
- Physical separation at the highest levels

This is architecturally identical to traditional MSL -- separate systems at separate levels, connected only through CDS at the boundaries. Cloud multi-account strategies do not introduce MLS or MILS; they replicate MSL.

### The gap

The honest assessment is that cloud infrastructure today provides **MSL with stronger isolation properties than traditional MSL** (Nitro's minimal hypervisor, hardware memory encryption, immutable firmware), but falls well short of MILS assurance:

- No formal verification of the hypervisor or security mechanisms
- No CC evaluation against a CDS protection profile
- No information flow proofs
- No covert channel analysis or mitigation
- Platform under the cloud provider's physical control, not the operator's

---

## The opportunity for software CDS

Several developments are closing the gap between hardware and software CDS assurance:

### What is changing

**Formal verification has become practical.** seL4 demonstrated that a production microkernel can be formally verified to binary level, providing assurance that exceeds the most stringent certification schemes. A software CDS built on a formally verified separation kernel inherits this for the platform layer.

**Memory-safe languages eliminate vulnerability classes.** Rust, SPARK/Ada, and similar languages prevent buffer overflows, use-after-free, and other memory corruption at compile time. PikeOS supports Rust within partitions. Muen is written entirely in SPARK 2014. A guard written in Rust on a verified kernel has a dramatically smaller attack surface than one written in C on a commodity OS.

**Compositional certification has a methodology.** The certMILS programme developed and validated compositional security certification across three industrial pilots. The path from individually certified components to a system-level assurance argument is now documented.

**BSI has broken the hardware-only assumption.** genua's genugate Virtual became the first virtualised firewall to achieve CC EAL 4+ and BSI approval for classified information (VS-NfD, equivalent to NATO RESTRICTED). This demonstrates that a national security authority is willing to certify virtualised security products for classified processing.

### The trajectory

| Classification | Software CDS status | Timeframe |
|---------------|-------------------|-----------|
| **RESTRICTED / CUI** | Already happening (genua BSI-approved virtualised products) | Now |
| **CONFIDENTIAL** | Feasible with MILS separation kernels (PikeOS EAL 5+, INTEGRITY EAL 6+) and certified guard components | Near-term |
| **SECRET** | Requires formally verified kernel + formally verified guard logic, compositionally certified | Medium-term (5--10 years) |
| **TOP SECRET** | Hardware CDS remains required -- data diodes for critical one-way flows | Foreseeable future |

!!! warning "The covert channel barrier"

    The hardest remaining problem for software CDS is covert channels. Even with formal verification, side channels through shared hardware (caches, TLBs, branch predictors, power consumption) remain difficult to eliminate entirely. Hardware CDS with physical one-way paths (data diodes) have an inherent advantage here that software cannot match. This is why TOP SECRET is likely to remain hardware-only for the foreseeable future.

### What to watch

**genua** (Germany) -- BSI-certified virtualised CDS, quantum-resistant cryptography, MILS architecture. The most progressive approach to resolving the assurance-vs-agility tension in CDS.

**seL4** -- If a CDS guard is built on seL4 with formally verified application logic, the resulting assurance argument would be stronger than many existing hardware CDS products. No one has done this yet, but the pieces exist.

**certMILS methodology** -- Compositional certification is the enabler that allows high-assurance kernels and lower-assurance components to be combined into a certified system. Watch for adoption by national evaluation authorities beyond BSI.

**AWS Nitro evolution** -- If AWS pursues formal verification or CC evaluation of Nitro components (unlikely but not impossible), the cloud MILS gap would narrow significantly.

---

## How MLS, MSL, and MILS relate to CDS types

| | MLS | MSL | MILS |
|---|---|---|---|
| **Security mechanism** | Single OS enforces MAC across all levels | Physical/logical separation per level | Separation kernel isolates partitions |
| **Where CDS fits** | CDS is built into the MLS system itself | CDS sits at the boundary between separate systems | CDS components run in dedicated MILS partitions |
| **Maps to CDS type** | [Multi-Level solutions](three-types.md) | [Transfer solutions](three-types.md) (guards, diodes) | Either -- MILS can host guards or provide multi-level processing |
| **Assurance argument** | Trust the entire OS TCB | Trust the physical separation + boundary CDS | Trust the separation kernel + individual components |
| **Real-world prevalence** | Rare (Oracle Label Security at INSCOM, SELinux MLS mode) | Dominant (NIPRNet/SIPRNet/JWICS, all NATO nations) | Growing (INTEGRITY-based guards, PikeOS-based systems) |
| **Cloud equivalent** | None today | Multi-account strategies | Potentially: Nitro + confidential computing + verified guards |

---

## Further reading

- [The Three Types of CDS](three-types.md) -- how Access, Transfer, and Multi-Level solutions map to these architectures
- [Hardware Guards](hardware/guards.md) -- MILS architecture in hardware guard implementations
- [Software CDS Considerations](software/considerations.md) -- separation kernels, virtualisation, and the trust challenge
- [Software CDS Architectures](software/architectures.md) -- cloud-native patterns including AWS and Azure
- [Hardware vs Software Trade-offs](software/hardware-vs-software.md) -- decision framework
- [Standards and Certification](standards/certification.md) -- Common Criteria, EAL levels, and national evaluation processes
