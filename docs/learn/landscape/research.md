# Academic Research and Open Problems

CDS sits at the intersection of formal security theory and practical engineering under extreme constraints. The academic research landscape is relatively small -- most CDS knowledge lives behind classified fences -- but the open literature reveals both the theoretical foundations and the unsolved problems shaping the field's future.

For the products and vendors that commercialise this research, see [Vendors and Products](vendors.md). For the standards and certification frameworks that constrain what is deployable, see [Standards and Classification](../standards/index.md).

---

## Key Papers by Theme

### Foundational Security Models

These papers established the theoretical frameworks underpinning all CDS design.

| Paper | Authors | Year | Contribution |
|-------|---------|------|-------------|
| Secure Computer Systems: Mathematical Foundations | Bell, LaPadula | 1973/1976 | Formalised MLS policy as a state-machine model. "No read up, no write down." The foundational model for all CDS. |
| Integrity Considerations for Secure Computer Systems | Biba | 1977 | Inverse of BLP, addressing data integrity. "No read down, no write up." Underpins import path design -- why [CDR](../how-it-works/treatments.md) and sanitisation exist. |
| A Comparison of Commercial and Military Computer Security Policies | Clark, Wilson | 1987 | Transaction-based integrity model. Maps well to guard data flows: data enters as untrusted, is transformed by inspection, exits as verified. |

[Bell-LaPadula](../../glossary.md) is the model you will encounter most. [Data diodes](../hardware/data-diodes.md) enforce the star property (no write down) in hardware. [Guards](../hardware/guards.md) implement controlled exceptions to the simple security property (allowing reviewed data to flow down).

### CDS Architecture

| Paper | Authors | Year | Venue | Contribution |
|-------|---------|------|-------|-------------|
| Cross-domain solutions (CDS): A comprehensive survey | Sundaravarathan et al. | 2024 | IEEE | The most comprehensive academic CDS survey. Covers taxonomy, architectures, and automation. Essential reading. |
| Design and analysis of a trustworthy CDS architecture | Daughety | 2022 | PhD, Ohio State | First CDS architecture with a formally verified Trusted Computing Base. |
| vCDS: A virtualized CDS architecture | Daughety et al. | 2021 | MILCOM | Virtualised CDS for [software-defined](../software/architectures.md) cross-domain. |
| Auditing a software-defined CDS architecture | Daughety et al. | 2022 | IEEE | Addresses formal verification and DevSecOps pace for CDS. |
| Security test and evaluation of cross domain systems | Loughry | 2014 | PhD, Oxford | CDS evaluation methodology including optical emanation side-channels. |

### Data Diodes and Unidirectional Gateways

| Paper | Authors | Year | Contribution |
|-------|---------|------|-------------|
| Application of data diodes for nuclear power plant safety systems | Barker, Cheese | 2012 | Foundational reference for nuclear sector data diode deployment. |
| An FPGA-based unidirectional gateway for OT-IT separation | Ha et al. | 2023 | FPGA-based data diode design -- more flexible than pure hardware, more assured than software. |
| Secure cross-domain data validation with FPGAs | Cliche et al. | 2026 | FPGA-based content validation -- data formally verified in hardware before crossing domain boundary. |
| Data diode for cyber-security: A review | Almaazmi et al. | 2022 | Survey of data diode hardware and security mechanisms. |
| Protecting networks with intelligent diodes | Dahlstrom, Taylor | 2022 | Formally verified parser combinators (Hammer library) built into data diodes for content inspection. |

### MILS and Separation Kernels

| Paper | Authors | Year | Contribution |
|-------|---------|------|-------------|
| A novel MILS cross domain solution | Liguori | 2015 | MILS-based CDS with attack model and security mechanisms. |
| From MLS to MILS | Liguori | 2017 | Historical evolution from MLS to MILS for CDS architecture. |
| High-assurance separation kernels: survey on formal methods | Zhao et al. | 2017 | Survey of formal verification for separation kernels -- the TCB underpinning MILS CDS. |
| Security certification of CPS based on compositional MILS | Hohenegger et al. | 2021 | Compositional certification for MILS CDS -- certify the kernel once, certify applications independently. |

