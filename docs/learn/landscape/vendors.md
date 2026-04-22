# Vendors and Products

The CDS market is a specialised corner of the defence and security industry -- dominated by a handful of large primes and specialist firms, shaped by certification requirements, and undergoing rapid consolidation.

This page covers market size, vendor tiers, product comparisons, and the trends reshaping the industry. For how products get certified, see [Certification and Evaluation](../standards/certification.md). For how different nations evaluate and approve these products, see [National Approaches](national-approaches.md).

---

## Market Overview

The CDS market is substantial and growing fast.

| Metric | Value |
|--------|-------|
| 2025 market size | $3.75 billion |
| 2031 forecast | $7.29 billion |
| CAGR (2026--2031) | 11.45% |

### What is driving growth

| Driver | Impact |
|--------|--------|
| Escalating multi-domain classified data flows | Highest -- more data needs to cross classification boundaries |
| Zero Trust mandates (US DoD, NATO) | High -- ZTA requires explicit domain boundary controls |
| AI/ML systems requiring air-gapped feeds | Significant -- training pipelines crossing security boundaries |
| Commercial cloud enclaves for classified workloads | Growing -- AWS GovCloud, Azure Government creating new CDS demand |

### Market segments

**By solution type:** Transfer solutions hold the largest share (41%). Multi-level solutions are the fastest-growing segment (12.2% CAGR).

**By component:** Hardware still dominates (46% share), reflecting the continued importance of [data diodes](../hardware/data-diodes.md) and physical guards. Software and services are growing faster.

**By deployment:** On-premises dominates (58%), reflecting the classified nature of most deployments. Cloud CDS is the fastest-growing model (12.6% CAGR).

**By end-user:** Aerospace and defence holds nearly half the market (50%). Critical infrastructure is the fastest-growing sector (12% CAGR), driven by regulatory mandates.

---

## Vendor Tiers

### Tier 1: Major CDS Specialists

These vendors have CDS as a core business, with certified products and significant market presence.

| Vendor | HQ | Key products | Differentiator |
|--------|----|-------------|----------------|
| **Everfox** (formerly Forcepoint Federal) | US | High Speed Guard, Transfer Guard, Trusted Thin Client, Hardsec/SAVI, Threat Adaptive Defense | Broadest CDS portfolio; NCDSMO TSABI/SABI; acquired Garrison and Deep Secure |
| **Owl Cyber Defense** (Motorola Solutions) | US | XD Bridge ST, data diodes, XD Guardian XML, XD Air | Dual-market (government + OT/ICS); NCDSMO certified |
| **Waterfall Security** | Israel | WF-600/WF-500 gateways, HERA, WF-Flip | OT/ICS market leader; 1,000+ installations globally |
| **Nexor** | UK | Protean, Sentinel, Data Diode, GuarDiode, Guardian, Edge Guard | Broadest UK portfolio; strong NATO/coalition experience |
| **OPSWAT** | US | MetaDefender CDR, MetaDefender Kiosk, NetWall diodes | 30+ AV engines + CDR; cross-CDS integration |

### Tier 2: Defence Primes with CDS Products

Large defence companies where CDS is part of a broader portfolio.

| Vendor | Key CDS product | Notes |
|--------|----------------|-------|
| **BAE Systems** | XTS Guard family, XTS IRIS | Long NCDSMO baseline history; passed UK CAPS |
| **General Dynamics Mission Systems** | CDS for DoD/IC | Top 5 vendor by market share |
| **Lockheed Martin** | Radiant Mercury | IC-focused MLS; deep C4ISR integration |
| **Northrop Grumman** | CDS integrated into mission systems | IC and C4ISR focus |
| **L3Harris** | CDS capabilities | DoD and IC |

### Tier 3: European CDS Specialists

Vendors with strong national certifications and growing international reach.

| Vendor | HQ | Key products | Certifications |
|--------|----|-------------|---------------|
| **INFODAS** | Germany | SDoT Security Gateway, SDoT Diode, SDoT Labelling Service, SDoT Secure Network Card | BSI up to SECRET; NATO NIAPC; CC EAL4+ |
| **genua** (Bundesdruckerei) | Germany | vs-diode, cyber-diode, genugate, genuscreen, genuline VPN | BSI CC EAL4+; VS-NfD; NATO RESTRICTED; first virtualised firewall with EAL4+ |
| **Advenica** | Sweden | DD500E, DD1G, DD1000A, DD1000i, DDSFX-10G, ZoneGuard | CC certified (2026); Swedish Armed Forces N3 approval for SECRET/TOP SECRET |
| **Sentyron** | Netherlands | DataDiode Ruggedised 1G-10G, DataDiode Andean 1G | **CC EAL7+** (highest achievable); **NATO TOP SECRET** |
| **Fox-IT** (NCC Group) | Netherlands | Fox DataDiode | CC EAL7+; NATO SECRET |
| **Thales** | France | ELIPS, Citadel, Mistral | ANSSI Tres Secret Defense; NATO SECRET; EU SECRET |
| **Arbit** | Denmark | Data Diode 10GbE, TRUST Gateway | CC EAL7+ (BSI certified); **NATO COSMIC TOP SECRET** |
| **Saab/Clavister** | Sweden | TactiGate XD, TacEdge Trusted Router | NATO NIAPC; NATO Restricted/Secret |
| **Insta** | Finland | Secure Gateway, Cross Domain Guard | Traficom approved up to SECRET |

