# The Three Types of CDS

Every cross-domain solution on the market falls into one of three categories. The type determines the security argument, the certification path, the operational model, and the cost. Getting this taxonomy right is the first step in any CDS decision.

---

## Access Solutions

**Definition:** An access solution lets a user view and interact with information in a different security domain without that information leaving the domain.

**The analogy:** Think of a window into a secure room. You can see what's inside and even manipulate objects through the window, but nothing physically leaves the room. The information stays where it is -- only the user's view crosses the boundary.

**How it works:** A single user interacts with multiple security domains from one workstation, while strict separation prevents data leaking between domains at any layer. Typical implementations use thin clients that render a classified desktop via hardware-isolated pixel streaming, or KVM switches with security features that let a user switch between physically separate machines.

**Examples:** Multi-level workstations, thin-client access solutions (like Everfox Trusted Thin Client), hardware-isolated remote browsing (SAVI/Garrison Technology).

**Key concern:** Data spills -- preventing classified information from leaking between the domains that the user can access. Clipboard controls, screen capture prevention, and peripheral isolation are critical.

---

## Transfer Solutions

**Definition:** A transfer solution moves data between security domains at different classification levels.

**The analogy:** Think of a secure airlock between two rooms at different pressure levels. Items placed in the airlock are inspected, decontaminated, and only then passed through to the other side. The two rooms never share atmosphere directly.

**How it works:** Data physically crosses the boundary, passing through a pipeline of [security treatments](how-it-works/treatments.md) -- [protocol break](how-it-works/protocol-break.md), format verification, content inspection, CDR, label checking, and potentially human review. The CDS enforces [information flow policy](how-it-works/security-enforcement.md) based on the data's classification and the destination domain's clearance.

Transfer solutions come in two flavours:

**Unidirectional (data diodes):** Hardware-enforced one-way data flow. The physics of a fibre-optic transmitter with no receiver (or vice versa) makes reverse flow impossible. Data diodes have zero reported successful cyber attacks -- ever. They are the highest-assurance CDS type.

**Bidirectional (guards):** Content-inspecting systems that handle data flow in both directions. A guard terminates the inbound protocol, inspects the content, and reconstructs a new outbound session. Guards are more complex than diodes because they must simultaneously prevent data exfiltration (high-to-low) and block malicious imports (low-to-high).

**Examples:** Owl Cyber Defense data diodes, Nexor Sentinel email guard, INFODAS SDoT Security Gateway, Waterfall Security unidirectional gateways, Everfox High Speed Guard.

---

## Multi-Level Solutions

**Definition:** A multi-level solution (MLS) processes information from multiple classification levels simultaneously on a single platform, using mandatory access control and security labels to enforce isolation.

**The analogy:** Think of a filing cabinet where every document has a colour-coded label, and the cabinet itself enforces who can open which drawers. All the documents are physically in one place, but the enforcement mechanism ensures each person can only access what their clearance permits.

**How it works:** All data resides in a single system. Every data object is tagged with a [security label](how-it-works/security-enforcement.md). Every user has a clearance level. The operating system enforces Bell-LaPadula (confidentiality) and optionally Biba (integrity) rules on every access -- no subject can read above its clearance or write below its clearance. The entire security enforcement mechanism (the Trusted Computing Base) must be trustworthy because all data is physically accessible to the OS.

MLS effectively combines access and transfer capabilities in a single system.

**Examples:** Oracle Label Security (database-level MLS), PitBull (MLS operating system based on RHEL), XTS-400 (evaluated at EAL 5+).

**Key concern:** The Trusted Computing Base must be trusted absolutely, creating a large verification burden. True MLS systems have proven difficult to build to the required assurance level and are generally limited to specific use cases like databases and messaging.

---

## Comparison

| | Access Solutions | Transfer Solutions (Diode) | Transfer Solutions (Guard) | Multi-Level Solutions |
|---|---|---|---|---|
| **Data movement** | None -- user views remotely | One direction only | Both directions | All directions within one system |
| **Security assurance** | High (hardware isolation) | Highest (physics-enforced) | High (protocol break + inspection) | Variable (depends on TCB trust) |
| **Complexity** | Moderate | Low | High | Very high |
| **Typical cost** | Moderate | Moderate--High | High | Very high |
| **Certification path** | Moderate | Straightforward (simple security argument) | Complex (bidirectional threat model) | Most complex |
| **Typical use cases** | Multi-level workstations, remote browsing, access to reference databases | SCADA monitoring, intelligence publication, log export, sensor data | Email exchange, web browsing across domains, file transfer with review | MLS databases, label-based messaging |
| **Latency** | Low (real-time rendering) | Low (streaming) to moderate (store-and-forward) | Moderate to high (inspection pipeline) | Low (local access) |

---

## Which Type Do I Need?

The right CDS type depends on what you're trying to do. Work through these questions:

**Do users need to interact with data in another domain, or does data need to move between domains?**

- If users need to *see and use* data without moving it → **Access Solution**
- If data needs to physically *move* → Transfer or Multi-Level Solution

**Does data need to flow in one direction or both?**

- One direction only (e.g., exporting monitoring data, importing threat feeds) → **Transfer Solution (Data Diode)**
- Both directions (e.g., email exchange, web browsing, database queries) → **Transfer Solution (Guard)** or **Multi-Level Solution**

**How many security domains are involved?**

- Two domains → **Transfer Solution** (diode or guard)
- Three or more domains with complex policy → Consider **Multi-Level Solution** or a hub-and-spoke architecture with multiple guards

**What classification levels are involved?**

- SECRET and above → hardware-based transfer solutions are strongly preferred and often mandatory
- RESTRICTED / CUI → software-based or cloud CDS may be appropriate, with lower cost
- TOP SECRET → hardware transfer solutions with the highest assurance; MLS only with evaluated TCB

!!! info "Start with a diode"

    Data diodes have the strongest security track record of any CDS type -- zero reported breaches, ever. If your use case can work with unidirectional flow, a data diode should be your default starting point. Add guard capability only where bidirectional flow is genuinely required.

For more on how each type handles data flow, see [Data Flow Models](how-it-works/data-flow.md). For the security mechanisms they all share, see [Protocol Break](how-it-works/protocol-break.md), [Treatments](how-it-works/treatments.md), and [Security Enforcement](how-it-works/security-enforcement.md).
