# Guards

A cross-domain guard is a device that enables controlled, bidirectional information exchange between networks of different security classifications. Unlike a [data diode](data-diodes.md) that enforces one-way flow through physics, a guard actively inspects, filters, sanitises, and controls data in both directions according to security policy.

Think of a data diode as a one-way valve. A guard is more like a border checkpoint: traffic flows in both directions, but every vehicle is stopped, inspected, and only allowed through if it meets the entry requirements.

## Guards vs data diodes

| Property | Data Diode | Guard |
|----------|-----------|-------|
| **Direction** | Strictly one-way (physical) | Bidirectional (controlled) |
| **Content inspection** | None -- passes all data blindly | Deep content inspection and filtering |
| **Security function** | Prevents any reverse flow | Inspects, filters, sanitises, controls |
| **Complexity** | Simple (physically one-way) | Complex (policy engine, filters, multiple subsystems) |
| **Attack surface** | Minimal | Larger (more code, more interfaces) |
| **Achievable assurance** | EAL7+ (simple function) | Typically EAL4--5 (complex function) |
| **Protocol handling** | Requires external proxy agents | Handles protocols natively with protocol-specific filters |
| **Trusted Computing Base** | Very small | Larger (OS, filters, policy engine, audit) |
| **Use cases** | One-way data export/import | Email exchange, web browsing, interactive queries across domains |

The core difference: a data diode passes all data in one direction without looking at it. A guard looks at every piece of data, decides whether it should pass based on policy, and may transform it along the way. Guards are fundamentally different from firewalls too -- firewalls filter based on addresses, ports, and protocol headers, whilst guards operate at the application and content level.

??? question "When should I use a guard instead of a data diode?"

    Use a guard when you need **bidirectional communication** -- email exchange between classification levels, web browsing from a high network to a low network, interactive queries across domains, or any scenario where data must flow in both directions with content inspection.

    Use a [data diode](data-diodes.md) when data only needs to flow one way and maximum assurance is the priority.

    Some architectures use data diodes *within* a guard -- separate diodes for each direction with content inspection in between. Nexor's product line illustrates this spectrum: the Nexor Data Diode (pure one-way), the Nexor GuarDiode (data diode with import filtering), and the Nexor Guardian (full bidirectional guard).

## Guard architecture

A hardware guard is built from four core subsystems, each with a distinct security role.

### Interface subsystems

Each connected security domain gets a dedicated interface subsystem. In hardware guards, these are **physically separate** -- either separate computers, separate processors, or separate partitions enforced by a separation kernel. The guard sits between the networks, terminating connections on each side independently.

### Security-enforcing components

The heart of the guard. These components inspect content and enforce security policies:

**Content filters**
:   Inspect data format, structure, and content against policy rules.

**Security label verification**
:   Check classification markings on data against the security policy and the clearances of source and destination.

**Malware scanning**
:   Anti-virus and anti-malware inspection. The BAE Systems XTS Guard 7 includes redundant native filters for anti-virus and XML.

**Format validation**
:   Verify data conforms to expected formats -- XML schema validation, image format checking, document structure verification.

**Content Disarm and Reconstruction (CDR)**
:   A more aggressive approach than traditional malware scanning. Strips away risky elements and rebuilds data in a safe format, removing hidden threats.

**Dirty word checking**
:   Search for blacklisted terms or patterns that should not cross the domain boundary.

**Digital signature verification**
:   Validate data authenticity and integrity. Handle signed and encrypted content types.

### Policy engine

Defines what data is allowed to pass, in which direction, under what conditions. Modern policy engines support source/destination whitelists, data type restrictions, security label policies, volume and rate controls, time-based restrictions, and customisable filter chains. The BAE Systems XTS Guard 7 provides a pluggable filter architecture where new filters can be added to the policy chain via a simple API.

### Audit and logging

Comprehensive logging of all data transfers (permitted and blocked), policy decisions, administrative actions, and system health events. The Nexor Sentinel provides full message logging for communication exchange integrity. Guards generate the evidence trail that accreditors and auditors need to verify the system is working as intended.

## The protocol break

A fundamental security mechanism in hardware guards. The guard does not simply forward network packets between domains. Instead:

1. **Terminate** the network connection on the source side
2. **Extract** the application-level data (content)
3. **Inspect** the content through the filter chain
4. **Create a new** network connection on the destination side
5. **Send** the inspected content through the new connection

