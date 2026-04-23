# Data Flow Models

How information moves across security boundaries is governed by formal models developed in the 1970s that remain the theoretical foundation of every CDS deployed today. This page explains those models in plain English and maps them to the real-world data flow patterns that CDS implement.

---

## The Two Foundational Models

### Bell-LaPadula: Protecting Confidentiality

The Bell-LaPadula model (1973) formalised two rules that underpin virtually all CDS confidentiality design:

**No read up** (the simple security property)
:   A subject cannot read data at a classification level above their clearance. A SECRET-cleared user cannot read TOP SECRET documents.

**No write down** (the star property)
:   A subject cannot write data to a classification level below their own. A TOP SECRET process cannot send data to a SECRET system.

In plain English: secrets flow upward, never downward. A CDS that moves data from a higher domain to a lower one (export/downgrade) is deliberately violating the star property -- so it needs a **trusted subject**, a mechanism or person formally authorised to make that release decision. This is exactly what [guards with human review](../three-types.md) or automated release policies provide.

### Biba: Protecting Integrity

The Biba model (1977) is the mirror image of Bell-LaPadula, protecting integrity rather than confidentiality:

**No read down**
:   A subject should not read data from a lower-integrity source (it might be contaminated).

**No write up**
:   A subject should not write to a higher-integrity destination (it might corrupt trusted data).

In plain English: trust flows downward, never upward. A trusted system should not accept unverified input from a less-trusted source without inspection. This is why CDS import pipelines focus heavily on [CDR, malware scanning, and format verification](treatments.md) -- they are implementing Biba's integrity protection.

!!! info "BLP and Biba are complementary"

    Bell-LaPadula protects secrets from leaking down. Biba protects trusted systems from contamination coming up. A well-designed CDS enforces both -- confidentiality controls on export flows and integrity controls on import flows. The NCSC makes this explicit: "Data import focuses on minimising malicious content. Data export concerns correct release of data."

---

## Unidirectional Flow

### High-to-Low (Export / Downgrade)

Data flows from a higher-classification domain to a lower-classification domain. The security concern is **confidentiality**: ensuring that released data does not contain information classified above the lower domain's ceiling.

This violates Bell-LaPadula's star property, so it requires a trusted release mechanism -- something or someone formally authorised to make the downgrade decision. At one end, a human reviewer examines each transfer, redacts classified content, and approves the release (highest assurance, lowest throughput). At the other, an automated guard runs the content through label checking, dirty word search, deep inspection, and CDR before a policy engine makes the release decision. A high-to-low data diode can also serve this purpose for data that's known releasable by design (e.g. publishing pre-approved SCADA monitoring data).

**Use cases:** publishing intelligence products to lower-classification consumers, releasing operational data to coalition partners, exporting SCADA monitoring data from control networks, publishing election results to public networks.

### Low-to-High (Import / Upgrade)

Data flows from a lower-classification domain to a higher-classification domain. The security concern is **integrity**: ensuring that imported data does not introduce malware, exploits, or corrupted content.

This aligns with Bell-LaPadula (writing up is permitted) but raises Biba integrity concerns -- you're allowing untrusted content into a trusted system. A low-to-high data diode is the highest-assurance approach: hardware-enforced one-way flow ingests data while physically preventing any exfiltration from the high side. An import guard provides automated sanitisation (format verification, [CDR](treatments.md), malware scanning, schema validation) before accepting the data. For less time-sensitive transfers, quarantine-and-scan approaches stage files in isolation and run them through multiple scanning engines before release.

**Use cases:** ingesting open-source intelligence into classified analytic systems, importing threat intelligence feeds, pulling software updates into air-gapped environments, collecting sensor data from field systems.

---

## Bidirectional Flow

Bidirectional flow allows information to cross a boundary in both directions -- typically through the same CDS infrastructure. This is the most common model for interactive use cases.

### Why Bidirectional is Harder

Bidirectional flow combines the security concerns of both directions simultaneously:

- **Confidentiality risk** from high-to-low flows (data leakage)
- **Integrity risk** from low-to-high flows (malware introduction)

The CDS must prevent data exfiltration and block malicious imports at the same time. This dual requirement makes bidirectional CDS significantly more complex to design, implement, and accredit than unidirectional solutions.

### Implementation Approaches

**Separate import and export paths:** The NCSC recommends independent components for import and export. A bidirectional CDS may use two separate unidirectional channels, each with its own [treatment pipeline](treatments.md) optimised for its specific threat model.

**Bidirectional guard:** A single [guard](../three-types.md) that handles both directions, applying different inspection pipelines depending on flow direction. Typically implemented as a dual-homed application-layer proxy with direction-aware [policy enforcement](security-enforcement.md).

