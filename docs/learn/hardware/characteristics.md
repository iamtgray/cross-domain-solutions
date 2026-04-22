# Key Characteristics of Hardware CDS

What makes a cross-domain solution "hardware"? It is not just that a physical box sits in a rack. Hardware CDS products share a set of defining characteristics that collectively provide security assurance rooted in physics rather than code. These characteristics are why national security frameworks require hardware CDS at the highest classification boundaries.

## Physical separation

The foundational characteristic. Hardware CDS enforces domain boundaries through physical hardware design, not logical isolation. A [data diode](data-diodes.md) physically removes the return communication path. A hardware [guard](guards.md) uses physically separate processing subsystems for each security domain with a security-enforcing component mediating all transfers.

This distinction matters because logical separation (virtual machines, containers, access control lists) can be bypassed through software vulnerabilities, misconfiguration, or zero-day exploits. Physical separation cannot be bypassed by software attacks -- an attacker would need physical access to the hardware itself.

Hardware CDS evolved as an automated replacement for air gaps. The advantage: continuous, real-time data transfer with equivalent or near-equivalent assurance, without the sneakernet process of physically carrying removable media between networks.

## Hardware-enforced information flow control

The direction and nature of permitted data flows are determined by the physical design of the device. A data diode uses optical or electrical hardware that is physically incapable of transmitting data in the reverse direction. A hardware guard uses physically separate subsystems where the separation kernel controls inter-partition information flow.

This provides **deterministic security** -- the security properties are guaranteed by physics and hardware design, not by software correctness. Entire classes of attack become impossible:

- Remote access trojans need bidirectional connectivity -- data diodes prevent this physically
- Command-and-control communications require a return channel -- no return channel exists
- Software reconfiguration cannot change the hardware's physical properties
- The security posture does not deteriorate over time

Many hardware CDS devices implement the [Bell-LaPadula model](../how-it-works/security-enforcement.md) directly in hardware. Data diodes enforce the "write up" or "write down" direction as a physical property.

## Tamper resistance and tamper evidence

Hardware CDS products are designed to resist physical tampering and make any tampering attempts visible.

**Tamper-evident construction** -- Data diode hardware is typically sealed in tamper-evident enclosures that show visible signs if opened or modified. The Nexor Data Diode is a sealed, tamper-evident unit with no IP address or management interface, making it immune to remote exploitation.

**Tamper resistance in MILS** -- The MILS architecture defines tamperproofing as one of its four essential NEAT properties: the system controls modification rights to security monitor code, configuration, and data. See [guards](guards.md) for more on the MILS architecture.

**Supply chain protection** -- The INFODAS SDoT Secure Network Card is specifically designed to provide reliable protection against supply chain attacks through tamper-proof hardware.

## TEMPEST and emanation security

Hardware CDS products processing classified information must meet strict requirements for electromagnetic emanation security.

TEMPEST is a US NSA codename and NATO certification standard addressing the risk of intelligence leakage through unintentional electromagnetic, acoustic, or other emanations from electronic equipment. Compromising emanations are unintentional intelligence-bearing signals which, if intercepted and analysed, could disclose the information being processed.

### Protection levels

| Level | NATO Zone | Assumed Attacker Distance |
|-------|-----------|--------------------------|
| Level A (NSTISSAM Level I) | Zone 0 | ~1 metre |
| Level B (NSTISSAM Level II) | Zone 1 | ~20 metres |
| Level C (NSTISSAM Level III) | Zone 2 | ~100 metres |

Shielded enclosures require a minimum of 100 dB insertion loss from 1 kHz to 10 GHz. TEMPEST mandates strict **RED/BLACK separation** between circuits handling unencrypted classified data (RED) and circuits carrying encrypted or secured signals (BLACK). This directly affects hardware CDS physical design.

!!! warning "Certification sensitivity"

    TEMPEST certification applies to complete systems, not individual components. Changing even a single wire can invalidate the tests, and manufacturing requires careful quality control to ensure additional units are built exactly as the tested units were. This is why hardware CDS products have long procurement lead times and why modifications require re-testing.

## Form factors

Hardware CDS comes in several physical configurations depending on the deployment environment.

**Rack-mount (enterprise/data centre)** -- Standard 19-inch rack chassis, typically 1U or 2U. The Owl Talon One is available in 1U with RJ-45, SFP, and SFP+ connectivity. The BAE Systems XTS Enterprise Guard 7 supports up to 22 enclaves in an enterprise chassis.

**Ruggedised/tactical** -- For deployed military and field environments with extreme temperature, vibration, shock, and humidity. The BAE Systems XTS Tactical Guard 7 is designed for airframes and land/sea/air deployments with minimal SWaP (Size, Weight, and Power). The Sentyron DataDiode Ruggedised is built for harsh deployed environments.

**Embedded/edge** -- Small form factor for space-constrained deployments. The Nexor Edge Guard Family occupies a fraction of standard rack space. Fend data diodes are compact industrial devices for embedding directly in OT environments.

**SFP module** -- The Advenica DDSFX-10G is a data diode in a standard SFP transceiver form factor -- a unique approach that embeds diode functionality directly into existing network infrastructure.

## Performance

