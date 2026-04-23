# Protocol Break

Protocol break is the foundational security mechanism in a cross-domain solution. If you understand one thing about how CDS works, it should be this.

---

## What Protocol Break Is

A protocol break is the deliberate termination of one network connection, extraction of the data payload, and initiation of a completely new connection on the other side of the security boundary.

The NCSC defines it plainly: a protocol break "will terminate one transmission path, extract the relevant information, and use this to initiate a new transmission path."

More specifically: "A protocol break will terminate the network connection, and the application protocol. The payload will then be passed via a simplified protocol."

The source and destination systems never have a direct network connection. The CDS sits between them, receiving data on one side, tearing apart the protocol, inspecting the content, and rebuilding everything from scratch on the other side.

!!! warning "If your CDS doesn't do protocol break, it's not a CDS"

    Protocol break is not optional. It is not an advanced feature. It is the architectural characteristic that distinguishes a genuine cross-domain solution from a proxy, a filter, or a firewall with deep packet inspection.

    If a product passes network packets through -- even with inspection -- it is not performing protocol break. The original protocol headers, session state, and network-layer information must be completely discarded and rebuilt. This is the litmus test.

---

## Why It Matters

Protocol break prevents an entire class of attacks: those that exploit vulnerabilities in network protocol implementations.

### Malformed protocol messages cannot propagate

The protocol break receiver must be "robustly designed" with the critical goal that "malformed messages in the original protocol cannot exploit vulnerabilities." Because the original protocol is terminated at the CDS boundary, any exploit crafted into the protocol headers, handshake, or session negotiation is discarded. Only the extracted data payload crosses.

### Attack surface is minimised

The simplified transport protocol used across the boundary has far fewer features -- and therefore far fewer potential vulnerabilities -- than a full application protocol like HTTP or SMTP. The Isode M-Guard, for example, uses GCXP (Guard Content eXchange Protocol) internally, which transmits XML messages via CBOR framing over TLS. That is dramatically simpler than full XMPP or SMTP.

### Command and control channels are severed

Even if malware reaches the destination network through the content payload, a unidirectional protocol break prevents it from communicating back to the attacker. The NCSC notes that "flow control does not stop a vulnerability being exploited within the destination system but it can make it difficult for an attacker to perform command and control."

### Defence in depth across the stack

Protocol break operates at the network and transport layers. [Content treatments](treatments.md) (CDR, format verification, dirty word search) operate at the application layer. Together they provide protection across the full protocol stack -- failure of one layer does not compromise the other.

---

## How it works

The architecture follows the NCSC's import pipeline pattern, placing complex processing on the untrusted side and simple verification on the trusted side:

``` mermaid
flowchart LR
    subgraph untrusted["Untrusted side"]
        direction TB
        A["1. Terminate inbound<br/>connection"] --> B["2. Extract data<br/>payload"]
        B --> C["3. Transform to<br/>simpler format"]
    end

    subgraph break["Boundary"]
        direction TB
        D["4. Pass via simplified<br/>protocol<br/>(hardware-enforced)"]
    end

    subgraph trusted["Trusted side"]
        direction TB
        E["5. Verify structure<br/>and semantics"] --> F["6. Reconstruct in<br/>clean protocol headers"]
    end

    C --> D --> E

    style untrusted fill:#b71c1c,color:#fff,stroke:#b71c1c
    style break fill:#4a148c,color:#fff,stroke:#4a148c
    style trusted fill:#1b5e20,color:#fff,stroke:#1b5e20
```

The original network session ends at step 1 -- no packets pass through. The CDS strips all protocol headers, session state, and transport metadata (step 2), then converts complex data formats into simpler, verifiable forms (step 3). The NCSC places this transformation engine deliberately on the untrusted side because "we assume that the transformation engine could be compromised" -- the complex, potentially vulnerable processing happens where a compromise is least damaging.

The simplified payload crosses the boundary via a minimal internal transport (step 4), often hardware-enforced. On the trusted side, a verification engine checks structure and semantics (step 5) before wrapping approved data in "known good protocol headers" and delivering it via a completely new connection (step 6).

