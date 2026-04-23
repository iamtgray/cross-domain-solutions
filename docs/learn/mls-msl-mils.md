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

    If you're thinking about building a software-based CDS -- particularly in cloud environments -- you need to understand these three approaches. MSL is what you're doing today (separate AWS accounts per classification level). MILS is likely where you're going (verified separation on shared infrastructure). True MLS remains largely out of reach.

---

## A note on EAL

You'll see EAL levels referenced throughout this page and the rest of the site. EAL stands for **Evaluation Assurance Level** -- it's the Common Criteria scale (EAL 1 through EAL 7) that measures how rigorously a product's security claims have been independently tested and verified. Higher numbers mean more rigorous evaluation: EAL 1 is basic functional testing, EAL 4 is roughly where serious commercial security products sit, and EAL 6-7 involves formal mathematical verification of the design. For the full picture, see [Certification and Evaluation](standards/certification.md).

---

## Why MSL dominates today

Almost every CDS deployment in the world uses the MSL pattern. The US government's own classified networks -- NIPRNet (UNCLASSIFIED), SIPRNet (SECRET), JWICS (TOP SECRET/SCI) -- are physically separate networks connected only through accredited cross-domain solutions. NATO nations follow the same approach. This is MSL at national scale.

The reasons are straightforward:

**No trusted operating system ever became mainstream.** MLS required operating systems with mathematically proven mandatory access controls. The few that achieved the necessary certification were expensive, limited, and incompatible with commodity software. Since government IT overwhelmingly runs on Windows and Linux, MSL became the only practical option.

**Certification is dramatically simpler.** Certifying a system at one level means proving it protects information at that level. Certifying an MLS system means proving it simultaneously protects multiple levels, prevents all downward flows (including covert channels), and correctly labels every object. The accreditation burden is orders of magnitude higher.

**Physical separation is easier to audit.** When SECRET and UNCLASSIFIED networks run on separate hardware, an auditor can verify isolation by inspecting cables and room layouts. When MLS claims to separate them on the same hardware, the auditor must trust the software -- and the history of software vulnerabilities makes that a hard sell.

**The covert channel problem is unsolved.** For MLS to work perfectly, there must be no way for a TOP SECRET process to signal a lower process -- not through shared memory, disk timing, CPU load, power consumption, or any other side effect. As the academic literature acknowledges, "it is extremely difficult to close all covert channels in a practical computing system, and it may be impossible in practice." MSL sidesteps this entirely through physical separation.

MSL works, but it's expensive. Every classification level requires its own hardware, network infrastructure, administration, and often its own physical space. Users with clearances at multiple levels need multiple terminals or KVM switches. Data sharing requires CDS infrastructure at every boundary. This cost is what drives the interest in MILS and virtualisation -- reducing the MSL burden without accepting the MLS risk.

---

## The MLS dream and why it mostly failed

The MLS concept emerged from a specific military problem in the 1960s-70s: the US Department of Defense needed computer systems that could process information at multiple classification levels simultaneously, serving users with different clearances on the same machine. In 1973, David Bell and Len LaPadula formalised this as the [Bell-LaPadula model](three-types.md), establishing the "no read up, no write down" rules that remain the theoretical foundation of CDS design.

The vision was clear: build operating systems with formally verified security kernels, mathematically proven to enforce these rules, allowing a single system to safely handle TOP SECRET and UNCLASSIFIED data at the same time. The US DoD codified the requirements in the Trusted Computer System Evaluation Criteria (TCSEC, 1983) -- known as the Orange Book -- which defined a hierarchy from D (minimal protection) up to A1 (verified design with formal mathematical proofs).

??? note "The Orange Book levels"

    | Level | Name | Key requirement |
    |-------|------|----------------|
    | D | Minimal Protection | Evaluated but failed higher requirements |
    | C1 | Discretionary Security | Basic identification and authentication |
    | C2 | Controlled Access | Audit trails, individual accountability |
    | B1 | Labelled Security | Mandatory access control, sensitivity labels |
    | B2 | Structured Protection | Formal security model, covert channel analysis |
    | B3 | Security Domains | Reference monitor, minimised TCB |
    | A1 | Verified Design | Formal top-level specification, formal verification |

    True MLS required at least B1. High-assurance MLS required B3 or A1 -- and only three systems (Honeywell SCOMP, Aesec GEMSOS, Boeing SNS Server, all 1980s-vintage) ever achieved A1. None achieved commercial success.

