# Hardware vs Software CDS

The choice between hardware and software cross-domain solutions is not binary. Most real-world deployments use hybrid architectures. This page examines the trade-offs across multiple dimensions to support informed decision-making.

## The comprehensive comparison

| Dimension | Hardware CDS | Software CDS |
|-----------|-------------|-------------|
| **Assurance basis** | Physics and electronics | Code correctness and formal verification |
| **Strongest guarantee** | Unidirectional flow ([data diode](../hardware/data-diodes.md)) | Formally verified kernel (seL4) |
| **Typical evaluation** | Physical inspection + TEMPEST | Common Criteria EAL 3--6 |
| **Side-channel resistance** | Strong (physical separation) | Weak (shared CPU, memory, caches) |
| **Covert channel resistance** | Strong | Requires active mitigation |
| **Scalability** | Limited by physical units; weeks to months | Horizontal; minutes in cloud |
| **Flexibility** | Fixed function | New protocols and policies via software update |
| **Cloud compatibility** | None | Native |
| **Deployment speed** | Weeks to months | Hours to days |
| **Upfront cost** | High | Lower |
| **Patch surface** | Small (firmware) | Large (OS, libraries, applications) |
| **Configuration drift** | None | Possible |

## Security assurance

[Hardware CDS](../hardware/characteristics.md) derives assurance from physical properties. A [data diode's](../hardware/data-diodes.md) laser transmitter physically cannot receive. FPGA-based [guards](../hardware/guards.md) implement content inspection in hardware with a frozen, auditable design. Hardware vulnerabilities exist (fault injection, electromagnetic emanation, supply chain tampering) but require physical access and sophisticated equipment to exploit.

Software CDS derives assurance from code correctness. seL4's formal verification mathematically proves the implementation matches its specification -- the strongest software guarantee available. Common Criteria evaluation at EAL 5+ provides high assurance but not mathematical proof. EAL 3--4 is more typical for commercial software guards.

**Verdict**: Hardware provides categorically stronger assurance for unidirectional enforcement. For bidirectional content-inspecting guards, the gap narrows because hardware guards also contain software (firmware, content inspection logic). The real question is whether the [classification boundary](../standards/classification.md) demands physics-based assurance.

## Certification

### Hardware CDS certification

- Physical inspection and TEMPEST testing
- Hardware design review (schematics, PCB layout)
- Firmware review (smaller scope than full software CDS)
- NCDSMO/NSA Raise the Bar baseline requirements (US)
- Typically faster because the TCB is smaller and behaviour more deterministic

### Software CDS certification

- Full source code review of security-enforcing components
- Penetration testing and vulnerability analysis
- Formal methods where applied
- OS and platform evaluation (the entire stack matters)
- Configuration management and build reproducibility

The key difference: hardware CDS has a smaller, better-defined certification boundary. Software CDS certification extends through the OS, hypervisor, libraries, and tools -- the entire software supply chain. See [certification and evaluation](../standards/certification.md) for how different national frameworks handle this.

## Cost

### Upfront

| Element | Hardware CDS | Software CDS |
|---------|-------------|-------------|
| Product acquisition | High | Lower |
| Installation | Rack space, power, cabling | VM/container deployment |
| Initial certification | Amortised by vendor | Often requires site-specific evaluation |
| Integration | Custom physical interfaces | Standard network/API |

### Operational

| Element | Hardware CDS | Software CDS |
|---------|-------------|-------------|
| Physical space | Rack units, cooling, power | Runs on existing infrastructure |
| Maintenance | Hardware spares, vendor contracts | Software patches, licence renewals |
| Staffing | Hardware maintenance + security ops | Security ops only |
| Updates | Hardware refresh every 5--10 years | Continuous patching |

**Verdict**: Software CDS has lower upfront and lifecycle costs but requires ongoing patch management and re-certification. Hardware CDS has higher upfront cost but may be cheaper to operate if the environment is stable. The patch management tension in software CDS -- unpatched systems are vulnerable, but patching changes the certified baseline -- is a real operational challenge. See [software CDS considerations](considerations.md) for more on this.

## Scalability and flexibility

Hardware CDS scales by buying and racking more physical units. Lead times of weeks to months. Protocol changes may require hardware replacement. New threats may require vendor firmware updates. The adaptation cycle is slow.

Software CDS scales horizontally by adding VMs or containers. In cloud environments, auto-scaling based on load is possible. Scaling happens in minutes. New protocols, content types, and policies are added through software updates. The adaptation cycle is days to weeks.

**Verdict**: Software CDS is dramatically more scalable and adaptable. For dynamic environments with changing protocols and policies, software is strongly favoured.

## Performance

Hardware CDS achieves line-rate inspection for specific protocols using purpose-built FPGA or ASIC processing. Latency is minimal for [data diodes](../hardware/data-diodes.md) (near wire speed) and moderate for [guards](../hardware/guards.md) with content inspection. Processing is deterministic -- no garbage collection or scheduler variability.

Software CDS throughput depends on compute allocation. Modern servers achieve 10+ Gbps for simple filtering but drop significantly for deep content inspection. Latency is higher and more variable due to OS scheduling and virtualisation overhead. CPU-bound content inspection (AV scanning, format conversion, DLP) is typically the bottleneck.

**Verdict**: Hardware wins for raw throughput and deterministic latency. Software is adequate for most workloads but may struggle at very high volumes or where latency is critical.

## Deployment speed and cloud

Hardware CDS requires weeks to months for procurement, racking, cabling, configuration, and testing. It is incompatible with public cloud -- physical presence in a data centre is required.

Software CDS deploys in hours to days for virtualised guards, minutes for cloud-native policy enforcement. Natively compatible with government cloud (AWS GovCloud, Azure Government). Can run on any compute platform with sufficient resources.

**Verdict**: Software CDS is the only option for cloud and for rapid deployment. Hardware CDS is constrained to traditional data centres.

## Patch management

Hardware CDS has infrequent, vendor-controlled firmware updates. Re-certification scope is narrow. Lower patch frequency means longer vulnerability windows, but smaller attack surface means fewer vulnerabilities.

Software CDS has frequent patches: OS monthly, libraries weekly, AV signatures daily. Each patch potentially changes the certified baseline. But more frequent patching reduces vulnerability windows.

The architectural solution: separate patchable components (content inspection engines) from the certified security-enforcing core (separation kernel, reference monitor). Only the core requires full re-certification. This is one reason [separation kernel architectures](considerations.md) are preferred -- the kernel changes rarely, whilst the components above it can be updated.

## Vendor lock-in and supply chain

Hardware CDS has high vendor lock-in (few vendors, proprietary hardware, expensive switching) and a hardware supply chain that is harder to verify (chips, PCBs, firmware) but has fewer components. Vendor viability is a real risk in a small market -- Forcepoint Federal became Everfox, Garrison was acquired, Archon was acquired by CACI.

Software CDS has moderate lock-in (more vendors, more open-source options, container/VM portability). The software supply chain is complex (open-source dependencies, compilers, build tools) but more auditable through SBOMs (Software Bills of Materials).

**Verdict**: Software offers more vendor diversity and lower switching costs. Both face supply chain risks of different kinds -- hardware risks are harder to detect; software risks are more numerous but more auditable. See [hardware CDS characteristics](../hardware/characteristics.md) for how vendors address supply chain concerns.

## Decision framework

### When hardware CDS is the right choice

1. **Unidirectional flow is required** -- [data diodes](../hardware/data-diodes.md) provide the only physics-based guarantee of one-way transfer
2. **Classification boundary is high** -- SECRET to TOP SECRET, or classified to unclassified
3. **Regulations mandate physical separation** -- many [national frameworks](../landscape/national-approaches.md) require hardware at the highest levels
4. **Threat model includes sophisticated adversaries** with side-channel attacks, covert channel exploitation, or software zero-days
5. **Simplicity and determinism are paramount** -- the guard must do one thing with maximum assurance
6. **Air-gap enforcement is required** -- connecting previously air-gapped networks
7. **Long operational life with minimal change** -- stable boundary and protocols for years

### When software CDS is the right choice

1. **Complex content inspection is needed** -- software is necessary for deep content analysis
2. **Cloud deployment is required** -- hardware cannot run in public or government cloud
3. **Scalability and elasticity matter** -- dynamic workloads
4. **Rapid policy changes are expected** -- new protocols, data formats, or transfer rules
5. **Cost constraints are significant** -- especially at lower classification boundaries
6. **Multiple protocols on one platform** -- web, email, database, API, file transfer
7. **Classification boundary is lower** -- OFFICIAL to OFFICIAL-SENSITIVE, or between business domains
8. **Integration with modern architectures** -- microservices, [data mesh](architectures.md), API-first design
9. **Zero Trust implementation** -- ZTA requires software-defined, dynamic policy enforcement

### When hybrid

Most real-world high-assurance deployments use a combination:

- **Hardware data diode** provides unidirectional enforcement with physics-based assurance
- **Software guard on a separation kernel** provides bidirectional content inspection and policy enforcement
- **Cloud-native controls** (VPC isolation, IAM, API gateways) provide outermost domain separation
- **Defence in depth** combines layers so failure in one does not compromise the boundary

The NCSC import pattern exemplifies this: the [protocol break](../how-it-works/protocol-break.md) and flow control layer (which can be a hardware diode) sits between the software transformation engine (low side) and the software verification engine (high side).

### Key questions to ask

| Question | Implication |
|----------|------------|
| What is the classification boundary? | Higher classification = stronger argument for hardware |
| What is the threat model? | Nation-state adversary = hardware for critical paths |
| Is unidirectional enforcement sufficient? | If yes, hardware data diode |
| What content inspection is needed? | Complex inspection requires software |
| Is cloud deployment required? | Cloud = software only |
| What is the expected change rate? | Frequent changes favour software |
| What is the budget (upfront + 10-year)? | Software is cheaper but has ongoing costs |
| What certifications are required? | Some frameworks mandate hardware at certain levels |
| What is the physical environment? | Tactical/mobile favours software; data centre supports either |
| What is the availability requirement? | Hardware redundancy means duplicate hardware; software can failover in virtualised infrastructure |

## The industry trend toward software

### Drivers

The trend toward software CDS is strong, driven by:

1. **Cloud migration** -- government and defence organisations are moving to cloud. Hardware CDS cannot follow.
2. **API-first architectures** -- modern systems communicate via APIs, not file transfers. Software CDS is native to this.
3. **Data mesh and distributed data** -- decentralised architectures need CDS at every data product boundary, not a single chokepoint.
4. **Zero Trust adoption** -- drives demand for software-defined, dynamic trust decisions.
5. **Cost pressure** -- software on shared infrastructure is significantly cheaper.
6. **Scalability demands** -- cross-domain data volume is growing faster than hardware can scale.
7. **Protocol diversity** -- new protocols emerge faster than hardware can be updated.

### Risks of the trend

1. **Assurance gap** -- software CDS has fundamentally lower assurance than hardware data diodes. Moving to software at higher boundaries increases risk.
2. **Certification lag** -- software changes faster than certification processes can evaluate it.
3. **Complexity** -- microservices, containers, and cloud-native architectures have a much larger attack surface than a hardware diode.
4. **Side channels** -- software on shared infrastructure is vulnerable to attacks that do not affect physically separated hardware.
5. **False equivalence** -- treating cloud security controls (VPC isolation, IAM, API gateways) as equivalent to evaluated CDS guards. Cloud controls are necessary infrastructure but are not independently evaluated or certified as CDS.
6. **Supply chain expansion** -- software CDS pulls in hundreds of dependencies, each a potential vulnerability.

### The likely future

The tiered model is emerging:

- **Software CDS for RESTRICTED/OFFICIAL** -- cloud-native, scalable, API-integrated, rapidly evolving
- **Hardware CDS for SECRET and above** -- physics-based assurance, certified, stable
- **Separation kernels** (seL4, LynxSecure, INTEGRITY) becoming the standard platform for high-assurance software CDS, providing a formally verified foundation that approaches hardware-level assurance
- **Hybrid architectures** dominating -- hardware for the physical layer, software for intelligence and policy, cloud controls for the outermost boundaries
- **Certification frameworks adapting** -- streamlined evaluation for software updates, component-based certification, and continuous assurance models

The question is not whether software CDS will grow -- it will. The question is whether assurance frameworks and certification processes can evolve fast enough to keep pace without lowering the security bar.
