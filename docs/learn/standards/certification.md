# Certification and Evaluation

Getting a CDS product from development to deployment is a long, expensive, and fragmented process. Certification -- proving that a product meets the required security standards -- is the single biggest barrier to entry in the CDS market.

This page covers how CDS products get certified, who does the certifying, how long it takes, what it costs, and why a certificate from one country rarely helps in another. For the standards being evaluated against, see [International Standards](international.md). For the classification levels that drive assurance requirements, see [Classification Systems](classification.md).

!!! info "Certification is not accreditation"
    **Certification** evaluates that a CDS *product* meets defined security requirements. It is product-focused.

    **Accreditation** authorises a specific CDS *deployment* in a specific environment for a specific classification pair. It considers the certified product, the physical installation, the personnel, the operating procedures, and the residual risk.

    A product can be certified but never deployed. A deployment can only be accredited if it uses a certified product. The two are complementary but distinct.

## The Common Criteria Process

The standard CC evaluation process for CDS follows four steps:

1. **Vendor develops a Security Target (ST)** -- defines the product scope, security requirements, and claims conformance to relevant [Protection Profiles](international.md)
2. **Accredited lab evaluates** -- an independent Common Criteria Testing Laboratory (CCTL) performs the technical evaluation
3. **National scheme certifies** -- the national certification body reviews the evaluation and issues the certificate
4. **Mutual recognition** -- the certificate may be recognised by other nations under CCRA or (formerly) SOG-IS

For CDS, this process is significantly more demanding than for typical IT products. The security-critical nature of cross-domain solutions means deeper analysis, more evidence, and more time.

---

## National Evaluation Processes

### United States -- NCDSMO and CDTAB

The US has the most formalised CDS evaluation ecosystem, centred on the **National Cross Domain Strategy and Management Office (NCDSMO)** within the NSA.

The NCDSMO:

- Maintains the Cross-Domain Baseline List (approved CDS products)
- Manages the CDS Testing Program
- Oversees the Cross Domain Technical Advisory Board (CDTAB)
- Issues policy and guidance on CDS deployment

Products are evaluated through the **Lab-Based Security Assessment (LBSA)**, which goes well beyond standard CC evaluation:

| LBSA element | What it tests |
|-------------|---------------|
| Architecture review | Structural security of the CDS design |
| Source code analysis | Full code review including third-party components |
| Penetration testing | Active adversarial testing |
| Configuration assessment | Security of the evaluated configuration |
| Interoperability testing | Correct operation with target systems |

Products that pass receive authorisation at one of two levels:

**SABI** (Secret and Below Interoperability)
:   For CDS handling SECRET and below.

**TSABI** (Top Secret/SCI and Below Interoperability)
:   For CDS handling TOP SECRET/SCI. The most stringent evaluation.

#### Raise the Bar

The **Raise the Bar (RTB)** initiative, introduced by NSA/NCDSMO in 2018, fundamentally raised CDS security requirements. Current version: RTB Baseline Release v4.1 (July 2022). RTB goes beyond standard CC evaluation to mandate:

- Hardware-enforced data flow control
- Full [protocol breaks](../how-it-works/protocol-break.md)
- Deep content inspection
- Separation kernels or equivalent isolation
- Reduced attack surface and minimal trusted computing base
- Continuous evaluation, not just point-in-time

RTB significantly raised the barrier to entry. The number of approved CDS products on the baseline list dropped substantially, and vendors who could not meet the requirements exited the market. This drove much of the [market consolidation](../landscape/vendors.md) seen over the past decade.

### United Kingdom -- NCSC

The UK's National Cyber Security Centre takes a **Principles-Based Assurance (PBA)** approach. Rather than prescribing specific EAL levels, NCSC evaluates CDS against its 13 Security Principles covering everything from network protocol attack protection to component integrity.

NCSC operates two key schemes:

**CAPS (CESG Assisted Products Service)**
:   The highest assurance level for UK government CDS products. CAPS evaluates products handling SECRET and above. BAE Systems' XTS IRIS Large Scale Enterprise platform passed UK CAPS evaluation.

**PBA (Principles-Based Assurance)**
:   Products are assessed against the 13 CDS principles. NCSC issued updated cross-domain guidance in April 2026, including new import and export design patterns.

The UK approach is less prescriptive than the US but no less rigorous. NCSC works directly with vendors through the evaluation process, and the emphasis is on demonstrating that a product addresses real risks rather than ticking certification checkboxes.

### Germany -- BSI

The German Federal Office for Information Security (BSI) operates several certification programmes:

- **Common Criteria** -- full CC evaluation, typically EAL4+ for CDS
- **Technical Guidelines (TR)** -- Germany-specific requirements
- **Accelerated Security Certification (BSZ)** -- fixed-time cybersecurity certification

BSI approves products for German national (VS-NfD, GEHEIM), EU, and NATO classifications in parallel. German CDS vendors like [INFODAS and genua](../landscape/vendors.md) hold extensive BSI certifications. Notably, genua achieved BSI certification for the first virtualised firewall (genugate Virtual) at EAL4+ -- a significant milestone for [software CDS](../software/index.md).

### France -- ANSSI

The French ANSSI operates two main certification tracks:

**Common Criteria**
:   Full CC evaluation. ANSSI acts as both the national scheme and the certification body.

**CSPN (Certification de Securite de Premier Niveau)**
:   A faster, lower-cost alternative with a fixed evaluation timeframe (typically 2--3 months). CSPN is national recognition only -- not mutually recognised under CCRA.

