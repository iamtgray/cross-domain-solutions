# Data Diodes

A data diode is a network device that allows data to travel in only one direction. The one-way property is enforced by the physical hardware design -- not by software, configuration, or firewall rules -- making it impossible for data to flow in the reverse direction regardless of what is running on either connected network.

Think of it like a one-way valve in a water pipe. Water flows through in one direction, and no amount of pressure from the other side can push it back. The physics of the device simply does not permit it.

There are no reported cases of a data diode being bypassed or exploited to enable two-way traffic. That is a remarkable track record for any security technology.

## Data diode vs unidirectional gateway

These terms are often used interchangeably, but there is a useful distinction:

**Data diode**
:   The hardware component itself -- the optical or electrical device that enforces one-way transfer.

**Unidirectional gateway**
:   The complete system: the data diode hardware plus software agents (proxies) on either side that handle protocol conversion, file transfer, database replication, and other application-level functions.

Modern unidirectional gateways combine hardware-enforced physical unidirectionality with software that replicates databases and emulates protocol servers. The hardware provides the security guarantee; the software provides the usability.

## The physics of one-way transfer

### Optical data diodes (fibre optic)

The most common mechanism uses fibre optic technology. A standard fibre link has two paths -- one for sending, one for receiving. A data diode modifies this by physically removing or disconnecting the return path.

In a typical optical data diode:

- A **transmitter** (LED or laser diode) on the sending side converts electrical signals to light pulses
- A **fibre optic path** carries the light in one direction
- A **receiver** (photodiode) on the receiving side converts light pulses back to electrical signals
- There is **no return path** -- no fibre, no transmitter on the receiving side, no receiver on the sending side

Some data diodes use a discrete LED-to-photodiode coupling rather than standard fibre transceivers. The Nexor Data Diode, for example, uses a light emitting diode and receiver pair to ensure data can only travel one way, with one half of the optical connection physically terminated.

Fibre optic diodes have inherent advantages. Unlike copper cables, fibre does not create electromagnetic fields that could leak data in the reverse direction. The absence of a return fibre is easily verified by physical inspection. And the technology supports high bandwidth -- modern implementations achieve 100 Gbps using standard commercial off-the-shelf transceivers.

### Electrical data diodes

Some data diodes use electrical mechanisms rather than optical ones.

**Electromagnetic induction** -- Siemens developed an industrial-grade data diode using electromagnetic induction and custom chip design for railway security. Data transfers via changing magnetic fields from one coil to another, with the physical design preventing reverse coupling. This achieved SIL 4 certification, the highest safety integrity level.

**Optical isolation with electrical interfaces** -- Some products present standard Ethernet interfaces (RJ-45) externally while using optical isolation internally. Fend data diodes use patented technology that physically transports data using light across two optically isolated circuits, combining optical security with Ethernet convenience.

### Software-enforced unidirectional transfer

German companies INFODAS and genua developed approaches using microkernel operating systems to enforce unidirectional data transfer in software. These are not hardware diodes in the strictest sense -- the one-way property is enforced by a formally verified microkernel rather than by physical hardware. They may achieve equivalent assurance through formal verification, and can offer higher speeds than purely hardware-based versions.

## The ACK problem

This is the fundamental challenge of running standard network protocols over a one-way link.

### What it is

When a sender transmits data using TCP, it expects an acknowledgment (ACK) from the receiver confirming receipt. Over a data diode, this ACK can never reach the sender. The consequences cascade:

- **TCP connections cannot be established** -- the three-way handshake cannot complete
- **Reliable delivery cannot be confirmed** -- the sender never knows if data arrived
- **Flow control breaks** -- the sender has no feedback on congestion or receiver capacity
- **Application protocols fail** -- anything expecting a response (HTTP, database queries) cannot function

### Solutions

**Protocol proxy architecture** -- The most common solution. Proxy agents on both sides of the diode handle the protocol conversion:

```
[Source Network] <--TCP--> [Sending Proxy] --UDP--> [DIODE] --UDP--> [Receiving Proxy] <--TCP--> [Destination]
```

The sending proxy accepts standard TCP connections from source applications, provides ACKs back locally, converts data to sequenced UDP packets with forward error correction, and sends them through the diode. The receiving proxy reassembles the data and presents it to destination applications using standard protocols.

