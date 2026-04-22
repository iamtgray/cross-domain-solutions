# International Standards

Every CDS product is built and evaluated against a layered set of standards -- some international, some national, some alliance-specific. Understanding these standards is essential for anyone specifying, procuring, or deploying cross-domain solutions.

This page covers the major frameworks. For how these standards translate into practical certification processes, see [Certification and Evaluation](certification.md). For the classification systems these standards protect, see [Classification Systems](classification.md).

## Common Criteria (ISO/IEC 15408)

The Common Criteria is the international standard for evaluating IT security products. It provides a framework where independent assessors test a product against defined security requirements and assign an Evaluation Assurance Level (EAL).

Think of it as a graded exam for security products. The higher the grade, the more evidence the vendor must provide and the more rigorous the testing.

### Evaluation Assurance Levels

| Level | Name | What it means | CDS relevance |
|-------|------|---------------|---------------|
| EAL1 | Functionally Tested | Basic independent testing against the spec | Not used for CDS |
| EAL2 | Structurally Tested | Developer provides design info and test results | Baseline for some low-risk devices |
| EAL3 | Methodically Tested and Checked | Thorough investigation without re-engineering | Minimum for some CDS components |
| EAL4 | Methodically Designed, Tested and Reviewed | Rigorous commercial security engineering | Typical for COTS components in CDS; ~$500K--$1.5M over 12--24 months |
| EAL5 | Semi-Formally Designed and Tested | Designed with EAL5 intent from the start | Required for some CDS; typical for separation kernels |
| EAL6 | Semi-Formally Verified Design | Rigorous development environment; high-risk situations | High-assurance RTOS (e.g. INTEGRITY-178B) |
| EAL7 | Formally Verified Design and Tested | Full formal methods and source analysis | Data diodes (e.g. Sentyron, Arbit); tightly focused functionality |

The EAL required for a CDS depends on the [classification pair](classification.md) it bridges. Moving data from SECRET to UNCLASSIFIED typically requires EAL5 or higher. The widest gaps -- TOP SECRET/SCI to UNCLASSIFIED -- may require EAL6 or EAL7 with formal verification.

!!! warning "CCRA mutual recognition stops at EAL2"
    The Common Criteria Recognition Arrangement (CCRA) only provides mutual recognition up to **EAL2** (with flaw remediation augmentation). Since CDS typically require EAL4 or above, a Common Criteria certificate from one country does **not** automatically carry weight in another for CDS purposes. Each nation effectively requires its own evaluation. See [Certification and Evaluation](certification.md) for the practical implications.

### Protection Profiles

A Protection Profile (PP) defines security requirements for a class of products without being tied to a specific implementation. CDS-relevant PPs include:

- **Collaborative Protection Profile for Network Devices (NDcPP)** -- baseline for network devices in CDS architectures
- **Firewall Protection Profile** -- packet filtering and stateful inspection components
- **Application Layer Gateway PP** -- content-inspecting [guards](../hardware/guards.md)
- **Separation Kernel Protection Profile (SKPP)** -- published by NSA in 2007 for separation kernels, the foundation of many CDS architectures. INTEGRITY-178B was the first product certified against it (September 2008). The SKPP was **discontinued in 2011** as part of a shift toward evaluating CDS as complete systems rather than individual components.

### Key limitations of Common Criteria

CC evaluation focuses primarily on documentation and process assessment, not on active adversarial testing. The US [Raise the Bar](certification.md) initiative was created partly to address this gap by adding penetration testing and architecture review on top of CC.

The evaluation process is also slow. By the time a product completes certification, it may already be a generation behind.

---

## NIST Standards

The US National Institute of Standards and Technology publishes several standards directly relevant to CDS.

### SP 800-53 Rev. 5 -- AC-4 Information Flow Enforcement

AC-4 is the primary NIST control for cross-domain information flow. It requires organisations to enforce approved authorisations for controlling information flow within and between systems.

The AC-4 family has over 30 enhancements, many directly applicable to CDS:

| Enhancement | What it covers |
|-------------|---------------|
| AC-4(1) | Flow enforcement based on security attributes and metadata |
| AC-4(2) | Enforcement between processing domains (separation kernels) |
| AC-4(8) | Security and privacy policy filters |
| AC-4(9) | Human reviews before release |
| AC-4(12) | Data type identifiers |
| AC-4(19) | Validation of metadata |
| AC-4(20) | Approved solutions (NCDSMO baseline) |
| AC-4(21) | Physical or logical separation of information flows |
| AC-4(28) | Linear filter pipelines |
| AC-4(30) | Filter mechanisms using multiple processes |
| AC-4(31) | Failed content transfer prevention |

If you are accrediting a CDS in a US government environment, these AC-4 enhancements form the control baseline you will be assessed against.

### SP 800-207 -- Zero Trust Architecture

Zero Trust shifts security from static network perimeters to per-session, identity-based verification. For CDS, ZTA is complementary rather than contradictory -- CDS are effectively continuous verification mechanisms at domain boundaries.

The practical intersection: ZTA mandates (US Executive Order 14028, DoD Zero Trust Strategy) are creating demand for CDS that integrates with identity-aware, continuously verified architectures. See [Academic Research](../landscape/research.md) for current research on the CDS-ZTA relationship.

### CNSSI 1253 -- Security Categorisation for National Security Systems

CNSSI 1253 maps SP 800-53 controls to National Security Systems (NSS) requirements. Most systems that process classified information and require CDS fall under NSS. CNSSI 1253F Attachment 3 contains the authoritative US government definition of a cross-domain solution.

---

## NATO Standards