It didn't work. David Bell himself, reflecting in 2005, identified why: the gap between abstract models and real implementations was larger than anyone anticipated; covert channels through timing and side effects undermined the theoretical guarantees; the evaluation process was prohibitively expensive; users wouldn't accept the rigid constraints; and there was no reliable way for a TOP SECRET user to sanitise a document down to SECRET without a privileged bypass that fundamentally undermined the MLS guarantee.

Some products still implement MLS features in limited contexts -- Oracle Label Security for database-level mandatory access control (notably deployed at US Army INSCOM spanning JWICS and SIPRNet), SELinux in MLS policy mode, General Dynamics PitBull. But these are niche. The broader MLS vision of a single operating system safely processing all classification levels on one machine never materialised at scale.

---

## MILS: the practical middle ground

Rather than building a single operating system that understands all security levels (MLS), use a small, verified separation kernel to create isolated partitions on shared hardware, each running at a single security level. Information flow between partitions is controlled by small, individually certifiable components.

MILS is, in essence, a highly assured form of MSL -- but implemented on shared hardware rather than physically separate systems, using a verified separation kernel rather than air gaps.

!!! info "MILS gives you partitions, not policy"

    MILS provides domain separation and controlled information flow. But the policy logic -- the actual cross-domain guard functionality (content inspection, treatments, security label checking) -- must still be built on top. MILS is an enabler for CDS, not a CDS in itself.

### The Rushby foundation

The intellectual foundation is John Rushby's 1981 paper "Design and Verification of Secure Systems." Rushby proposed the separation kernel: a small kernel whose sole purpose is to create an environment indistinguishable from a physically distributed system, where each partition behaves as a separate, isolated machine.

This was a radical simplification. Instead of building a kernel that understands complex security policies (Bell-LaPadula, Biba, roles), build a kernel that does only one thing: separate partitions. Move all policy enforcement into applications running within or between those partitions. The smaller the kernel, the easier it is to verify.

### The NEAT properties

For a separation kernel to be trustworthy enough to replace physical separation, it must satisfy four properties -- collectively known as NEAT. These aren't unique to MILS; they're the same reference monitor properties from the Orange Book, but applied specifically to the separation kernel rather than the entire operating system. That scoping is what makes MILS tractable where MLS was not -- you're verifying these properties for a small kernel, not a full OS.

**Non-bypassable**
:   There's no alternative path around the security monitor. Every interaction between partitions must go through it -- no back doors, no shortcuts, no shared memory regions that skip the check.

**Evaluatable**
:   The kernel is small and simple enough that it can actually be evaluated to the assurance level required. This is the whole point of the separation kernel concept -- a 10,000-line kernel can be formally verified; a million-line operating system can't.

**Always-invoked**
:   Every access, every message, every resource request is checked by the security monitor. Not just the first access, not just on open -- every single operation. There's no "once you're in, you're in."

**Tamperproof**
:   Nothing running in the partitions can modify the kernel's code, configuration, or data. The separation mechanism protects itself from the things it's separating.

### Why compositional certification is transformative

The key innovation of MILS is compositional certification. A single MILS system can contain a separation kernel certified at EAL 6-7, content filters certified at EAL 4-5, and unmodified Linux running in a partition alongside TYPE I crypto -- with the overall security argument composed from individual component certifications.

This changes the game because:

1. **The verification burden is distributed.** Instead of verifying a monolithic MLS OS, you verify a small kernel at the highest level and larger components at lower levels.
2. **Components can be updated independently.** Replacing an EAL 4 application doesn't require re-certifying the EAL 6+ kernel.
3. **COTS software can participate.** Unmodified Linux or Windows can run in a partition, with the separation kernel providing the assurance that it can't escape.
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

The separation kernel is the foundation of MILS. Five products represent the current state of the art -- they're listed here for reference, but the key takeaway is that verified separation kernels exist, they're production-ready, and they span US, European, and open-source options.

??? note "INTEGRITY-178B (Green Hills Software, USA)"

    The certification leader. Holds the highest Common Criteria rating ever achieved for a commercial operating system: **EAL 6+ / High Robustness**. The only separation kernel with **NSA Raise the Bar certification specifically for CDS**.

    - Hardware memory protection creating isolated partitions
    - Supports AMP, SMP, and BMP multiprocessing
    - Multivisor for running Linux, Windows, and Android as isolated guests
    - Platforms: ARM, x86, Power, RISC-V, MIPS
    - US-controlled (ITAR restrictions apply)

