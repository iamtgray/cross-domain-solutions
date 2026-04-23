# The Three Types of CDS

Every cross-domain solution on the market falls into one of three categories. The type determines the security argument, the certification path, the operational model, and the cost. Getting this taxonomy right is the first step in any CDS decision.

---

## Access Solutions

**Definition:** An access solution lets a user view and interact with information in a different security domain without that information leaving the domain.

**The analogy:** You're sat in a council flat with a window into a bank vault. You can see the gold bars, read the serial numbers, maybe even use a robotic arm to move them around inside the vault -- but nothing comes through that window. The glass is one-way for physical objects; only light (pixels) crosses the boundary. If someone smashes the window from the flat side, they still can't get the gold out because the vault has its own walls.

That's how hardware-isolated remote access works. The classified data stays on the classified system. Your workstation just receives a video stream of pixels -- rendered remotely, streamed as an image. There's no file transfer, no clipboard sharing, no protocol that carries data back. The user sees and interacts with SECRET content from an UNCLASSIFIED terminal, but the SECRET data never touches the UNCLASSIFIED hardware.

**How it works:** A single user interacts with multiple security domains from one workstation, while strict separation prevents data leaking between domains at any layer. Typical implementations use thin clients that render a classified desktop via hardware-isolated pixel streaming, or KVM switches with security features that let a user switch between physically separate machines.

**Examples:** Multi-level workstations, thin-client access solutions (like Everfox Trusted Thin Client), hardware-isolated remote browsing (SAVI/Garrison Technology).

**Key concern:** Data spills -- preventing classified information from leaking between the domains that the user can access. Clipboard controls, screen capture prevention, and peripheral isolation are critical.

---

## Transfer Solutions

**Definition:** A transfer solution moves data between security domains at different classification levels.

**The analogy:** Think of the airlock on the International Space Station. When a supply capsule docks, the astronauts don't just open both doors. They seal the outer hatch, pressurise the airlock, check for leaks, scan the cargo manifest, inspect the contents, and only then open the inner hatch to bring supplies aboard. If something's wrong -- unexpected item, pressure mismatch, contamination risk -- the inner hatch stays shut and the cargo gets rejected back to the capsule.

A transfer CDS works the same way. Data arrives from one security domain, the CDS terminates the connection ([protocol break](how-it-works/protocol-break.md)), pulls out the data content, runs it through a pipeline of inspections and [treatments](how-it-works/treatments.md) -- format verification, content inspection, CDR, label checking, potentially human review -- and only then reconstructs a new connection to deliver it into the destination domain. The two domains never have a direct connection; the CDS is the airlock between them.

Transfer solutions come in two flavours:

### Unidirectional (data diodes)

Hardware-enforced one-way data flow. A fibre-optic cable with a transmitter on one end and a receiver on the other -- but no transmitter on the receiving side and no receiver on the sending side. Physics makes reverse flow impossible. It's not a software rule that could have a bug; it's the absence of hardware that could carry a signal back.

Data diodes have zero reported successful cyber attacks -- ever. They're the highest-assurance CDS type.

**The analogy:** A newspaper printing press. Information flows from the newsroom to the printed page, but there's no mechanism for the printed page to send information back to the newsroom. The physics of ink-on-paper is one-way. Data diodes work on the same principle -- the physics of the connection only allows transmission in one direction.

### Bidirectional (guards)

Content-inspecting systems that handle data flow in both directions. A guard terminates the inbound protocol, inspects the content, and reconstructs a new outbound session. Guards are more complex than diodes because they must simultaneously prevent data exfiltration (high-to-low) and block malicious imports (low-to-high).

**The analogy:** An airport security checkpoint, but in both directions. Passengers going airside get screened for prohibited items. But everything coming back from airside -- returned bags, cargo, equipment -- also gets screened before it enters the public terminal. The checkpoint understands what's allowed in each direction, and the rules are different depending on which way you're going. Guards work the same way: the export path worries about classified data leaking out; the import path worries about malware and hostile content getting in.

**Examples:** Owl Cyber Defense data diodes, Nexor Sentinel email guard, INFODAS SDoT Security Gateway, Waterfall Security unidirectional gateways, Everfox High Speed Guard.