### Tier 4: CDR Specialists

Vendors focused on Content Disarm and Reconstruction -- increasingly integrated into guard solutions.

| Vendor | Key product | Notes |
|--------|------------|-------|
| **Glasswall** | Glasswall CDR, Foresight (AI), Halo | CMMC Level 2; trusted by NATO, NSA, Five Eyes |
| **Everfox/Deep Secure** | Threat Adaptive Defense | CDR integrated into guard pipeline |
| **OPSWAT** | MetaDefender CDR | 30+ AV engines + CDR; broadest deployment model |
| **Votiro** (now Menlo Security) | File Security | CDR pioneer; absorbed into browser isolation platform |

### Tier 5: Specialist / Niche Vendors

| Vendor | HQ | Niche |
|--------|----|-------|
| **Oakdoor** (PA Consulting) | UK | State-machine SEFs in hardware; 1G and 10GbE diodes |
| **Controlled Interfaces** | US | All-optical COTS diodes; 200G+ throughput |
| **Isode** | UK | Military messaging (M-Guard XML boundary guard, STANAG 4406/5066) |
| **ST Engineering** | Singapore | Secure e-Application Gateway with diode pairs |
| **Fend Inc** | US | ICS/Modbus data diodes |
| **HAVELSAN** | Turkey | Data FLOW X diode (domestic Turkish capability) |
| **ASELSAN** | Turkey | Dumrul (domestic Turkish critical infrastructure protection) |

---

## Product Comparison: Data Diodes

| Vendor | Product | Max throughput | Highest certification | Target market |
|--------|---------|---------------|----------------------|---------------|
| Sentyron | DataDiode Ruggedised | 1--10 Gbps | CC EAL7+, NATO TOP SECRET | Government, defence, CNI |
| Arbit | Data Diode 10GbE | 10 Gbps | CC EAL7+, NATO COSMIC TOP SECRET | Defence, NATO |
| Fox-IT | Fox DataDiode | 1--10 Gbps | CC EAL7+, NATO SECRET | Government, CNI |
| Controlled Interfaces | Optical Data Diode | **200G+** | NIST compliant, SABI tested | Nuclear, enterprise-scale |
| Waterfall Security | WF-600/WF-500 | N/A (public) | NERC, IEC, NIST, BSI, NSA recommended | OT/ICS (1,000+ sites) |
| Owl Cyber Defense | Data Diode range | N/A (public) | NCDSMO (XD Bridge ST) | Government + OT/ICS |
| INFODAS | SDoT Diode | N/A (public) | BSI up to SECRET | German defence, NATO |
| genua | vs-diode / cyber-diode | N/A (public) | BSI approved (classified) / OPC UA | Defence / Industry 4.0 |
| Advenica | DD range (6 products) | 10 Gbps (DDSFX-10G) | CC (2026), Swedish N3 | Nordic defence, EU |
| Nexor | Data Diode / GuarDiode | N/A (public) | ISO 27001, HMG approved | UK MOD, NATO |
| Oakdoor | 1G / Enterprise Diode | 1 / 10 Gbps | NCSC principles, NCDSMO RTB | Defence, CNI |
| Thales | ELIPS Secure Diode v3 | N/A (public) | ANSSI Top Secret, NATO SECRET | French defence, NATO |

**Key observations:**

- **Highest formal assurance:** Sentyron, Arbit, and Fox-IT all achieve CC EAL7+
- **Highest throughput:** Controlled Interfaces at 200G+ using COTS optical transceivers
- **Largest OT installed base:** Waterfall Security with 1,000+ global sites
- **Unique form factors:** Advenica DDSFX-10G (SFP module), Nexor Edge Guard (tactical), Oakdoor Enterprise (10GbE data centre)

---

## Product Comparison: Guards

| Vendor | Product | Direction | Highest certification | Notes |
|--------|---------|-----------|----------------------|-------|
| Everfox | High Speed Guard | Bidirectional | NCDSMO TSABI/SABI | High-throughput bulk transfer |
| BAE Systems | XTS Guard / XTS IRIS | Bidirectional | NCDSMO baseline; UK CAPS | Long-established product line |
| Owl Cyber Defense | XD Bridge ST | Bidirectional | NCDSMO certified | Bridges classification levels |
| Nexor | Guardian / Sentinel | Bidirectional | HMG approved | General data guard / email guard |
| INFODAS | SDoT Security Gateway | Bidirectional | BSI up to SECRET | German sovereign solution |
| genua | genugate | Bidirectional | BSI CC EAL4+, NATO RESTRICTED | Also available virtualised |
| Isode | M-Guard | Paired unidirectional | UK assurance | XML/messaging boundary guard |
| Oakdoor | Oakdoor Gateway | Bidirectional | NCSC principles, NCDSMO RTB | Single-box bidirectional |
| Lockheed Martin | Radiant Mercury | Bidirectional | NCDSMO (historical) | IC-focused MLS |