??? note "PikeOS (SYSGO, Germany)"

    The European sovereign option. **BSI EAL 5+** certified separation kernel, explicitly marketed as ITAR-free.

    - Type 1 hypervisor with strict time and space partitioning
    - First RTOS to achieve SIL 4 certification on multi-core platforms
    - Supports Rust as a development language within partitions
    - Pre-certified: DO-178C DAL A, EN 50716 SIL 4, ISO 26262 ASIL D
    - Platforms: ARM, x86, SPARC, Power, RISC-V
    - Partner in all three EU MILS research programmes

    PikeOS is owned by a German company with no US export restrictions. For European CDS implementations that can't depend on US-controlled technology, PikeOS provides a sovereign foundation with BSI certification.

??? note "seL4 (seL4 Foundation, Australia)"

    The formal verification benchmark. Has the most comprehensive mathematical proofs of any production OS kernel: functional correctness (implementation matches specification), information flow security (proven absence of leakage between partitions), and binary verification (proofs extend to the compiled binary). The proofs go beyond what Common Criteria, ISO 26262, and DO-178C require at their most stringent levels. Used in DARPA's HACMS programme to build demonstrably unhackable systems -- but not yet certified against a CDS-specific protection profile.

    - Platforms: ARM (primary), x86
    - Licence: GPL v2

??? note "LynxSecure (Lynx Software Technologies, USA)"

    Notable for its **information flow modelling language** -- an explicit, traceable way to define how data flows between partitions. The kernel remains "out of the data plane," meaning it can't be a vector for data leakage.

    - Customers: US Navy, US Army, NASA, Lockheed Martin, Northrop Grumman, BAE Systems
    - Designed for DO-178C DAL A, NIST, NSA Common Criteria compliance
    - Platforms: x86, ARM

??? note "Muen (codelabs, Switzerland)"

    The open-source option. Written in SPARK 2014 (formally verifiable Ada subset), with proven absence of runtime errors at source code level. Uniquely addresses covert channels explicitly -- a problem most platforms ignore.

    - Developed in cooperation with secunet Security Networks (a BSI-evaluated vendor)
    - Uses Intel VT-x, VT-d, EPT for separation; fixed cyclic scheduling
    - Licence: GPL v3
    - Platforms: x86 (primary), ARM64 (preliminary)

---

## Can MILS translate to cloud?

This is the critical question for software CDS. Cloud infrastructure increasingly resembles MILS architecture -- but with significant gaps.

### AWS Nitro: closer than you think

The AWS Nitro System is the most architecturally interesting convergence between cloud infrastructure and separation kernel principles:

- **The Nitro Hypervisor** is a minimised, modified Linux kernel with no networking support and no unnecessary capabilities. Its sole functions are CPU and memory partitioning. Once it sets up partitions, it's "just about always quiescent." This is remarkably close to the separation kernel concept.
- **The Nitro Security Chip** implements hardware root of trust with measured boot. The general-purpose processor can't change any firmware.
- **Firmware is immutable** -- EC2 servers can't update their own firmware. Only AWS can update it through the Nitro System.
- **All I/O runs on dedicated Nitro Cards**, off the general-purpose processor. Security group enforcement, encryption, and storage all happen in hardware.

#### Formal verification of Nitro

AWS has applied formal methods to Nitro components at increasing levels of ambition:

**The Nitro Isolation Engine (NIE)** -- announced at re:Invent 2025 and described as "the world's first formally verified cloud hypervisor" -- has been verified using the Isabelle/HOL proof assistant (the same tool used for seL4). The proof covers specifications of the Graviton-5 processor architecture, the Rust code of hypercalls and their functional correctness, and security properties of the isolation mechanism. It runs to approximately 250,000 lines of formal proof. Larry Paulson (emeritus professor of computational logic at Cambridge, Amazon Scholar) and John Harrison (AWS Senior Principal Applied Scientist) are among the researchers behind the work.

**Nitro Controller boot code** has been formally verified for memory safety -- proven free from buffer overflows, use-after-free, and similar memory corruption in the secure boot path (referenced in the AWS Nitro System Security Design whitepaper, with the underlying work published at CAV 2018).

**Nitro Controller network API** parsing has been proven free from memory safety errors "in the face of any configuration file and any network input" -- a universal proof, not just testing against sample inputs.

