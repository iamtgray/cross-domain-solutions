# Classification Systems

A CDS exists to move data between security domains. The classification level of those domains -- and the gap between them -- determines everything about what CDS is required, how it must be built, and how rigorously it must be tested.

This page covers the major national and alliance classification systems, maps them against each other, and explains why "SECRET" does not mean the same thing everywhere.

For the standards used to evaluate CDS products, see [International Standards](international.md). For the certification processes that enforce these requirements, see [Certification and Evaluation](certification.md).

---

## NATO Classification System

NATO uses five classification levels defined in security policy C-M(2002)49:

| Level | Abbreviation | Compromise impact |
|-------|-------------|-------------------|
| COSMIC TOP SECRET | CTS | Exceptionally grave damage to NATO |
| NATO SECRET | NS | Serious damage to NATO |
| NATO CONFIDENTIAL | NC | Damage to NATO |
| NATO RESTRICTED | NR | Disadvantageous to NATO interests |
| NATO UNCLASSIFIED | NU | Copyright-protected but not classified |

"COSMIC" stands for Controlled Originator Special Management in Confidence.

### Special NATO markings

**ATOMAL** is applied to US Restricted Data, Formerly Restricted Data, or UK Atomic Information released to NATO:

- COSMIC TOP SECRET ATOMAL (CTS-A)
- NATO SECRET ATOMAL (NSAT)
- NATO CONFIDENTIAL ATOMAL (NCA)

CDS operating in NATO environments must process all five levels, handle ATOMAL markings, support [STANAG 4774/4778](international.md) labels, and map between NATO and national classification systems. This last requirement -- mapping -- is one of the hardest problems in coalition CDS. See [National Approaches](../landscape/national-approaches.md) for how NATO manages this.

---

## United States

US classification is governed by Executive Order 13526.

### Classification levels

| Level | Network | Compromise impact |
|-------|---------|-------------------|
| TOP SECRET (TS) | JWICS | Exceptionally grave damage to national security |
| SECRET (S) | SIPRNet | Serious damage to national security |
| CONFIDENTIAL (C) | -- | Damage to national security |
| UNCLASSIFIED (U) | NIPRNet | Not classified |

### Controlled Unclassified Information (CUI)

CUI sits below classified information but still requires protection. Created by Executive Order 13556 to replace a chaotic landscape of over 100 ad hoc markings -- FOUO, SBU, LES, and many more -- with a single unified system. CUI has over 124 categories across 20 groupings.

CDS at the unclassified boundary must handle CUI correctly. Data flowing from SIPRNet to NIPRNet may be downgraded to CUI rather than fully unclassified, and the CDS must enforce the correct markings.

### Sensitive Compartmented Information (SCI)

SCI is not a classification level -- it is an access control overlay. A common misconception is that SCI is "above TOP SECRET." In reality, information at *any* classification level can be within an SCI control system, though in practice most SCI is TOP SECRET.

Major SCI control systems:

- **Special Intelligence (SI)** -- communications intelligence
- **TALENT KEYHOLE (TK)** -- space-based imagery and signals intelligence
- **HCS** -- HUMINT Control System

Access requires a Single Scope Background Investigation (SSBI), explicit need-to-know for specific compartments, formal indoctrination, and a nondisclosure agreement. SCI must be processed in a SCIF (Sensitive Compartmented Information Facility).

**Marking example:** `TOP SECRET//SI-G 1234-M/TK-BLFH//NOFORN`

### Special Access Programs (SAPs)

SAPs provide additional protections beyond standard classification:

- **Acknowledged SAPs** -- existence publicly known, details classified
- **Unacknowledged SAPs (USAPs)** -- existence itself is classified
- **Waived SAPs** -- exempt from most reporting requirements

Three DoD categories exist: Acquisition (AQ-SAPs), Intelligence (IN-SAPs), and Operations and Support (OS-SAPs).

### CDS implications

US CDS must handle classification transitions between NIPRNet, SIPRNet, and JWICS. They must enforce CUI handling at the unclassified boundary, SCI compartment restrictions, SAP markings, and releasability caveats. Products must be on the NCDSMO [baseline list](certification.md) -- either SABI or TSABI.

---

## United Kingdom

The UK simplified its classification system in April 2014, replacing the five-level Government Protective Marking Scheme (GPMS) with three tiers.

### Current system (2014 Government Security Classifications Policy)