**Forward error correction (FEC)** -- Rather than relying on retransmission (which requires ACKs), data diodes add redundant data so the receiver can reconstruct lost packets without requesting retransmission. This is the same principle used in satellite communications, where a return channel is impractical.

**The Network Pump** -- Developed by the US Naval Research Laboratory as a compromise. It allows a very narrow, rate-limited, noise-injected reverse channel specifically for ACK packets. The bandwidth of this reverse channel is tightly controlled to minimise covert channel capacity. This is not a pure data diode -- it sacrifices some one-way assurance for improved reliability.

**Dual-diode bidirectional** -- ST Engineering's approach uses separate diodes in each direction, each providing one-way assurance, combined with software proxies that coordinate bidirectional protocol flow. This enables real-time bidirectional HTTP(S) transactions whilst maintaining the physics-based guarantee in each direction.

??? question "Which protocols work natively over a data diode?"

    **UDP-based protocols work naturally** because UDP is fire-and-forget -- no acknowledgments needed. This includes UDP multicast, RTP/RTSP streaming, syslog, and SNMP traps.

    **TCP-based protocols require proxy conversion.** This includes HTTP/HTTPS (full proxy on each side), SMTP email (store-and-forward proxy), FTP/SFTP (file transfer proxy), SMB/CIFS (file share replication), JDBC/ODBC (database replication), and OPC UA/DA (industrial protocol bridging).

    The key insight: the diode hardware handles the physics; the proxy software handles the protocol complexity. Modern gateways can transfer multiple protocols and data types simultaneously.

## What you can send through a data diode

### Files

The sending agent monitors a designated directory, splits files into sequenced UDP packets, transmits through the diode, and the receiving agent reassembles them. Straightforward and well-supported across all major vendors.

### Database replication

Database changes are captured on the source side (via change data capture, transaction logs, or periodic queries), serialised, transmitted through the diode, and applied to a replica database on the receiving side. The hardware enforces unidirectionality whilst software replicates databases and emulates protocol servers.

### Video and streaming

Video is a natural fit for data diodes because it is inherently unidirectional, uses UDP-based streaming protocols, and tolerates minor packet loss (brief visual artefacts rather than fatal errors). Owl Cyber Defense's XD Vision streams HD voice, video teleconferencing, and full motion video supporting MPEG-2, MPEG-4, H.264, and SIP protocols across 2 to 20+ networks simultaneously.

### SCADA and industrial control system data

One of the most significant use cases. The typical deployment:

```
[OT/SCADA Network] --> [DIODE] --> [IT/Corporate Network] --> [Cloud/Analytics]
```

Operational data (telemetry, sensor readings, historian data) flows out of the protected OT network for monitoring and analytics, whilst it is physically impossible for attacks -- ransomware, malicious commands -- to reach the control system from the IT network. Key industrial protocols include Modbus, OPC UA/DA, DNP3, and IEC 61850.

### Email

Email transfer uses store-and-forward: the sending agent accepts emails via SMTP, converts to one-way transmission format, and the receiving agent delivers via SMTP to the destination mail server. For email with full content inspection and security labelling, a [guard](guards.md) is typically more appropriate.

## Major vendors

| Vendor | Headquarters | Key Products | Throughput | Top Certification | Differentiator |
|--------|-------------|-------------|-----------|------------------|---------------|
| **Owl Cyber Defense** | USA | Talon One, XD Bridge ST | 1G--100G | NCDSMO baseline, NIAP | Broadest product range; 25+ years in CDS |
| **Waterfall Security** | Israel | Unidirectional Gateway | Industrial | NERC CIP, IEC 62443 | OT/SCADA focus; 1,000+ deployments |
| **BAE Systems** | UK/USA | XTS Diode, Data Diode Solution | 10 Gbps | CC EAL7+, first RTB-compliant | First NSA RTB-compliant OWT; STOP OS |
| **Nexor** | UK | Nexor Data Diode, GuarDiode | 1G--10G | CC EAL7+ | LED-to-photodiode; no IP address; sealed tamper-evident |
| **Sentyron** | Netherlands | DataDiode Ruggedised, Andean | 1G--10G | CC EAL7+, NATO TOP SECRET | First EAL7+ data diode (2015); 20+ countries |
| **Advenica** | Sweden | DD1G, DD1000A, DDSFX-10G | 1G--10G | Common Criteria (2026) | SFP form factor; Swedish national security trusted |
| **Fend** | USA | Optical isolation diodes | Industrial | ICS-focused | Modbus gateway with optical isolation; 24 countries |
| **INFODAS** | Germany | SDoT CDS | Variable | BSI approved to SECRET | Microkernel-based; made in Germany |
| **Siemens** | Germany | EM induction diode | Railway | SIL 4 | Electromagnetic induction; railway safety |