---

## Multi-Level Solutions

**Definition:** A multi-level solution (MLS) processes information from multiple classification levels simultaneously on a single platform, using mandatory access control and security labels to enforce isolation.

**The analogy:** Imagine a single office building where every floor handles a different classification level -- TOP SECRET on the top floor, SECRET below it, RESTRICTED below that, and UNCLASSIFIED in the lobby. Everyone works in the same building, but the lifts are smart: they check your security badge before the doors open, and they'll only take you to floors your clearance allows. Every document in the building has a classification stamp, and the building management system tracks what goes where. If someone on the SECRET floor tries to email a document to the UNCLASSIFIED lobby, the system blocks it automatically.

The building is the single platform. The smart lifts and the badge checks are the mandatory access controls. The classification stamps on every document are the security labels. And the building management system that enforces all of this is the Trusted Computing Base -- the core software that everything depends on being correct.

That's the MLS vision: one system, multiple levels, the software keeps them apart. It's elegant in theory. In practice, it's extraordinarily hard to build a "building management system" that you can mathematically prove will never let someone onto the wrong floor -- especially when attackers are actively trying to find side doors, ventilation shafts, and timing tricks to bypass the lifts.

For a practical example of what multi-level security looks like when applied to data rather than systems, see [Data-Centric Security](https://datacentricsecurity.org/) -- which covers how security labels, attribute-based access control, and encryption can be embedded directly into data so that protection travels with it regardless of where it's stored or processed. The [cross-domain automated sanitisation scenario](https://datacentricsecurity.org/scenarios/04-cross-domain-automated-sanitisation/) is particularly relevant, covering the five-phase process for high-to-low transfer with classification determination.

**How it works:** All data resides in a single system. Every data object is tagged with a [security label](how-it-works/security-enforcement.md). Every user has a clearance level. The operating system enforces access rules on every operation -- no exceptions, no overrides, no "just this once." The entire Trusted Computing Base must be trustworthy because all data is physically accessible to the OS.

MLS effectively combines access and transfer capabilities in a single system.

**Examples:** Oracle Label Security (database-level MLS), PitBull (MLS operating system based on RHEL), XTS-400 (evaluated at EAL 5+).

**Key concern:** The Trusted Computing Base must be trusted absolutely, creating a verification burden that has historically proven very difficult to meet. See [MLS, MSL, and MILS](mls-msl-mils.md) for the full story of why true MLS mostly failed and what's replacing it.

---

## The security models behind CDS

Every CDS -- regardless of type -- is ultimately enforcing rules that trace back to two formal security models from the 1970s. These aren't just academic curiosities; they're the theoretical foundation that every guard, diode, and MLS system is built on. Understanding them makes the rest of CDS design click into place.

### Bell-LaPadula: protecting confidentiality

In 1973, David Elliott Bell and Leonard J. LaPadula -- working at the MITRE Corporation under guidance from Roger Schell -- formalised the US Department of Defense's multilevel security policy as a mathematical model. The problem they were solving: how do you prove that a computer system will never leak classified information to someone who shouldn't see it?

Their model defines two mandatory rules:

**Simple Security Property ("no read up")**
:   A subject (user or process) at a given security level cannot read data classified above that level. A SECRET-cleared analyst cannot read TOP SECRET documents.

**Star Property ("no write down")**
:   A subject at a given security level cannot write data to a lower classification level. A TOP SECRET process cannot send data to a SECRET system.

The model is sometimes summarised as **"read down, write up"** -- you can read at or below your level, and write at or above your level. If every single state transition in the system preserves these two properties, Bell and LaPadula proved mathematically that confidentiality is maintained.

!!! info "Why this matters for CDS"

    Every transfer CDS enforces Bell-LaPadula in practice. A data diode exporting from HIGH to LOW is implementing a controlled exception to the "no write down" rule -- the diode allows data to flow downward, but only after inspection and treatment. A guard that blocks an unauthorised export is enforcing the star property directly. The entire concept of "release authority" -- who decides what classified data can be released to a lower domain -- exists because Bell-LaPadula says the default answer is "no."

**Source:** Bell, D.E. and LaPadula, L.J. (1973), "Secure Computer Systems: Mathematical Foundations," MITRE Corporation. Bell reflected on the model's legacy in "Looking Back at the Bell-La Padula Model" at ACSAC 2005.

### Biba: protecting integrity

Four years later, Kenneth J. Biba addressed the other half of the problem. Bell-LaPadula stops secrets from leaking down, but it says nothing about preventing contamination flowing up. What if someone injects malicious data from an untrusted network into a trusted classified system?

Biba's 1977 model is the direct inverse of Bell-LaPadula, focused on **data integrity** rather than confidentiality:

**Simple Integrity Property ("no read down")**
:   A subject at a given integrity level must not read data from a lower integrity level. A trusted system should not consume untrusted input without sanitisation.

**Star Integrity Property ("no write up")**
:   A subject at a given integrity level must not write data to a higher integrity level. An untrusted process should not be able to modify trusted data.

Where Bell-LaPadula is summarised as "read down, write up," Biba is **"read up, write down"** -- the exact mirror.

!!! info "Why this matters for CDS"

    Biba explains why the **import path** of a CDS is just as critical as the export path -- and why [CDR (Content Disarm and Reconstruction)](how-it-works/treatments.md) exists. When you import data from a low-trust network into a high-trust classified system, you're violating Biba's integrity rules unless you sanitise that data first. CDR -- stripping active content, rebuilding files to specification, removing anything that doesn't comply with the expected format -- is essentially a Biba integrity mechanism. It ensures untrusted content can't contaminate trusted systems.

    Data diodes on the import path (low-to-high) are enforcing Biba: they allow data to flow upward into the classified domain, but the proxy agents and content filters on the receiving side sanitise everything to maintain the integrity of the higher domain.

**Source:** Biba, K.J. (1977), "Integrity Considerations for Secure Computer Systems," MITRE Corporation (ESD-TR-76-372).

### How the two models work together

In practice, a CDS must enforce both:

| Direction | Model | Concern | CDS mechanism |
|-----------|-------|---------|--------------|
| **Export** (high → low) | Bell-LaPadula | Classified data leaking to lower domains | Release authority, dirty word search, label checking, redaction, human review |
| **Import** (low → high) | Biba | Malicious or untrusted data contaminating trusted systems | CDR, format verification, malware scanning, schema validation |

A data diode enforces one model perfectly through physics: an export diode enforces Bell-LaPadula (controlled downward flow); an import diode enforces Biba (controlled upward flow). A bidirectional guard must enforce both simultaneously -- which is why guards are more complex and harder to certify than diodes.

---

## Choosing a CDS type

``` mermaid
flowchart TD
    A[What do you need?] --> B{Does data need to<br/>move between domains?}
    B -->|No -- users just need<br/>to see and interact| C[Access Solution]
    B -->|Yes -- data physically<br/>crosses the boundary| D{What direction<br/>does data flow?}
    D -->|One way only| E[Data Diode]
    D -->|Both directions| F{How many security<br/>domains?}
    F -->|Two| G[Bidirectional Guard]
    F -->|Three or more with<br/>complex policy| H{Single platform<br/>acceptable?}
    H -->|Yes -- and you can certify<br/>the Trusted Computing Base| I[Multi-Level Solution]
    H -->|No -- or TCB certification<br/>is impractical| J[Hub-and-spoke<br/>with multiple guards]

    style C fill:#4a148c,color:#fff
    style E fill:#4a148c,color:#fff
    style G fill:#4a148c,color:#fff
    style I fill:#4a148c,color:#fff
    style J fill:#4a148c,color:#fff
```

!!! info "Start with a diode"

    Data diodes have the strongest security track record of any CDS type -- zero reported breaches, ever. If your use case can work with unidirectional flow, a data diode should be your default starting point. Add guard capability only where bidirectional flow is genuinely required.

For more on how each type handles data flow, see [Data Flow Models](how-it-works/data-flow.md). For the security mechanisms they all share, see [Protocol Break](how-it-works/protocol-break.md), [Treatments](how-it-works/treatments.md), and [Security Enforcement](how-it-works/security-enforcement.md). For the full story on why most systems use MSL rather than true MLS, see [MLS, MSL, and MILS](mls-msl-mils.md).