| Product | Throughput |
|---------|-----------|
| Owl Talon One | 1 Gbps to 100 Gbps |
| Owl XD Bridge ST | Up to 10 Gbps |
| BAE Systems XTS Diode | 10 Gbps |
| Nexor Data Diode | 1 Gbps and 10 Gbps |
| Sentyron DataDiode | 1 Gbps to 10 Gbps |
| Controlled Interfaces (demo) | 100 Gbps (COTS transceivers) |

Data diodes excel in high-throughput scenarios with low latency -- near wire speed for the diode hardware itself. Hardware guards add content inspection latency but remain deterministic, without the variability introduced by OS scheduling and virtualisation overhead in [software CDS](../software/index.md).

The effective throughput depends on the specific protocol being transferred, the processing power of proxy servers, and the complexity of any content inspection or format conversion.

## Reliability and availability

Mission-critical deployments require redundancy and failover. The BAE Systems Data Diode Solution includes a High Availability Library with automatic failover in less than 6 seconds, protection against single points of failure (server, diode, network switch, site), and simultaneous data replication across both diode links with no human intervention required.

The Nexor Guardian provides high-availability features that maintain uninterrupted data streams during equipment or power failures.

## Hardware root of trust

Hardware CDS products increasingly incorporate a hardware root of trust -- a tamper-resistant hardware component that provides a foundation for verifying device integrity.

**Secure boot** ensures that only authorised, unmodified firmware and software runs on the device. Modern data diodes include secure boot, certificate management, data integrity verification, FEC, and TLS encrypted communication.

**TPM and HSM support** provides cryptographic key storage and attestation capabilities. The separation kernel foundation provides the secure boot chain that verifies every component from power-on.

## Non-bypassability

A critical requirement: the security-enforcing mechanisms cannot be bypassed by users, administrators, software running on the device, or attackers who have compromised connected systems.

For data diodes, non-bypassability is a physical property. The Nexor Data Diode cannot be bypassed or altered by software, firmware, or configuration. It has no IP address or management interface, making it immune to remote exploitation.

For hardware guards, non-bypassability is enforced by the separation kernel and the MILS NEAT properties -- the security monitor cannot be circumvented because there is no alternative communication path at the hardware level.

## Supply chain security

Supply chain integrity is critical because a compromised device defeats the entire security architecture.

| Vendor | Supply Chain Claim |
|--------|-------------------|
| BAE Systems | Built by 5Eyes Security Cleared personnel with secure supply chains |
| Sentyron | Developed and managed in the Netherlands; complete control over hardware and software; government-recognised as strategically vital |
| Nexor | Designed, built, and supported in the UK; manufacturing through certified partners |
| Advenica | Designed, developed, and manufactured in Sweden; trusted by Swedish national security |
| Fend | Designed and manufactured in the USA |
| INFODAS | Made in Germany; BSI approved |

Many nations require that CDS products used for national security be manufactured domestically by cleared organisations. Each vendor emphasises their national credentials and controlled manufacturing environment. See [national approaches](../landscape/national-approaches.md) for how different countries handle this.

## Hardware vs software: the defining differences

| Characteristic | Hardware CDS | Software CDS |
|---------------|-------------|--------------|
| **Separation mechanism** | Physical (optical, electrical) | Logical (VM, container, ACL) |
| **Bypassability** | Physically impossible | Software vulnerabilities could bypass |
| **Zero-day vulnerability** | Immune (physical barrier) | Vulnerable |
| **Attack surface** | Minimal | Larger, exploitable |
| **Maintenance** | Low, near "set-and-forget" | High, ongoing updates required |
| **Configuration drift** | None (physical properties do not drift) | Possible through misconfiguration |
| **Compliance verification** | Simple physical proof | Complex ongoing checks |
| **Security over time** | Does not degrade | Can degrade without patching |
| **Scalability** | Limited by physical units | Horizontal scaling, cloud-native |
| **Flexibility** | Fixed function | New protocols and policies via software update |
| **Cost** | Higher upfront | Lower upfront, higher ongoing |
| **Cloud compatibility** | None | Native |

Data diodes make certain attacks physically impossible rather than relying on rule-based filtering. Their security posture does not deteriorate over time. But they cannot match the flexibility, scalability, or cost efficiency of [software CDS](../software/index.md).

For a comprehensive trade-off analysis across these dimensions to help with real decision-making, see [hardware vs software](../software/hardware-vs-software.md).

## High assurance vs medium assurance

What makes a hardware CDS "high assurance":

1. **Common Criteria EAL5+ evaluation** -- NSA experts participate directly at EAL5 and above
2. **Hardware-enforced separation** -- physical mechanisms prevent bypass
3. **Raise the Bar compliance** (US) -- architecture and OS requirements set by NSA
4. **Minimal TCB** -- small enough to be thoroughly verified
5. **Formal methods** -- semi-formal or formal verification at EAL6--7
6. **NEAT properties** -- non-bypassable, evaluatable, always-invoked, tamperproof

Medium-assurance CDS may use software-based separation, be evaluated at EAL3--4, handle lower classification levels (OFFICIAL to OFFICIAL-SENSITIVE), and use standard commercial operating systems with hardening. The distinction is determined by national security authorities based on the risk assessment for the specific deployment. See [certification and evaluation](../standards/certification.md) for more on how this is assessed.