| Level | Description | Clearance required |
|-------|-------------|-------------------|
| TOP SECRET | Exceptionally grave damage to national security | Developed Vetting (DV) or enhanced DV |
| SECRET | Serious harm if compromised | Security Check (SC) |
| OFFICIAL | Routine public sector business | Baseline Personnel Security Standard (BPSS) |

All material produced by a public body is **OFFICIAL** by default unless otherwise marked.

### OFFICIAL-SENSITIVE

When need-to-know applies within OFFICIAL:

- OFFICIAL-SENSITIVE
- OFFICIAL-SENSITIVE COMMERCIAL
- OFFICIAL-SENSITIVE LOCSEN (location sensitive)
- OFFICIAL-SENSITIVE PERSONAL

### Nationality caveats

| Marking | Scope |
|---------|-------|
| UK EYES ONLY | UK citizens only |
| CANUKUS EYES ONLY | Canadian, UK, US citizens |
| AUSCANNZUKUS | Five Eyes countries |

### Legacy system (pre-2014)

The old GPMS used five levels: TOP SECRET, SECRET, CONFIDENTIAL, RESTRICTED, PROTECT. Documents under the old system remain in circulation for up to 100 years, so CDS in UK environments may still encounter legacy markings.

??? question "Why did the UK simplify its classification system?"
    The 2014 reform recognised that the five-tier system was over-complex and poorly understood. The majority of government information does not need the protections of SECRET or TOP SECRET. By collapsing CONFIDENTIAL, RESTRICTED, and PROTECT into a single OFFICIAL tier with an optional SENSITIVE descriptor, the system became more practical. The trade-off is that the UK no longer has direct equivalents to NATO CONFIDENTIAL or NATO RESTRICTED, which creates [mapping challenges](#cross-national-mapping).

---

## Australia

Australia uses the 2018 Protective Security Policy Framework (PSPF).

| Level | Description |
|-------|-------------|
| TOP SECRET | Exceptionally grave damage |
| SECRET | Serious damage |
| PROTECTED | Damage to national interest |
| OFFICIAL: Sensitive | Requires need-to-know |
| OFFICIAL | Routine government business |
| UNOFFICIAL | Not related to official duty |

### Australian caveats

Four types of caveats apply:

1. **Codewords** -- sensitive compartmented information
2. **Foreign government markings** -- indicate origin
3. **Special handling instructions** -- EXCLUSIVE FOR, CABINET, NATIONAL CABINET
4. **Releasability markings** -- **AUSTEO** (Australian Eyes Only), **AGAO** (Australian Government Access Only), REL TO [countries]

Australian CDS must handle PSPF tiers, enforce AUSTEO and AGAO restrictions, and meet ASD [ISM gateway requirements](certification.md).

---

## Canada

Canada uses a dual system -- security classifications for national security information, and protected designations for non-national-security sensitive information.

### Security classifications

| Level | French equivalent | Compromise impact |
|-------|------------------|-------------------|
| TOP SECRET | Tres secret | Exceptionally grave injury |
| SECRET | Secret | Serious injury |
| CONFIDENTIAL | Confidentiel | Injury to national interest |

### Protected designations

| Level | Description |
|-------|-------------|
| PROTECTED C | Extremely grave injury outside national interest |
| PROTECTED B | Severe injury (medical records, tax returns) |
| PROTECTED A | Injury or embarrassment (employee IDs, banking) |

This dual system means Canadian CDS may need to handle both classification types -- a nuance not all CDS products are designed for.

### Canadian caveats

- **Canadian Eyes Only** -- restricted to Canadian nationals
- **Special Operational Information (SOI)** -- carries particularly severe breach penalties

---

## France

| Level | Description |
|-------|-------------|
| Tres Secret Defense | Requires Prime Minister authorisation; extreme damage |
| Secret Defense | Serious damage to national defence |
| Diffusion restreinte | Restricted distribution |

France abolished *Confidentiel Defense* in 2021, merging it into *Secret Defense*.

The caveat *special France* restricts information to French citizens.

---

## Cross-National Mapping

This is where it gets complicated. Here is an approximate mapping between the major systems:

| NATO | US | UK | Australia | Canada | France |
|------|----|----|-----------|--------|--------|
| COSMIC TOP SECRET | TOP SECRET | TOP SECRET | TOP SECRET | TOP SECRET | Tres Secret Defense |
| NATO SECRET | SECRET | SECRET | SECRET | SECRET | Secret Defense |
| NATO CONFIDENTIAL | CONFIDENTIAL | *(no direct equivalent)* | PROTECTED | CONFIDENTIAL | *(abolished 2021)* |
| NATO RESTRICTED | *(no direct equivalent)* | OFFICIAL-SENSITIVE | OFFICIAL: Sensitive | PROTECTED A/B | Diffusion restreinte |
| NATO UNCLASSIFIED | UNCLASSIFIED | OFFICIAL | OFFICIAL | UNCLASSIFIED | *(unclassified)* |

!!! warning "These mappings are approximate"
    Exact equivalences are established by bilateral and multilateral agreements and may differ from this table. The gaps are real and create genuine challenges for CDS at coalition boundaries.

### Why "SECRET" is not the same everywhere

The mapping gaps matter:

- The **US abolished RESTRICTED** after World War II. There is no direct US equivalent to NATO RESTRICTED.
- The **UK abolished CONFIDENTIAL and RESTRICTED** in 2014, folding them into OFFICIAL. There is no direct UK equivalent to NATO CONFIDENTIAL.
- **France abolished Confidentiel Defense** in 2021.

When a CDS sits at a NATO boundary and must translate between classification systems, these gaps create genuine policy decisions. Does UK OFFICIAL-SENSITIVE map to NATO RESTRICTED? Usually, yes -- but not always, and the specific context matters. A CDS cannot simply do a string replacement.

---

## Classification Pair Matrix

The classification differential between the high side and low side determines the CDS assurance requirements. The wider the gap, the more stringent the requirements.

| High side | Low side | CDS type needed | Approximate EAL | Notes |
|-----------|----------|----------------|----------------|-------|
| OFFICIAL-SENSITIVE | OFFICIAL | Minimal filtering | EAL2--3 | Content filtering may suffice |
| SECRET | OFFICIAL | Standard CDS | EAL4+ | [Protocol break](../how-it-works/protocol-break.md) + content inspection |
| SECRET | UNCLASSIFIED | Enhanced CDS | EAL5+ | Hardware-enforced controls |
| TOP SECRET | SECRET | High-assurance CDS | EAL5+ | TSABI baseline (US) |
| TOP SECRET/SCI | SECRET | Maximum assurance | EAL5--7 | Hardware-enforced, minimal TCB |
| TOP SECRET/SCI | UNCLASSIFIED | Maximum assurance | EAL6--7 | Widest gap; may require [data diode](../hardware/data-diodes.md) (one-way only) |

For the widest classification differentials -- particularly where data flows in only one direction -- [data diodes](../hardware/data-diodes.md) provide the highest assurance. Products certified at EAL7 (such as the Sentyron DataDiode and Arbit Data Diode) provide formally verified one-way data flow suitable for the most sensitive transitions.

---

## Caveats and Releasability Markings

Classification level is only half the story. Caveats and releasability markings add restrictions that are *not hierarchical* -- they sit alongside the classification.

A document can be SECRET//NOFORN (cannot be shared with any foreign national) or SECRET//REL TO GBR (can be shared with the UK). Both are SECRET, but the handling requirements differ dramatically.

### Common releasability markings

| Marking | Meaning | CDS handling |
|---------|---------|-------------|
| NOFORN | No foreign nationals | Block all foreign-destined transfers |
| REL TO [countries] | Releasable to listed nations only | Enforce destination whitelist |
| FVEY | Five Eyes nations only | Allow AUSCANNZUKUS destinations |
| UK EYES ONLY | UK nationals only | Block non-UK destinations |
| AUSTEO | Australian Eyes Only | Block non-Australian destinations |
| ORCON | Originator controlled | Require originator approval for release |

### CDS must enforce both dimensions

A CDS must simultaneously enforce:

1. **Classification level** -- determines which domains the information can traverse
2. **Caveats and releasability** -- determines which recipients within those domains can access the information

For [RFC 2634 (ESS)](international.md) security labels used in email CDS, the classification value must be accompanied by a security policy identifier that defines the handling rules. A CDS must understand not just *what level* but *whose rules apply*.

In coalition environments -- NATO mission networks connecting multiple nations -- a CDS may need to simultaneously enforce NATO policy, originating nation policy, and receiving nation policy. This creates some of the most complex CDS requirements in existence. See [National Approaches](../landscape/national-approaches.md) for how NATO structures this.