**Paired data diodes:** Two data diodes in opposite directions, each providing hardware-enforced unidirectional flow. Software proxies on each side handle protocol reconstruction -- reassembling request/response pairs across the two one-way channels.

### Covert Channel Risk

Bidirectional flows create opportunities for covert channels. An attacker who has compromised systems on both sides could encode information in timing, packet sizes, or error rates of legitimate traffic. Even controlled backchannel mechanisms like the NRL Network Pump introduce a potential covert channel through artificially delayed acknowledgements. This is one reason [anti-covert-channel treatments](treatments.md) exist.

**Use cases:** email exchange between classification levels, web browsing from high-side workstations, cross-domain database queries, collaborative applications, SOA/web service interactions.

---

## Multi-Level / Multi-Domain Flow

Multi-level flow involves information moving between three or more security domains, potentially at different classification levels with different caveats. The CDS mediates a mesh of flows rather than a simple high/low pair.

### Architectural Approaches

**Hub-and-spoke (broker):** A central CDS broker connects to all domains. All cross-domain flows pass through the broker, which applies domain-pair-specific policies. Centralises policy and audit but creates a single point of failure.

**Chained guards:** Guards in series, each handling one boundary. For example: UNCLASSIFIED → guard → SECRET → guard → TOP SECRET. Conceptually simple but introduces latency for flows crossing multiple boundaries.

**MLS system:** A true [multi-level solution](../three-types.md) stores data at all classification levels in a single system, using labels and mandatory access control. Avoids multiple guard stages but requires extreme trust in the TCB.

**MILS platform:** A separation kernel creates isolated partitions for each domain, with security monitors mediating inter-partition flows. Different partitions may operate at different classification levels.

### Complexity

The number of potential flow paths grows combinatorially. For *n* domains, there are *n*(*n*-1) possible directed flows. Each may require a different security policy based on classification levels, caveats, releasability, data type, and operational context. Coalition environments with national SECRET networks for multiple nations plus a coalition network create exactly this kind of complex policy matrix.

---

## Store-and-Forward vs Real-Time

### Store-and-Forward

Data is received by the CDS, stored in a staging area, processed through the full [treatment pipeline](treatments.md), and forwarded to the destination once approved. Source and destination are not connected in real time.

- **Asynchronous** -- sender does not wait for delivery confirmation
- **Thorough inspection** -- the CDS has time for deep content inspection, multiple scanning engines, and human review
- **Latency** -- seconds for automated processing; hours or days if human review is required
- **Reliability** -- the CDS can retry failed deliveries and handle destination unavailability

Most traditional guard architectures operate in store-and-forward mode. This aligns naturally with email, file transfer, and intelligence dissemination.

### Real-Time / Streaming

Data flows through the CDS with minimal buffering, approximating real-time connectivity.

- **Low latency** -- milliseconds to low seconds
- **Limited inspection depth** -- the CDS cannot fully inspect data it has not fully received
- **Higher residual risk** -- less processing time means less thorough security treatment

Real-time flow is fundamentally in tension with thorough content inspection. Approaches to managing this include data-type-specific lightweight processing, parallel inspection stages, statistical sampling, and pre-approved channels for known-safe data types.

**Use cases:** video teleconferencing, real-time sensor feeds (radar, SIGINT), SCADA telemetry, cross-domain chat, streaming surveillance video.

---

## Interaction Patterns

### Publish-Subscribe

Sources publish to topics; consumers subscribe. The CDS mediates both publication and subscription, enforcing [security policy](security-enforcement.md) at the topic level and optionally filtering individual messages by their markings.

Pub-sub aligns naturally with [data diode](../three-types.md) architectures for high-to-low publication. It supports one-to-many distribution -- common in intelligence dissemination and situational awareness feeds.

### Request-Response

A client in one domain sends a request to a service in another; the CDS mediates both legs. This is inherently bidirectional, requiring inspection of the request (is it authorised?) and the response (does it contain information the requestor's domain may receive?).

Latency sensitivity limits inspection depth. Paired data diodes can support this pattern with software proxies handling protocol reconstruction.

### Streaming

Continuous data flow from source to consumer, mediated by the CDS. Streaming naturally aligns with unidirectional patterns -- most streaming use cases involve one-way flow (sensor data out, video feeds out, telemetry out). Modern optical data diodes support up to 100 Gbps, making high-bandwidth streaming feasible.

---

The NCSC's overarching guidance: use defence in depth and consider the CDS as an end-to-end system. "Include as much of the full end-to-end system involved in transferring information as practical."

For more on the mechanisms that enforce these flow models, see [Protocol Break](protocol-break.md), [Treatments](treatments.md), and [Security Enforcement](security-enforcement.md).
