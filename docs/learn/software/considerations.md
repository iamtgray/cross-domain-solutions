# Key Considerations for Software CDS

Software CDS derives its security assurance from the correctness of code, configuration, and the underlying platform. This is fundamentally different from [hardware CDS](../hardware/index.md), where assurance comes from physics. Understanding what this difference means in practice -- for attack surface, certification, OS choice, and deployment decisions -- is essential for anyone designing or evaluating a software-based cross-domain solution.

## What makes software CDS different

### Larger trusted computing base

Hardware CDS products (especially [data diodes](../hardware/data-diodes.md)) have a tiny TCB -- the core security function is enforced by simple physical hardware. Software CDS exposes operating system interfaces, network stacks, parsing libraries, and management planes. All of these are part of the TCB, and all of them are potential attack surfaces.

### Harder to certify

Software CDS faces a tougher certification path because:

- A larger TCB means more code to evaluate, driving up cost and time
- Dynamic behaviour (threads, memory allocation, interrupts) is harder to prove correct than static hardware circuits
- Dependency chains (OS, libraries, drivers) expand the evaluation scope
- Each software update potentially changes the certified baseline

Common Criteria evaluation at EAL 3+ is generally required, with EAL 5+ needed for SECRET-to-UNCLASSIFIED transfers in the US. The cost and time for EAL 4+ evaluation can be significant.

### Greater flexibility

The flip side of complexity is capability. Software CDS can implement content inspection, protocol transformation, and policy enforcement that would be impractical in hardware alone. It can be patched, reconfigured, and extended without physical replacement. But this update velocity also means the certified baseline can drift.

### Different verification approach

[Hardware CDS can be verified by physical inspection](../hardware/characteristics.md) -- you can confirm the return fibre is absent. Software CDS requires code analysis, formal verification, or testing to establish trust. The NCSC notes that CDS should be implemented using industry best practice for secure design, coding, testing, and deployment, and that organisations facing advanced threats should augment with high assurance principles.

## Operating system security

The OS is the foundation. A compromised OS means a compromised guard. Three approaches exist, each with different assurance characteristics.

### Hardened general-purpose OS

Stripped-down Linux or Windows with mandatory access controls (SELinux, AppArmor). Widely available and well-understood, but the TCB remains large (millions of lines of code) and the kernel attack surface is extensive. Suitable as part of a defence-in-depth approach at lower assurance levels.

### Separation kernels (MILS architecture)

Separation kernels partition hardware resources into isolated compartments with formally defined information flows. This is the gold standard for software CDS platforms.

Key products:

**LynxSecure** (Lynx Software Technologies)
:   Controls hardware resources according to an information flow modelling language. Implements a distributed application runtime with unmodifiable, least-privilege resource allocation per partition. Crucially, the separation kernel remains out of the data plane -- it mediates access without being in the path of the data itself.

**INTEGRITY** (Green Hills Software)
:   Real-time OS with separation kernel architecture. The first separation kernel certified against the NSA's Separation Kernel Protection Profile (2008). Widely used in military and aerospace CDS applications.

**seL4** (seL4 Foundation)
:   A high-assurance microkernel that is unique because of its comprehensive formal verification. The implementation has been mathematically proven to match its specification. Open source. See the info box below.

**PikeOS** (SYSGO)
:   Combines separation kernel hypervisor technology with hard real-time capabilities. CC EAL5+ certified by BSI. Supports x86, ARM, RISC-V, PowerPC, and SPARC.

Why separation kernels matter for CDS:

1. **Provable isolation** -- partitions cannot interfere with each other
2. **Controlled information flow** -- data crosses partition boundaries only through explicitly defined channels
3. **Small TCB** -- typically tens of thousands of lines vs millions for Linux, making formal verification feasible
4. **Reference monitor properties** -- the kernel mediates all inter-partition access

### Microkernel-based OS

Some CDS products (INFODAS, genua) use microkernel operating systems to enforce unidirectional data transfer. These sit between hardened GPOS and full separation kernels in terms of assurance -- smaller TCB than Linux, but may lack the formal verification of seL4 or the evaluated separation properties of INTEGRITY.

