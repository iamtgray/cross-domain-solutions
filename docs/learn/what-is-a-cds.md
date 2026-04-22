# What is a Cross-Domain Solution?

A cross-domain solution (CDS) is a system that controls the flow of information between security domains at different classification levels. It inspects, transforms, and enforces policy on the *content* that crosses a boundary -- not just the network packets carrying it.

That content-level enforcement is what distinguishes a CDS from every other network security device. A CDS understands *what the data is*, not just where it is going.

---

## The Formal Definitions

Different national authorities define CDS in slightly different terms, but all agree on the core idea: controlled, policy-mediated information flow between security domains.

??? quote "NIST (United States)"

    NIST SP 800-53 Rev. 5, citing CNSSI 1253, defines a CDS as:

    > A form of controlled interface that provides the ability to manually and/or automatically access and transfer information between different security domains.

    The definition further notes that "a CDS may consist of one or more devices."

??? quote "NCSC (United Kingdom)"

    The NCSC defines CDS as mechanisms and systems used to connect different domains, where a domain is a collection of computer and network equipment that shares services, accesses, and has the same rules and policies. The purpose of a CDS is to enable information flow while mitigating or reducing cyber attacks and information leaks.

??? quote "NSA / NCDSMO (United States)"

    The NSA, through the National Cross Domain Strategy and Management Office (NCDSMO), treats CDS as an integrated information assurance system composed of specialised software or hardware that provides a controlled interface to manually or automatically enable and/or restrict the access or transfer of information between two or more security domains based on a predetermined security policy.

    The NCDSMO maintains the cross-domain baseline list -- all CDS products must appear on this list before Department of Defense deployment is permitted.

??? quote "ASD (Australia)"

    The Australian Signals Directorate (ASD), through the Information Security Manual (ISM), provides guidance on cross-domain solutions within its "Guidelines for Data Transfers" and "Guidelines for Gateways" sections. The ISM addresses security controls for transferring data between security domains of different classification levels, including requirements for content filtering, data diodes, and cross-domain guards.

---

## CDS is Not a Firewall

This is the single most important distinction for anyone new to the field.

!!! warning "A CDS is not a firewall"

    A **firewall** filters traffic based on network-level rules: ports, protocols, IP addresses, packet headers. It decides whether a connection is allowed, but it does not understand the content flowing through that connection.

    A **CDS** inspects, transforms, and controls the actual data content flowing between security domains. It terminates the network protocol entirely ([protocol break](how-it-works/protocol-break.md)), extracts the payload, runs it through a pipeline of [security treatments](how-it-works/treatments.md), and rebuilds a new connection on the other side.

    If your device passes network packets through with rule-based filtering, it is a firewall. If it tears the protocol apart, inspects the content, and rebuilds everything from scratch, it might be a CDS.

| | Firewall | CDS |
|---|---|---|
| **Inspects** | Packet headers (IP, port, protocol) | Data content (files, messages, structured data) |
| **Decides based on** | Network rules (allow/deny by source, destination, port) | Security policy (classification, labels, content type, release authority) |
| **Protocol handling** | Passes packets through (or drops them) | Full [protocol break](how-it-works/protocol-break.md) -- terminates and reconstructs |
| **Content awareness** | None (or limited DPI) | Deep -- format verification, CDR, dirty word search, label checking |
| **Assurance model** | Configuration correctness | Evaluated security mechanisms (often Common Criteria certified) |
| **Typical use** | Network perimeter defence | Boundary between classification levels |

---

## A Brief History

Cross-domain solutions have roots going back over fifty years, emerging from the US Department of Defense's work on multilevel security.

### Foundations (1970s--1980s)

The formal security models that underpin every CDS were developed in this era:

- **Bell-LaPadula model (1973)** formalised the "no read up, no write down" confidentiality rules. A subject cannot read data above its clearance, and cannot write data below its clearance. These rules remain the theoretical foundation of CDS design. See [Data Flow Models](how-it-works/data-flow.md) for how BLP maps to actual CDS data flows.
- **Biba integrity model (1977)** provided the complementary integrity rules -- "no read down, no write up" -- protecting trusted systems from contamination by less-trusted data.
- The **Orange Book (TCSEC)** provided evaluation standards for multilevel security systems, with B3/A1 ratings representing the highest assurance.

Early systems included Honeywell's SCOMP (evaluated at Orange Book A1) and NSA's Blacker system.

### The Guard Era (1980s--1990s)

The first information security guards emerged before firewalls (which arrived around 1987). Early guards like the ACCAT Guard were designed for a specific job: controlled release of email from classified systems through human review. Guards operated at the application layer, inspecting content rather than just packet headers -- a fundamental distinction that persists today.

### NSA Governance (2000s--2010s)

The US established the NCDSMO within the NSA to provide centralised governance. Key developments included:

- **Raise the Bar (RtB)** -- an NSA initiative mandating more rigorous testing through Lab-Based Security Assessment
- **The cross-domain baseline list** -- a controlled register of CDS products approved for DoD use
- **Common Criteria evaluation** -- products required evaluation against specific Protection Profiles, with higher classification boundaries requiring higher Evaluation Assurance Levels

### Modern Era (2010s--present)

CDS has expanded well beyond its military origins. Data diodes are now mandated for nuclear facilities (US NRC) and railway systems (French ANSSI). Virtualised CDS using separation kernels is emerging. Enterprise adoption spans financial services, healthcare, and legal sectors. Optical data diodes now support 100 Gbps throughput.

The market has grown to [$3.75 billion (2025)](why-cds-matters.md), with the top five vendors holding roughly 55% market share.

---

## CDS and Multi-Level Security

CDS and multilevel security (MLS) are closely related but distinct. Understanding the distinction matters because it affects architecture choices.

MLS
:   A system that processes information at multiple classification levels simultaneously, using mandatory access control and security labels to enforce access rules based on user clearance and data classification. An MLS system is effectively an "all-in-one CDS, encompassing both access and data transfer capabilities."

Multiple Single Level (MSL)
:   An architecture that maintains strict physical or logical separation of domains, with each domain operating at a single security level. CDS components (guards, diodes, filters) mediate transfers between these separated domains. This is what most real-world CDS deployments use.

MILS (Multiple Independent Levels of Security)
:   A high-assurance architecture that uses a separation kernel to create strongly isolated partitions on a single hardware platform, with controlled information flow between partitions. MILS components must satisfy the NEAT properties: Non-bypassable, Evaluatable, Always-invoked, and Tamperproof.

Most deployed CDS systems use the MSL approach -- physically or logically separated domains with guards or diodes mediating the transfers between them. True MLS systems exist (Oracle Label Security, PitBull) but are limited to specific use cases and require extreme trust in the operating system's Trusted Computing Base.

The [three types of CDS](three-types.md) map directly onto this distinction: access and transfer solutions typically use MSL, while multi-level solutions implement true MLS.