This is a significant development. But it's worth understanding the boundaries: the NIE proof artifacts aren't publicly available for independent scrutiny (unlike seL4's), binary-level refinement hasn't been confirmed, and information-flow (noninterference) proofs -- the gold standard for isolation assurance -- aren't explicitly claimed. No part of the Nitro System has undergone Common Criteria evaluation. The formal verification provides strong assurance for the specific properties proven, but the composition of all Nitro components into an end-to-end security argument hasn't been published.

??? question "How does Nitro map to MILS properties?"

    | MILS property | Nitro equivalent | Gap |
    |---------------|-----------------|-----|
    | Non-bypassable | Hardware-enforced partition boundaries | NIE formally verified, but no published noninterference proof |
    | Evaluatable | Minimal hypervisor, formally verified in Isabelle/HOL | No CC evaluation; proof artifacts not public |
    | Always-invoked | All I/O mediated by Nitro Cards | No published proof of complete mediation |
    | Tamperproof | Immutable firmware, measured boot | Boot code memory safety formally proven |

### Confidential computing: hardware memory encryption

AMD SEV-SNP, Intel TDX, and ARM CCA provide hardware-enforced memory encryption that brings cloud closer to MILS:

| Technology | Mechanism | What it gives you |
|-----------|-----------|------------------|
| **AMD SEV-SNP** | Encrypted VMs, protected from hypervisor | Infrastructure operator removed from trust boundary |
| **Intel TDX** | Encrypted "trust domains" | Attestation-gated data release |
| **ARM CCA** | Secure "realms" | Hardware memory isolation between workloads |

**What confidential computing doesn't provide:** no information flow control, no security labelling, no availability guarantees. Side-channel attacks have been demonstrated against all these platforms in lab conditions, though the practical exploitability in cloud environments is more nuanced (see below). The assurance gap between these technologies and a certified separation kernel remains significant.

### Cloud multi-account as MSL

The standard cloud security pattern for classified workloads is essentially MSL replicated in cloud infrastructure: separate AWS accounts per classification level, separate VPCs with no cross-level peering, separate AWS regions for higher classifications (GovCloud for IL5, Secret Region, Top Secret Region), and physical separation at the highest levels.

This is architecturally identical to traditional MSL -- separate systems at separate levels, connected only through CDS at the boundaries. Cloud multi-account strategies don't introduce MLS or MILS; they replicate MSL.

### Covert channels and low-visibility architectures

Traditional side-channel attacks -- cache timing, branch predictor state, power analysis -- assume the attacker knows which physical hardware the target is running on. In cloud, this assumption breaks down. AWS's architecture is deliberately opaque: no one person knows both which customer is running which workload and which physical server it's on. An attacker who can't solve the co-location problem -- getting their malicious VM onto the same physical host as the target -- can't mount most side-channel attacks regardless of whether the theoretical channel exists.

This isn't a formal closure of covert channels in the way that a separation kernel proof would be. But it's a practical barrier that on-premises systems don't have. The attacker's problem goes from "exploit this cache timing side-channel" to "first find the target among millions of servers, then get yourself scheduled onto the same host, then exploit the channel before either of you gets migrated."

AWS goes further than opacity alone. The Nitro System's default EC2 isolation already prevents cross-tenant CPU side-channels -- cores are pinned to instances, SMT threads are never shared between customers, and microarchitectural state is flushed on context switches. But for workloads where even theoretical co-residency is unacceptable, EC2 Dedicated Hosts give you an entire physical server (i.e. the actual underlying machine, not just a VM). No other customer's instances can be scheduled on that hardware while it's allocated to you. This eliminates the co-location prerequisite for side-channel attacks entirely -- not through software mitigation, but by removing the physical precondition. The US Air Force's GPS OCX programme runs on 200+ dedicated hosts in GovCloud for exactly this reason.

Layered with Nitro Enclaves (which provide cryptographically isolated compute where even root on the parent instance can't access enclave memory) and confidential computing (AMD SEV-SNP, Intel TDX), cloud providers can offer defence-in-depth that makes covert channel exploitation operationally very difficult -- even if theoretically possible.

### The gap

The honest assessment is that cloud infrastructure today provides **MSL with stronger isolation properties than traditional MSL** -- and the Nitro Isolation Engine's formal verification is a genuine step toward MILS-grade assurance. But gaps remain:

- No CC evaluation against a CDS protection profile
- No published information-flow (noninterference) proofs
- No end-to-end composition of verified components into a system-level security argument
- Platform under the cloud provider's physical control, not the operator's

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