---

## Product Comparison: CDR

| Vendor | Product | AV engines | AI/ML capability | Integration model |
|--------|---------|-----------|------------------|-------------------|
| OPSWAT | MetaDefender CDR | 30+ | No | Network, kiosk, email, storage |
| Glasswall | Glasswall CDR / Halo | N/A | Foresight AI for zero-day prediction | On-prem, Oracle OCI, M365 |
| Everfox/Deep Secure | Threat Adaptive Defense | N/A | N/A | Integrated into guard pipeline |

CDR is increasingly a component of guard solutions rather than a standalone market. The trend is toward CDR-inside-the-guard. See [Treatments and Transforms](../how-it-works/treatments.md) for how CDR fits into the CDS processing pipeline.

---

## Product Comparison: Software and Cloud CDS

| Vendor | Product | Model | Certification | Notes |
|--------|---------|-------|--------------|-------|
| Nexor | Protean | Cloud-native | ISO 27001, HMG | Most explicitly cloud-targeted CDS |
| genua | genugate Virtual | Virtualised | BSI CC EAL4+, NATO RESTRICTED | First virtualised firewall with EAL4+ |
| genua | genuscreen Virtual | Virtualised | BSI VS-NfD, NATO RESTRICTED | First virtualised VPN with full BSI approval |
| genua | genusphere | Cloud/SaaS | BSI process | Zero Trust Application Access |
| Advenica | ZoneGuard | On-prem/virtual | CC (2026) | Software-based cross-domain guard |

This is the **most immature segment**. Most CDS products remain hardware appliances. But it is also the fastest-growing (cloud CDS at 12.6% CAGR). The key barrier is [certification of virtualised environments](../standards/certification.md) -- genua's BSI approval of virtualised products demonstrates it is achievable at RESTRICTED level but remains rare.

---

## NIAPC-Listed Products

The [NATO NIAPC](../standards/international.md) lists products evaluated for use in NATO information systems. Known CDS products include:

| Product | Vendor | Origin | Max NATO certification |
|---------|--------|--------|----------------------|
| Arbit Data Diode 10GbE | Arbit | Denmark | **COSMIC TOP SECRET** |
| Fox DataDiode | Fox-IT/NCC Group | Netherlands | SECRET |
| SDoT Security Gateway | INFODAS | Germany | SECRET |
| SDoT Software Data Diode | INFODAS | Germany | SECRET |
| SDoT Labelling Service | INFODAS | Germany | SECRET |
| ELIPS Secure Diode v3 | Thales | France | SECRET (ANSSI Top Secret) |
| Arbit TRUST Gateway | Arbit | Denmark | SECRET |
| XD Guardian XML | Owl Cyber Defense | US | US Government assessed |
| MetaDefender Optical Diode | OPSWAT | US | CC EAL4+ |
| TactiGate XD | Saab/Clavister | Sweden | SECRET |
| Clavister Firewall | Clavister | Sweden | Listed August 2024 |
| IHSE KVM Extenders | IHSE | Germany | CC EAL4+ |

---

## Consolidation Trends

The CDS market has seen significant M&A activity, driven partly by [Raise the Bar](../standards/certification.md) requirements pushing less capable vendors out.

### The Everfox acquisition trail

The dominant vendor story of the past decade:

1. **Raytheon Websense** becomes **Forcepoint** (2016)
2. TPG Capital acquires Forcepoint's government business
3. Rebranded as **Everfox** (2024)
4. **Deep Secure** (UK CDR) incorporated into Everfox
5. **Garrison Technology** (UK hardsec/SAVI) acquired by Everfox (2025)

The result: Everfox now has the broadest CDS portfolio of any single vendor -- access solutions, transfer guards, CDR, and hardware-enforced isolation.

### Other consolidation

- **Owl Cyber Defense** acquired by **Motorola Solutions** -- data diodes into a larger communications platform
- **Fox-IT** acquired by **NCC Group** -- data diodes within a security consultancy
- **Votiro** acquired by **Menlo Security** (2025) -- CDR into browser isolation
- **genua** acquired by **Bundesdruckerei** (2020) -- German sovereign security
- **Arbit** entered strategic partnership with **Leonardo** (December 2024) -- Danish CDS technology into the Italian defence market

### Sovereignty considerations

Several acquisitions and partnerships reflect a growing emphasis on national sovereignty in CDS:

- genua's Bundesdruckerei ownership makes it a German sovereign trust anchor
- Sentyron is recognised by the Dutch government as "strategically vital"
- INFODAS emphasises "Made in Germany"
- Turkey is developing indigenous CDS through HAVELSAN and ASELSAN

Nations increasingly want CDS products built, certified, and controlled within their own borders -- particularly for the highest classification levels. This works against global consolidation and may fragment the market further.