!!! info "The certification landscape"

    Data diode certifications vary by country and target market. The highest Common Criteria level achieved is **EAL7+** (formally verified design), held by Nexor, Sentyron, and BAE Systems. In the US, products must pass **Lab-Based Security Assessment (LBSA)** and meet **Raise the Bar (RTB)** requirements to be listed on the NCDSMO baseline. For industrial deployments, **IEC 62443**, **NERC CIP**, and **NIS2** compliance are the relevant standards. See [certification and evaluation](../standards/certification.md) for the full picture.

## Use cases

### Nuclear power

The US Nuclear Regulatory Commission mandates data diodes for nuclear power facilities. They allow safety system data to be monitored externally whilst making it physically impossible for cyber attacks to reach reactor control systems.

### Military and intelligence

The original use case. Applications include intelligence sharing between classification levels, streaming ISR data, coalition operations between allied nations, and tactical deployments on ships, aircraft, and vehicles. BAE Systems products serve the DoD, Intelligence Community, Coalition Partners, and foreign militaries globally.

### SCADA and industrial control systems

The fastest-growing segment. Power generation, oil and gas pipelines, water treatment, manufacturing, and railway systems all use data diodes to protect control systems whilst enabling monitoring. France's ANSSI mandated data diodes (not firewalls) for class 3 networks including railway systems in 2013.

### Financial services

Protecting trading systems from external attack whilst publishing market data. Secure data feeds between trading floors and risk management. Compliance data export.

### Election systems

Data diodes allow election results to be published to the public internet whilst ensuring no external attack can reach the vote counting systems.

## Limitations

Data diodes are not a universal solution. They have real constraints that determine where they are appropriate.

**Unidirectional only** -- Any use case requiring bidirectional communication (remote access, interactive queries, web browsing) cannot be served by a data diode alone. For bidirectional needs, see [guards](guards.md).

**No delivery confirmation** -- The sender cannot confirm that data was received. FEC mitigates packet loss, but for use cases requiring guaranteed delivery, additional application-level mechanisms (checksums, out-of-band confirmation) may be needed.

**Protocol complexity** -- Making bidirectional protocols work over a unidirectional link requires proxy software for each protocol. Custom or proprietary protocols may not be supported.

**Cost** -- Certified data diode solutions carry significant costs: hardware acquisition, protocol proxy licensing, integration, and per-protocol licensing in some cases. Though lower-cost solutions are now available, particularly for industrial use.

**Covert channels in the permitted direction** -- Whilst a pure data diode eliminates all reverse-direction covert channels, an attacker on the source network could theoretically encode information in timing patterns of permitted data. This is a concern for high-to-low deployments protecting against exfiltration.

??? question "When should I use a data diode vs a guard?"

    Use a **data diode** when data only needs to flow in one direction and you need the highest possible assurance. The physical guarantee of no reverse flow is the priority. Typical scenarios: exporting telemetry from OT networks, publishing data from classified to unclassified networks, streaming video from secure environments.

    Use a **[guard](guards.md)** when bidirectional communication is required (email exchange, web access, interactive queries) and content must be inspected, filtered, or sanitised. You trade some assurance for functionality.

    Use a **hybrid** approach when you need the best of both: separate diodes for each direction with content inspection in between. See the [hardware vs software analysis](../software/hardware-vs-software.md) for decision criteria.

??? question "Can a data diode replace an air gap?"

    Data diodes were originally developed as automated alternatives to air gaps. They provide continuous, real-time data transfer whilst maintaining equivalent or near-equivalent security assurance. The key advantage: no more sneakernet (physically carrying removable media between networks), which is both slow and introduces its own security risks (lost media, malware on USB drives). Most national security frameworks accept data diodes as a controlled replacement for air gaps where one-way data flow is sufficient.