!!! info "The key insight"

    The architecture is deliberately asymmetric. Complex, dangerous processing happens on the untrusted side. Simple, verifiable checking happens on the trusted side. The hardware-enforced break between them means that even if the transformation engine is compromised, it can't contaminate the trusted side.

---

## Protocol-Specific Examples

### HTTP

The CDS terminates the HTTP connection on the untrusted side, extracts the request or response body, and reconstructs a new HTTP transaction on the trusted side. Because HTTP is inherently bidirectional, the CDS must "correlate requests and responses" to ensure "a response cannot pass without a corresponding request of the correct type."

### SMTP (Email)

The CDS terminates the SMTP session, extracts the message envelope (sender, recipients) and body (MIME content), processes each component through the [treatment pipeline](treatments.md), and reconstructs a new SMTP delivery on the other side. Email is the most common bidirectional CDS use case and the original guard application going back to the ACCAT Guard in the 1980s.

### XMPP (Chat and Messaging)

The CDS terminates the XMPP session, extracts the XML stanzas (including any [STANAG 4774 security labels](security-enforcement.md)), and passes validated content across the boundary via a simplified internal protocol before reconstructing a new XMPP session on the other side. Isode's M-Guard is the best-documented example -- it uses GCXP (Guard Content eXchange Protocol), which is built from open standards (CBOR framing per RFC 8949 over TLS) but is itself a vendor specification rather than an independent standard. No open standard exists for the simplified boundary transport layer; the NCSC deliberately leaves this to the implementer. Guards are commonly deployed in pairs for bidirectional chat.

??? note "Open-source boundary transport"

    ANSSI (the French national cybersecurity agency) publishes [lidi](https://github.com/ANSSI-FR/lidi) -- an open-source tool (Rust, LGPL-3.0) for reliable unidirectional transfer across data diode links using RaptorQ forward error correction. It solves the transport layer problem but doesn't include content inspection or security label checking. It's the closest open-source building block for the simplified boundary protocol, though you'd still need to build the guard logic on top.

### Database

Database protocol break terminates the database wire protocol (ODBC, JDBC, or proprietary), extracts query results or update operations, validates them against schema and policy, and reconstructs new database operations on the trusted side. The NCSC highlights SQL queries as a case requiring request/response correlation to prevent unauthorised data export.

### File Transfer (FTP)

FTP protocol break terminates both the FTP control and data channels, extracts the file content, processes it through CDR and verification, and delivers the file via a new connection. Data diode products typically support this as a standard use case.

---

## Protocol Break and CDR: Defence in Depth

Protocol break handles the transport layer. [Content Disarm and Reconstruct (CDR)](treatments.md) handles the content layer. Together they provide layered protection across the full data transfer:

| Layer | Threat | Control |
|---|---|---|
| **Network / transport** | Protocol exploits, malformed packets | Protocol break + hardware-enforced flow |
| **Session** | Session hijacking, C&C channels | Session isolation, unidirectional flow |
| **Application format** | Malformed files, polyglot attacks | Format verification, schema validation |
| **Application content** | Embedded malware, active content | CDR, active content removal |
| **Semantic** | Covert channels, data exfiltration | Semantic verification, [label checks](security-enforcement.md) |

The NCSC captures this principle: "assurance is not gained at a single point, but through the pipeline."

Each control addresses a different attack vector. The failure of any single control does not compromise the entire boundary -- the remaining controls continue to provide protection. This is why a real CDS is fundamentally more secure than a device that only operates at one layer.

---

## Flow Separation

The NCSC requires that import and export flows are handled separately, with specific hardware enforcement:

- **Export flows** require "hardware enforced one way flow"
- **Import flows** need "hardware enforced one way flow inbound, and a protocol break"

There must also be "a clear separation between these externally exposed components, and internal components which have connectivity to more protected core network systems and services."

This separation ensures that a compromise of the external-facing protocol handling cannot provide a route into the protected core network.

For more on how data moves through these flows, see [Data Flow Models](data-flow.md). For the content-level treatments applied after protocol break, see [Treatments and Transforms](treatments.md). For the policy that governs what gets through, see [Security Enforcement](security-enforcement.md).