NATO members must interoperate across national boundaries and classification systems, which drives a distinct set of standards.

### Security labelling standards

| Standard | Purpose | CDS relevance |
|----------|---------|---------------|
| **STANAG 4774** | Confidentiality metadata label syntax | Defines the machine-readable format for NATO security labels. [Guards](../hardware/guards.md) must parse and enforce these labels during cross-domain transfers. |
| **STANAG 4778** | Metadata binding mechanism | Cryptographically binds labels to data so they cannot be separated or tampered with. Guards verify these bindings before making release decisions. |
| **STANAG 4406** | Military Message Handling System (MMHS) | Governs cross-domain email exchange in military messaging systems. |

These STANAGs are the technical backbone of NATO cross-domain interoperability. Products like Isode's M-Guard and the INFODAS SDoT Labelling Service implement STANAG 4774/4778 natively. See [Vendors and Products](../landscape/vendors.md) for details.

### Security policy

**C-M(2002)49** is NATO's foundational security policy document. It defines classification levels, handling requirements, clearance standards, and information system security requirements. Every NATO-connected classified system -- including every CDS operating at a NATO boundary -- must comply with it.

The **AC/322-D directive series** provides detailed technical security guidance. Key directives include:

| Directive | Subject |
|-----------|---------|
| AC/322-D/0030-REV6 | CIS Interconnection -- the core NATO standard governing how systems connect across domains |
| AC/322-D(2019)0041-REV1 | NIAPC establishment and COTS products in NATO |
| AC/322-D/0048-REV3 | CIS Security Requirements |
| AC/322-D(2021)0032 | Protection of NATO information in public cloud (commonly called "D32") |

The "D32" directive is particularly relevant as cloud adoption grows. It provides a framework for securing NATO information in commercial cloud environments -- creating new cross-domain challenges. See [National Approaches](../landscape/national-approaches.md) for how the NATO committee structure manages these directives.

### NATO Information Assurance Product Catalogue (NIAPC)

The NIAPC is the central NATO registry of evaluated information assurance products, maintained by the NCI Agency. Products are evaluated by national evaluation authorities (BSI, NCSC, ANSSI, NIAP, and others) and submitted for NIAPC listing.

As of April 2026, the NIAPC lists **26 products** in the data diode category alone, from vendors across the alliance. Products listed range from NATO RESTRICTED to **NATO COSMIC TOP SECRET** (Arbit Data Diode 10GbE). See [Vendors and Products](../landscape/vendors.md) for the full NIAPC product listing.

---

## IEC 62443 -- Industrial CDS

IEC 62443 is the international standard for industrial automation and control system (IACS) cybersecurity. It is directly relevant to CDS at the IT/OT boundary -- the point where corporate IT networks meet operational technology controlling physical processes.

### Security Levels

| Level | Protection against | Attacker profile |
|-------|-------------------|-----------------|
| SL-0 | No special requirement | N/A |
| SL-1 | Accidental misuse | Unintentional |
| SL-2 | Intentional misuse by simple means | Low resources, script-level |
| SL-3 | Sophisticated intentional misuse | Moderate resources, IACS-specific knowledge |
| SL-4 | Sophisticated misuse with extensive resources | Nation-state / APT |

### Zones and conduits

IEC 62443 introduces a **zones and conduits** architecture that maps directly to the CDS concept:

Zones
:   Groups of systems with common security requirements. Each zone has a defined security level.

Conduits
:   Communication paths between zones. A conduit between an IT zone and an OT zone functions as a cross-domain solution -- enforcing different security policies on each side.

IEC 62443 is a "horizontal standard" -- all sector-specific OT security standards must use it as their foundation. [Data diode](../hardware/data-diodes.md) vendors like Waterfall Security, genua (cyber-diode with OPC UA), and Fend target IEC 62443 environments directly.

---

## IETF Email Labelling Standards

Two IETF standards are relevant to cross-domain email solutions:

**RFC 2634 -- Enhanced Security Services (ESS) for S/MIME**
:   Defines cryptographically bound security labels for email. Labels include security policy identifier, classification level, privacy marks, and security categories. The "triple wrapping" mechanism (inner signature, encryption, outer signature) enables routing decisions on encrypted messages. This is the standard underlying high-assurance cross-domain email guards.

**RFC 7444 -- Security Labels in Internet Email (SIO-Label)**
:   Defines a simpler header-based labelling approach. Unlike RFC 2634, it provides **no integrity protection** -- labels are plain-text headers that can be modified in transit. Useful for display purposes but insufficient as the sole labelling mechanism for high-assurance CDS.

---

## DoD Instruction 8540.01

DoDI 8540.01 is the US Department of Defense's governing instruction for cross-domain policy. It formally defines the three CDS types:

1. **Access Solution** -- enables viewing and manipulating information across different security levels
2. **Transfer Solution** -- moves information between security domains
3. **Multi-Level Solution (MLS)** -- single-domain storage with trusted labelling and [Mandatory Access Control](../../glossary.md)

All three types must be on the NCDSMO [cross-domain baseline list](certification.md) before they can be deployed in DoD environments.

---

## ISO/IEC 27001 and 27002

ISO 27001 (Information Security Management System) and 27002 (Security Controls) provide the governance framework within which CDS operates. They cover information classification, access control, cryptography, and communications security -- all relevant to CDS environments.

ISO 27001 certification demonstrates organisational security capability (several [CDS vendors](../landscape/vendors.md) hold it) but does not replace product-specific CC evaluation or national CDS assurance schemes. It is a necessary but not sufficient condition for operating in the CDS space.