France also has a unique **Qualification** scheme (Standard, Reinforced, or High level) representing the highest French government approval. Thales' ELIPS-SD network diode holds ANSSI approval up to Tres Secret Defense.

In an unusual move, ANSSI published **Eurydice** as open-source software -- a user-friendly solution for transferring files through a physical data diode. It is one of the few national-authority-released CDS tools available on GitHub.

### Australia -- ASD

The Australian Signals Directorate runs the Australasian Information Security Evaluation Program (AISEP) and maintains the **Evaluated Products List (EPL)**. Products on the EPL have passed ASD's High Assurance evaluation.

Australia refers to CDS as "gateways" and "cross domain solutions" in its Information Security Manual (ISM). The ISM's Guidelines for Gateways contain Australia's specific CDS requirements, including content filtering, protocol break, and hardware-enforced unidirectional data flow requirements.

---

## Certification Timelines and Costs

| Scheme | Typical timeline | Estimated cost | Notes |
|--------|-----------------|---------------|-------|
| CC EAL2--3 | 6--12 months | $150K--$400K | Low-risk components |
| CC EAL4 (NIAP, US) | 12--24 months | $500K--$1.5M | Does not include LBSA |
| NCDSMO LBSA (US) | 12--36 months | $1M--$5M+ | On top of CC; includes active testing |
| NCSC PBA (UK) | 6--18 months | Varies widely | Depends on product complexity |
| NCSC CAPS (UK) | 12--24+ months | Significant | For higher-assurance products |
| ANSSI CSPN (France) | 2--3 months | Lower cost | National recognition only |
| ANSSI CC (France) | 12--24 months | $500K--$2M | Recognised under CCRA |
| BSI CC (Germany) | 12--24 months | $500K--$2M | Recognised under CCRA |
| ASD AISEP (Australia) | 12--24+ months | Varies | National requirements |

!!! warning "These figures are approximate"
    Evaluation bodies do not typically publish pricing. Costs vary significantly by product complexity, vendor preparedness, and scope changes during evaluation. Augmented requirements (EAL4+, EAL5+) further increase cost and time. A CDS vendor targeting multiple national markets may spend **$5M or more** across overlapping certification programmes.

---

## Mutual Recognition -- The Reality

### CCRA (Common Criteria Recognition Arrangement)

The CCRA enables mutual recognition of CC certificates between member nations. Certificate-producing members include the US (NIAP), UK (NCSC), France (ANSSI), Germany (BSI), Canada (CSE), and Australia (ASD).

The critical limitation: **CCRA mutual recognition extends only to EAL2** (with flaw remediation augmentation). Since CDS products typically require EAL4 or above, a CC certificate from one country does not carry weight in another for CDS purposes. High-assurance CDS must be independently evaluated under each national scheme.

### SOG-IS -- Ceased February 2026

The SOG-IS Mutual Recognition Agreement provided European mutual recognition at higher EAL levels, covering 17 participating nations. Two recognition tiers existed:

1. **Basic** -- certificate recognition up to EAL4
2. **Higher** -- recognition at higher EAL levels for approved technical domains

**As of 27 February 2026, SOG-IS ceased to emit certificates.** The implications for European CDS evaluation and mutual recognition are still being determined. Replacement arrangements are not yet established.

### The fragmented result

The combination of CCRA's EAL2 limit and SOG-IS cessation means CDS products face a severely fragmented certification landscape:

| Market | Required evaluation |
|--------|-------------------|
| United States | NCDSMO/LBSA evaluation |
| United Kingdom | NCSC PBA or CAPS evaluation |
| Germany | BSI certification |
| France | ANSSI CC or CSPN |
| Australia | ASD AISEP evaluation |
| NATO | NIAPC listing based on national evaluations |
| Five Eyes | Informal mutual recognition through intelligence relationships |

This fragmentation is one of the most significant barriers for CDS vendors operating internationally. A product certified by one nation must typically be re-evaluated by each additional national scheme, multiplying cost and time. See [National Approaches](../landscape/national-approaches.md) for how different nations handle this in practice.

---

## The Certification Bottleneck

The 18--36 month certification cycle creates a structural problem for the CDS industry. Products are always behind the current threat landscape by the time they achieve certification. Any change -- a patch, a feature, a configuration update -- risks invalidating the certification.

Some approaches to addressing this:

Compositional certification
:   Certify the separation kernel once, then certify applications independently. Proposed by Hohenegger et al. (2021) for MILS architectures.

Certified patch management
:   genua achieved BSI-certified patch management for genugate -- described as "unique worldwide" -- allowing patches to be deployed without re-certification.

Architectural separation
:   Separate the "policy enforcement mechanism" (which needs stable certification) from "threat intelligence" (which needs rapid updates). See [Academic Research](../landscape/research.md) for more on this.

??? question "Does certification guarantee security?"
    No. Certification demonstrates that a product met defined requirements at a specific point in time, in a specific configuration. It does not guarantee the product is free from all vulnerabilities, and it does not cover the deployment context (physical security, operator competence, network architecture). That is what [accreditation](#) addresses -- the site-specific risk assessment that considers the certified product in its actual operating environment.

??? question "Can I use an uncertified CDS?"
    In most government and defence contexts, no. US DoD policy (DoDI 8540.01) requires all CDS to be on the NCDSMO baseline list. UK MOD requires NCSC-evaluated products. NATO requires NIAPC-listed products. For commercial and critical infrastructure deployments, the requirements are less formal but increasingly driven by regulation (NRC data diode mandates for nuclear, NERC CIP for electricity, IEC 62443 for industrial).