This ensures that no network-level attack -- malformed packets, protocol exploits, covert channels in packet headers -- can pass through the guard. The guard acts as a full application-layer proxy, engaging in separate communications on each interface and passing only business information that meets configured checks.

This is the same [protocol break](../how-it-works/protocol-break.md) concept used across CDS architectures, but hardware guards implement it with physically separate subsystems on each side.

## MILS and separation kernel architecture

### MILS

MILS (Multiple Independent Levels of Security) is the dominant security architecture for high-assurance guards. It provides a structured approach to building systems that handle data at multiple classification levels simultaneously, based on the concepts of separation and controlled information flow.

MILS requires four essential properties -- the **NEAT** properties:

**Non-bypassable**
:   A component cannot use another communication path to bypass the security monitor.

**Evaluatable**
:   Any trusted component can be evaluated to the assurance level required of it.

**Always-invoked**
:   Every access and every message is checked by the appropriate security monitor.

**Tamperproof**
:   The system controls modification rights to the security monitor code, configuration, and data.

In a MILS-based guard, a separation mechanism divides the hardware into isolated partitions. Each security domain has its own partitions, security-enforcing components run in dedicated partitions, and information flow between partitions is controlled by the separation kernel according to configured policy. A key advantage is **compositional certification** -- different components can be certified at different assurance levels within one system (the separation kernel at EAL6--7, content filters at EAL4--5), with the overall security argument composed from the individual component certifications.

### Separation kernels

A separation kernel provides hardware-enforced partitioning of system resources. The concept was introduced by John Rushby in 1981, with the goal of creating an environment indistinguishable from that provided by a physically distributed system.

The kernel allocates all resources (CPU time, memory, I/O devices) into partitions, isolates them so actions in one partition cannot be detected by subjects in another, and controls information flow between partitions according to configured policy. The result is both tamperproof and non-bypassable.

Key commercial separation kernels used in guards:

| Kernel | Vendor | Certification | Key Feature |
|--------|--------|--------------|-------------|
| **INTEGRITY-178B** | Green Hills | First SKPP-certified (2008) | Military and aerospace heritage |
| **PikeOS** | SYSGO | CC EAL5+ (BSI) | Multi-architecture (x86, ARM, RISC-V); real-time |
| **LynxSecure** | Lynx Software | DO-178C, NIST, NSA CC | Immutable resource allocation; stays out of data plane |
| **seL4** | seL4 Foundation | Formal proof of correctness | Only OS kernel with mathematical functional proof |
| **Muen** | Open source | Formally verified | Open source separation kernel for x86 |

!!! info "The seL4 difference"

    seL4 is unique because it has a **mathematical proof of functional correctness** -- the implementation has been proven to match its specification. This covers functional correctness, integrity (information cannot flow between partitions unless allowed), confidentiality (data cannot leak between partitions), and binary correctness (the compiled binary matches the verified C code, for ARM). It is the strongest available software assurance basis for a CDS platform. See [software CDS considerations](../software/considerations.md) for more on formal verification.

## Content-specific guards

### Email guards

The most common use case. Email guards inspect envelope data, message headers (including security labels), message body, attachments, digital signatures, and encryption. The Nexor Sentinel supports SMTP and X.400 protocols with policy-enforcing content, security and protocol filters, access control lists, allowed attachment type filtering, and dirty word searching.

### File transfer guards

Inspect files moving between domains: file type identification and validation, content scanning, security label checking, malware scanning, and CDR. The Nexor Guardian facilitates safe exchange between networks of differing trust levels with deep content inspection.

### Web access guards

Allow users on a high-classification network to browse content on lower-classification networks. BAE Systems' Secure Access Gateway enables browsing "down" from high to low trust networks without significant infrastructure additions.

### Video and streaming guards

For real-time video and streaming content between domains. BAE Systems provides Secure Transfer Streaming for real-time communications between classified networks, supporting secure voice and video. Owl Cyber Defense's XD Vision handles HD video across 2 to 20+ networks simultaneously.

### Multi-protocol guards

Modern guards handle multiple data types simultaneously. The BAE Systems XTS Guard 7 supports SFTP, SMTP, UDP, TCP, HTTP/S, chat, XML, and ISR imagery, with up to 22 enclaves and multi-hop capability for transfers spanning multiple classification levels.

## Assured vs basic guards

### Assured (high assurance) guards

Designed for the highest-risk transfers -- between SECRET/TOP SECRET and lower classifications, or between national and coalition networks.