## Virtualisation as a security boundary

Virtualisation is commonly proposed as a security boundary for software CDS. The reality is nuanced.

### Strengths

- Hardware-assisted virtualisation (Intel VT-x, AMD-V) provides CPU-level isolation between VMs
- Memory Management Unit virtualisation prevents VMs from accessing each other's memory
- Well-understood technology with decades of deployment
- Enables running different security domains on shared hardware

### Weaknesses

- **Hypervisor attack surface** -- Type-1 hypervisors (VMware ESXi, KVM, Xen) have large codebases and have suffered guest escape vulnerabilities (VENOM CVE-2015-3456, VMware guest-to-host escapes)
- **Shared resources** -- CPU caches, memory buses, and I/O devices create covert channels between VMs
- **Management plane exposure** -- The NCSC identifies management bypass as a key anti-pattern where layered defences in a network data plane can be short-cut via the management plane
- **Not designed as security boundaries** -- commercial hypervisors are not evaluated as security boundaries to the same standard as separation kernels

### Verdict

General-purpose hypervisors (VMware, KVM, Hyper-V) are insufficient as the sole security boundary for high-assurance CDS. Separation kernel hypervisors (LynxSecure, INTEGRITY, PikeOS) are purpose-built for this role. For lower-assurance environments (OFFICIAL to OFFICIAL-SENSITIVE), commercial hypervisors with hardening may be acceptable as part of defence-in-depth.

## Why containers are insufficient

Containers share the host kernel. This is the fundamental limitation.

- **Shared kernel** -- All containers run on the same OS kernel. A kernel vulnerability compromises all containers. This is categorically different from VM isolation.
- **Namespace and cgroup isolation** are software constructs within the kernel, not hardware-enforced boundaries. Container escape vulnerabilities (CVE-2019-5736 in runc, CVE-2020-15257 in containerd) have demonstrated that these boundaries can be breached.
- **Privileged containers** and excessive capabilities can collapse the isolation entirely.
- **No mandatory access control by default** -- containers rely on discretionary access control unless explicitly configured with SELinux or AppArmor profiles.

??? question "When might containers be appropriate in a CDS architecture?"

    Containers can be useful **within a single security domain** -- for isolating CDS microservice components from each other as a defence-in-depth measure. They can also work when combined with additional isolation layers like gVisor or Kata Containers (which wrap containers in lightweight VMs).

    They are **not appropriate** as the primary isolation boundary between different security classifications, where the threat model includes nation-state adversaries, or for any boundary requiring formal assurance or Common Criteria evaluation.

## Attack surface analysis

Software CDS attack surfaces include:

**Network-facing parsers** -- Protocol parsers (HTTP, SMTP, XML, JSON, MIME) are the most exposed components. The NCSC import pattern explicitly assumes transformation engines will be compromised because parsing untrusted content is inherently risky.

**Content inspection engines** -- Antivirus, DLP, and format validation components process untrusted data and are prime targets for exploitation.

**Management interfaces** -- The NCSC warns that management planes can bypass data plane security controls. Management of low-side components must be separate from high-side management.

**Inter-component communication** -- Message queues, shared memory, and IPC mechanisms between CDS components.

**Dependencies** -- Libraries (OpenSSL, libxml2, image decoders), language runtimes, and OS services. Each dependency expands the attack surface.

**Boot and update mechanisms** -- UEFI, firmware, and software update channels.

The NCSC import pattern recommends a four-component defence-in-depth approach:

1. **Transformation Engine** (low-side) -- parses and simplifies untrusted content
2. **Protocol Break and Flow Control** -- enforces the boundary
3. **Verification Engine** (high-side) -- validates the simplified content
4. **Destination System**

The components are ordered deliberately: risky parsing happens on the low (untrusted) side, so that compromise of the parser does not directly expose the high side. This is the same [protocol break](../how-it-works/protocol-break.md) concept used in hardware guards, implemented in software.

## Side-channel attack risks