### Cloud and Zero Trust CDS

| Paper | Authors | Year | Contribution |
|-------|---------|------|-------------|
| Formal verification of secure information flow in cloud computing | Zeng et al. | 2016 | BLP-based flow-sensitive security model for federated clouds. 40 citations. |
| ZTA adoption in CDS -- seven-pillar perspective | Lee et al. | 2026 | Structured analysis of how Zero Trust maps to CDS. |
| Novel bidirectional distributed CDS architecture | Ansariyan, Doostari | 2024 | Distributed CDS for cloud scalability. |
| Deep learning CNN for packed malware in cloud CDS filters | Aguilera, Jacobson | 2022 | ML applied to CDS content inspection. |

### Other Notable Work

| Paper | Authors | Year | Contribution |
|-------|---------|------|-------------|
| XDDS: Scalable guard-agnostic cross domain discovery | Atighetchi et al. | 2010 | How services find each other across guarded boundaries at scale. |
| Solving the cross domain problem with functional encryption | Kaminsky et al. | 2021 | Cryptographic rather than boundary-based CDS -- early-stage but potentially transformative. |
| Towards high-assurance CDS for connected aviation | Federici et al. | 2024 | CDS modelling language with automated generation. CDS expanding into aviation. |
| Cross-core covert channel for RISC-V | Khan et al. | 2026 | Even separate processor cores do not eliminate covert channels. |
| Shared memory CDS for 5G applications | Sundaravarathan | 2025 | CDS for 5G network slicing and edge computing. |

**Key venues for CDS research:** MILCOM (IEEE Military Communications Conference) is where most CDS-specific papers appear. Other relevant venues include IEEE S&P, USENIX OSDI, ACSAC, and AIAA.

---

## Research Theme Maturity

| Theme | Research maturity | Commercial maturity | Key barrier |
|-------|------------------|--------------------|----|
| Formal methods for CDS | Medium | Low | Complexity of verifying real-world content inspection |
| ML in CDS | Low--Medium | Low | Deterministic assurance vs probabilistic ML |
| Cloud CDS | Medium | Low--Medium | [Certification](../standards/certification.md) of virtualised environments |
| IoT/Edge CDS | Low--Medium | Low--Medium | Resource constraints, form factor |
| Real-time CDS | Medium | Medium | Bidirectional latency for content inspection |
| Post-quantum CDS | Low--Medium | Low (genua exception) | Algorithm performance impact |
| Policy languages | Low | Very Low | No standard, vendor lock-in |
| Software-defined CDS | Medium | Low--Medium | Assurance gap vs [hardware](../hardware/index.md) |
| CDS + Zero Trust | Medium | Medium | Conceptual integration with ZTA |
| Coalition CDS | Medium | Medium | Political/policy barriers, no interoperability standard |

---

## Ten Open Problems

These are the unsolved challenges that define the field's future.

### 1. Scaling CDS to cloud-native architectures

Traditional CDS are hardware appliances at a single network boundary. Cloud-native architectures create hundreds of logical boundaries. You cannot put a [hardware guard](../hardware/guards.md) between every microservice.

The fundamental challenge: how do you provide hardware-enforced guarantees in a virtualised environment where you do not control the underlying silicon? Hypervisor escape, speculative execution side channels, and cloud provider supply chain risks are unresolved for high-assurance workloads.

**Current progress:** genua achieved BSI approval for virtualised firewalls at RESTRICTED level. Nexor Protean targets cloud deployment. But SECRET-level cloud CDS remains aspirational.

### 2. CDS for streaming and real-time data at scale

Video feeds, sensor data, and situational awareness updates need to flow across domain boundaries in real time. Traditional "inspect then release" does not apply to continuous streams. Bandwidth requirements (4K video, multi-sensor fusion) make guards into bottlenecks.

**Current progress:** FPGA-based approaches achieve hardware-speed content validation for structured data. Data diodes support real-time one-way streaming natively. Bidirectional real-time CDS remains fundamentally harder.

### 3. Automated policy management