- Common Criteria EAL5+ evaluation
- Hardware-enforced domain separation
- Raise the Bar (RTB) compliance (US)
- Minimal trusted computing base
- Redundant, diverse filter implementations
- High-assurance OS foundation
- Full audit trail
- Developed by cleared personnel in secure facilities
- Independently assessed through national security evaluation programmes

In the US, EAL 5 or higher is required to export from a SECRET domain to an UNCLASSIFIED domain.

### Basic (medium assurance) guards

Suitable for lower-risk transfers -- between networks of similar classification, or between OFFICIAL and OFFICIAL-SENSITIVE.

- May use software-based separation (VM isolation, hardened OS)
- Common Criteria EAL3--4 or Principles-Based Assurance
- Standard commercial OS with hardening
- Content inspection and filtering
- May not require cleared development teams

The distinction is ultimately determined by the **risk assessment** for the specific deployment -- the classification levels involved, the threat environment, and the national security authority's requirements. See [certification and evaluation](../standards/certification.md) for details on the evaluation process.

## Human review

For the highest-risk transfers -- typically high-to-low, where classified data is being released to a lower classification -- human review may be required. The original ACCAT guard implemented email export with human review. The Nexor Guardian supports a manual review option for flagged documents. This is a defence-in-depth measure: automated filters catch most issues, and a human reviewer handles edge cases that require judgement.

## Major vendors

| Vendor | Key Products | Type | Differentiator |
|--------|-------------|------|---------------|
| **BAE Systems** | XTS Guard 7 (Enterprise + Tactical) | Multi-enclave bidirectional guard | Up to 22 enclaves; RTB-compliant; STOP OS; hundreds of deployments |
| **Everfox** | High Speed Guard, Trusted Gateway, Hardsec | Guard + hardware-enforced isolation | Incorporates Garrison's Hardsec (FPGA/silicon security) and Deep Secure's content inspection |
| **Nexor** | Guardian, Sentinel, Edge Guard | Bidirectional guard + email guard | UK-built; NCSC-aligned; CDR; NATO NIAPC listed |
| **Owl Cyber Defense** | XD Bridge ST | CDS with guard function | Hardware-enforced separation with NCDSMO-certified data diode |
| **INFODAS** | SDoT Cross Domain Solutions | Bidirectional gateway | BSI approved to SECRET; microkernel-based; made in Germany |
| **Advenica** | ZoneGuard | Network guard | Swedish national security trusted; File Security Screener |
| **Lockheed Martin** | Radiant Mercury | Cross-domain guard | US Navy heritage |

## Testing and evaluation

Hardware guards undergo rigorous evaluation before deployment in national security environments.

### Common Criteria evaluation

Products are evaluated against the ISO/IEC 15408 framework. The vendor develops the product with security documentation, an accredited evaluation lab tests it against the Security Target, a national authority certifies the evaluation, and a certificate is issued specifying the evaluated configuration and assurance level. Higher EAL levels do not mean "better security" -- they mean the claimed security assurance has been more extensively verified.

### Lab-Based Security Assessment (LBSA)

In the US, CDS products undergo LBSA conducted by NSA/NCDSMO. This goes beyond Common Criteria by testing specifically for cross-domain threats: covert channel analysis, penetration testing, bypass attempts, configuration testing, and failure mode analysis.

### Raise the Bar (RTB)

The NSA's RTB programme establishes enhanced requirements for CDS architecture, including OS heterogeneity (using different operating systems on different components), content filtering requirements, architecture standards, and covert channel mitigation. The BAE Systems XTS Diode was the first product named RTB-compliant by NCDSMO and NSA.

### SABI/TSABI

For deployed US government configurations, SABI (Secret and Below Interoperability) and TSABI (Top Secret and Below Interoperability) evaluate specific deployment configurations -- not just the product in isolation -- to ensure the complete system meets security requirements.

### UK Principles-Based Assurance

The UK NCSC evaluates CDS against [13 security principles](../standards/certification.md) covering network protocol attack protection, content-based attack protection, unauthorised export protection, session isolation, and more. This is a risk-based approach rather than purely prescriptive.

!!! warning "Trusted subjects"

    A guard acts as a **trusted subject** in the Bell-LaPadula sense -- it is permitted to violate the normal "no write down" rule under strict controls. The guard reads data from a high-classification domain and writes sanitised data to a low-classification domain. This controlled exception is what makes guards both powerful and risky. If a guard is compromised, the entire cross-domain security could be defeated. This is why guards require such high levels of assurance -- they are the trusted exception to the mandatory access control policy. See [security enforcement](../how-it-works/security-enforcement.md) for more on how this works.
