# Cross-Domain Solutions

Everything you need to understand cross-domain solutions -- what they are, how they work, and how to choose the right one.

---

??? question "I'm new to CDS -- where do I start?"

    Start here:

    1. **[What is a CDS?](learn/what-is-a-cds.md)** -- the definition, the history, and why it's not a firewall
    2. **[Why CDS Matters](learn/why-cds-matters.md)** -- the information sharing dilemma and real-world contexts
    3. **[The Three Types of CDS](learn/three-types.md)** -- access, transfer, and multi-level solutions
    4. **[How CDS Works](learn/how-it-works/index.md)** -- data flows, protocol break, treatments, and security enforcement

    That sequence will take you from zero to a solid working understanding.

??? question "I'm an architect evaluating CDS options"

    You probably already know what CDS is. Jump to the technical detail:

    1. **[The Three Types](learn/three-types.md)** -- make sure you have the taxonomy straight
    2. **[Protocol Break](learn/how-it-works/protocol-break.md)** -- the foundational security mechanism
    3. **[Treatments and Transforms](learn/how-it-works/treatments.md)** -- the 12+ treatment types in a CDS pipeline
    4. **[Security Enforcement](learn/how-it-works/security-enforcement.md)** -- labels, access control, audit

??? question "I'm in policy or compliance"

    Focus on the governance and standards material:

    1. **[What is a CDS?](learn/what-is-a-cds.md)** -- get the formal definitions (NIST, NCSC, NSA, ASD)
    2. **[Security Enforcement](learn/how-it-works/security-enforcement.md)** -- how security labels and access control work
    3. **[Why CDS Matters](learn/why-cds-matters.md)** -- the stakes and the regulatory context

??? question "I want to build a software CDS"

    Understand the fundamentals first, then go deep on the mechanisms your software must implement:

    1. **[Protocol Break](learn/how-it-works/protocol-break.md)** -- if you skip this, you're not building a CDS
    2. **[Treatments and Transforms](learn/how-it-works/treatments.md)** -- the treatment pipeline your software needs
    3. **[Data Flow Models](learn/how-it-works/data-flow.md)** -- unidirectional, bidirectional, and multi-level flows
    4. **[Security Enforcement](learn/how-it-works/security-enforcement.md)** -- labels, MAC, ABAC, and audit

---

## Key Concepts

Cross-Domain Solution (CDS)
:   A controlled interface that enables the manual or automatic transfer of information between security domains at different classification levels, while enforcing information flow policy. Not a firewall -- a CDS inspects and transforms the *content*, not just the network packets.

Security Domain
:   A collection of systems, networks, and users that share the same security policies and classification level. For example, a SECRET network is one security domain; an UNCLASSIFIED network is another.

Protocol Break
:   The deliberate termination of one network connection, extraction of the data payload, and creation of an entirely new connection on the other side of the security boundary. The foundational mechanism that makes a CDS a CDS. See [Protocol Break](learn/how-it-works/protocol-break.md).

Security Label
:   Machine-readable metadata attached to data that encodes its classification, handling caveats, and release constraints. NATO STANAGs 4774 and 4778 are the closest thing to an international standard. See [Security Enforcement](learn/how-it-works/security-enforcement.md).

Treatment
:   Any processing step applied to data as it crosses a security boundary -- content inspection, format verification, CDR, dirty word search, label checking, and more. See [Treatments and Transforms](learn/how-it-works/treatments.md).

Data Diode
:   A hardware device that enforces physically unidirectional data flow. Data can travel in one direction only -- the physics makes reverse flow impossible. Zero reported successful cyber attacks, ever.

Guard
:   A bidirectional CDS that inspects, filters, and transforms content as it crosses a security boundary. Operates as a full application-layer proxy with complete protocol break.