CDS policies are configured manually through vendor-specific tools. There is no standard CDS policy language. Each vendor has proprietary configuration. Cross-vendor policy interoperability is non-existent.

**Current progress:** Federici et al. (2024) developed a modelling language with automated generation for aviation CDS. Standardisation is distant.

### 4. CDS testing and assurance at DevSecOps speed

[Certification](../standards/certification.md) takes 18--36 months. DevSecOps operates in sprints of days to weeks. Any change to a certified CDS risks invalidating the certification.

**Current progress:** Compositional certification (Hohenegger et al., 2021) could allow independent component updates. genua achieved certified patch management -- patches without re-certification. These are leading examples but not industry norms.

### 5. Covert channel detection and prevention

Covert channels -- unintended communication paths violating the CDS security policy -- are a fundamental threat. Complete covert channel analysis is theoretically undecidable. New channels are continually discovered (Spectre, Meltdown, cross-core cache attacks).

**Current progress:** ML-based detection of timing channels is being researched. Isode's M-Guard implements rate limiting. But you cannot prove the absence of all possible covert channels.

### 6. CDS for unstructured data

Guards work well for structured data (emails, XML messages with security labels). Unstructured data -- free-text documents, images, video, audio -- is much harder to inspect. Is that satellite photo SECRET because of what it shows, or UNCLASSIFIED because it is publicly available?

**Current progress:** CDR addresses the malware/format problem. Glasswall Find and Redact handles DLP for Office documents. But reliable automated classification of unstructured content remains out of reach.

### 7. Reducing the human review burden

Many deployments require human reviewers to examine data before it crosses boundaries. Human review is slow, expensive, does not scale, and is prone to fatigue-induced rubber-stamping.

**Current progress:** ML-assisted pre-screening is emerging but not widely deployed in certified CDS. Moving from human-in-the-loop to human-on-the-loop (monitoring automated decisions rather than making each one) is the direction of travel.

### 8. CDS interoperability between nations

NATO operations require CDS from different nations to work together. Different products, different [classification schemes](../standards/classification.md), different certification regimes, different security labels. There is no universal CDS interoperability standard.

**Current progress:** NATO STANAGs (4774/4778 for labelling, 4406 for messaging) provide some standardisation. Products with native NATO support (Isode, Nexor, INFODAS) have an advantage. But political barriers are as significant as technical ones.

### 9. Keeping CDS current with evolving threats

CDS are certified against point-in-time evaluations. A product certified in 2023 may not defend against a 2026 exploit technique. Patching risks invalidating certification.

**Current progress:** genua's certified patch management is a breakthrough. Glasswall Foresight uses AI for zero-day prediction. The architectural answer is separating the stable "policy enforcement mechanism" from the rapidly-updateable "threat intelligence."

### 10. Supply chain security for CDS components

CDS are assembled from commercial components (processors, FPGAs, firmware, libraries) that may contain vulnerabilities or backdoors. State-level adversaries have demonstrated supply chain capabilities.

**Current progress:** INFODAS SDoT Secure Network Card directly addresses supply chain attacks. National sovereignty trends drive demand for domestically manufactured CDS. RISC-V (open-source ISA) is being explored for security-critical applications.

---

## Priority Assessment

| Problem | Impact | Tractability | Time horizon |
|---------|--------|-------------|-------------|
| Cloud-native CDS | Very High | Medium | 3--5 years for RESTRICTED; 5--10 for SECRET |
| Streaming/real-time CDS | High | Medium | 3--5 years (FPGA approaches) |
| Automated policy management | High | Medium | 5--10 years for standardisation |
| DevSecOps assurance speed | Very High | Low--Medium | 5--10 years (compositional certification) |
| Covert channel detection | High | Low | Ongoing / never fully solved |
| Unstructured data CDS | High | Low | 5--10 years (depends on AI progress) |
| Human review reduction | Medium--High | Medium | 3--5 years for ML-assisted triage |
| Coalition interoperability | High | Low (political) | 5--10 years |
| Evolving threat currency | High | Medium | 2--5 years (architectural separation) |
| Supply chain security | Very High | Low | 10+ years for full assurance |