Software CDS running on shared hardware is vulnerable to side-channel attacks that do not affect physically separated [hardware CDS](../hardware/characteristics.md):

- **CPU cache timing attacks** (Spectre, Meltdown, and variants) allow one process to infer data from another sharing the same CPU, even across VM boundaries
- **Memory bus contention** reveals information about co-resident workloads through timing differences
- **TLB and branch predictor state** leaks across isolation boundaries through shared microarchitectural state
- **Network timing** variations can leak information about content inspection decisions

Mitigations include CPU core pinning, cache partitioning (Intel CAT, ARM MPAM), disabling simultaneous multithreading across trust boundaries, and constant-time algorithms. But the most reliable mitigation is physical separation -- which argues for hardware CDS at higher assurance levels.

Side-channel attacks are a primary reason why the highest classification boundaries still require physical separation rather than software isolation.

## Memory safety and language choice

Memory safety vulnerabilities (buffer overflows, use-after-free, double-free) account for approximately 70% of security vulnerabilities in systems software. For CDS components that parse untrusted data, language choice is a critical architectural decision.

**Rust**
:   Provides memory-safety and thread-safety through its type system and ownership model, eliminating many bug classes at compile-time with no runtime or garbage collector overhead. Increasingly adopted for security-critical software -- the Linux kernel now accepts Rust modules, and CISA and NSA have both recommended memory-safe languages.

**Ada/SPARK**
:   The SPARK subset provides formal verification capabilities including proof of absence of runtime errors. Used for decades in high-assurance systems (avionics, rail signalling). SPARK code may have a smoother EAL evaluation path than Rust, though Rust's ecosystem is far larger.

For CDS: new components (content parsers, protocol handlers, validation engines) should strongly prefer memory-safe languages. Existing C/C++ codebases represent ongoing risk -- rewriting critical parsers in Rust or applying formal verification can reduce this.

## The four reference monitor properties

The reference monitor concept defines four essential security properties that any CDS must satisfy:

**Non-bypassable**
:   The CDS must mediate all information flows between security domains. There must be no alternative path. The NCSC identifies management bypass as a key anti-pattern -- layered defences in the data plane can be short-cut via the management plane.

**Always-invoked**
:   The CDS must be invoked for every cross-domain access attempt, without exception. Even if the path exists, enforcement must trigger on every transaction.

**Tamperproof**
:   The CDS enforcement mechanisms must resist modification by unauthorised parties, including compromised components within the CDS itself. Separation kernels achieve this through immutable resource allocation.

**Evaluatable (verifiable)**
:   The CDS must be simple enough to be subjected to thorough analysis. This is the argument for minimal TCB and formal verification. A CDS that cannot be meaningfully evaluated cannot be trusted.

These properties map directly to the [NEAT properties](../hardware/guards.md) defined by the MILS architecture. Hardware CDS achieves them through physics; software CDS must achieve them through rigorous engineering, formal methods, and careful architectural design.

## When software CDS is appropriate

Software CDS is the right choice when:

- The classification boundary is lower sensitivity (OFFICIAL to OFFICIAL-SENSITIVE, or between business domains)
- Bidirectional communication with complex content inspection is needed
- Scalability and cloud deployment are requirements
- Cost constraints preclude dedicated hardware
- The threat model does not include nation-state hardware exploitation capabilities
- Rapid policy updates and protocol changes are expected

## When hardware is required

[Hardware CDS](../hardware/index.md) is required when:

- The classification boundary is high (SECRET to TOP SECRET, or classified to unclassified)
- Unidirectional flow must be physically guaranteed
- The threat model includes adversaries with sophisticated side-channel attacks
- Regulatory or accreditation requirements mandate physical separation
- Air-gap enforcement is needed

### Hybrid approaches

Most real-world deployments use a combination: a hardware [data diode](../hardware/data-diodes.md) for the physical enforcement layer, software guards on separation kernels for content inspection and policy enforcement, and the hardware providing the physics-based assurance floor whilst the software provides intelligence and flexibility. The [hardware vs software analysis](hardware-vs-software.md) covers this decision in detail.
