# National Approaches

Every NATO nation needs cross-domain solutions, but the maturity of their CDS ecosystems varies enormously -- from the US with its formalised baseline list and dedicated evaluation office, down to recent NATO members that rely entirely on alliance infrastructure.

This page covers the Five Eyes nations in depth, European approaches, NATO-wide policy, and the broader picture. For the products these nations evaluate, see [Vendors and Products](vendors.md). For the certification processes they use, see [Certification and Evaluation](../standards/certification.md).

---

## Maturity Tiers

| Tier | Nations | Characteristics |
|------|---------|----------------|
| **Tier 1: Full ecosystem** | US, UK | Dedicated CDS evaluation bodies, published guidance, approved product lists, domestic vendor base |
| **Tier 2: Mature capability** | Germany, France, Australia | National certification schemes, domestic CDS vendors, NATO/EU product listings |
| **Tier 3: Developing capability** | Canada, Netherlands, Sweden, Finland, Denmark | Evaluation authority exists, some domestic vendors, leverage partner evaluations |
| **Tier 4: Consumer nations** | Norway, Poland, Italy, Spain, Israel | Active CDS users, rely on allied products, limited domestic CDS manufacturing |
| **Tier 5: Alliance-dependent** | Baltics, Balkans, Iceland, smaller members | Rely on NATO infrastructure and NIAPC-listed products |

---

## Five Eyes

The Five Eyes nations (US, UK, Canada, Australia, New Zealand) represent the most mature CDS markets globally. Their intelligence-sharing alliance under the UKUSA Agreement drives deep cooperation on information assurance, including cross-domain technologies.

??? question "What are the Five Eyes?"
    The Five Eyes (FVEY) alliance -- US, UK, Canada, Australia, New Zealand -- is the world's most significant intelligence-sharing partnership. It originated from the 1946 UKUSA Agreement for signals intelligence cooperation. The Five Eyes designation itself started as shorthand for an "AUS/CAN/NZ/UK/US Eyes Only" releasability caveat. See [Classification Systems](../standards/classification.md) for how FVEY markings work.

### United States

The US has the most developed CDS ecosystem globally.

**National Cross Domain Strategy and Management Office (NCDSMO)**

:   Housed within the NSA, the NCDSMO is the primary US authority for CDS. It sets design and implementation requirements, manages the CDS Testing Program, conducts CDS-related threat intelligence, and maintains the cross-domain baseline list. The office evolved through several names: UCDMO, UCDSMO, and now NCDSMO.

**Raise the Bar (RTB)**

:   Introduced in 2018, RTB fundamentally raised CDS security requirements beyond standard NIST RMF controls. Current version: RTB Baseline Release v4.1 (July 2022). RTB mandates hardware-enforced controls, [protocol breaks](../how-it-works/protocol-break.md), deep content inspection, and continuous evaluation. It significantly raised the barrier to entry for CDS vendors. See [Certification and Evaluation](../standards/certification.md) for full details.

**SABI/TSABI Authorisations**

:   Products receive authorisation at two levels: SABI (Secret and Below Interoperability) and TSABI (Top Secret/SCI and Below Interoperability). Both Everfox and Owl Cyber Defense have achieved TSABI and SABI authorisations.

**DoDI 8540.01**

:   The governing DoD instruction for CDS. Requires all CDS to be on the NCDSMO baseline list. Defines three CDS types: access, transfer, and multi-level solutions. Maintains a sunset list for products being phased out.

**DISA CDES**

:   The Defense Information Systems Agency's Cross Domain Enterprise Service provides operational support for CDS across DoD, including fielding and lifecycle support.

**Known US-certified products:**

| Vendor | Products | Authorisations |
|--------|----------|---------------|
| Everfox | Transfer Guard, High Speed Guard, Trusted Thin Client | TSABI, SABI |
| Owl Cyber Defense | XD Guardian XML, data diodes | RTB-compliant, NSA-approved |
| BAE Systems | XTS Guard, XTS Diode, XTS IRIS | TSABI, SABI; first RTB-compliant OWT |
| OPSWAT | MetaDefender Diode X, Optical Diode | Software-defined CDS |

### United Kingdom

The UK has a well-established CDS ecosystem driven by NCSC and MOD requirements.

**NCSC Cross-Domain Guidance**

:   The NCSC published 13 Security Principles for CDS, recently updated in April 2026 with new import and export design patterns. The principles cover network protocol attack protection, content-based attack protection, session isolation, persistent compromise protection, management, audit, authentication, data protection, patching, and component integrity.

**CAPS and PBA**

:   CAPS (CESG Assisted Products Service) provides the highest assurance for UK government CDS. PBA (Principles-Based Assurance) evaluates products against the 13 principles. The UK approach is less prescriptive than the US but no less rigorous.

**Known UK-evaluated products:**

| Vendor | Products | Assurance |
|--------|----------|-----------|
| Everfox (via Garrison) | Transfer Guard, SAVI Isolation Platform | NCSC-evaluated |
| Nexor | GuarDiode, Edge Guard | UK specialist |
| Oakdoor | Oakdoor Basic Diode | NCSC CAPS-approved |
| BAE Systems | XTS IRIS Large Scale Enterprise | UK CAPS evaluated |

### Australia

The Australian Signals Directorate (ASD) is the national authority. Key elements:

- Products evaluated against the **Information Security Manual (ISM)** Guidelines for Gateways
- CDS referred to as "gateways" and "cross domain solutions" in Australian guidance
- Evaluated products listed on the **ASD Evaluated Products List (EPL)**
- Classification uses the PSPF scheme: UNOFFICIAL, OFFICIAL, OFFICIAL:Sensitive, PROTECTED, SECRET, TOP SECRET

### Canada

The **Communications Security Establishment (CSE)**, through the **Canadian Centre for Cyber Security (CCCS)**, handles CDS evaluation. Canada's **ITSG-33** framework provides IT security risk management guidelines including provisions for classified system interconnections.

Canada does not publish a publicly available CDS approved products list. It leverages Five Eyes partner evaluations, particularly from the US and UK.

### New Zealand

The **Government Communications Security Bureau (GCSB)** is responsible for CDS policy. The **NZISM** (New Zealand Information Security Manual) contains CDS requirements that closely mirror the Australian ISM structure.

As the smallest Five Eyes nation, New Zealand does not maintain its own evaluated products list. It relies on Australian ASD EPL evaluations and Five Eyes mutual recognition.

---

## European Approaches

??? info "Germany -- BSI and INFODAS"
    Germany has one of the most developed CDS ecosystems in Europe.

    **BSI** operates Common Criteria, Technical Guidelines, and Accelerated Security Certification (BSZ) programmes. Products receive parallel approvals for German national (VS-NfD through STRENG GEHEIM), EU, and NATO classifications.

    **Key vendors:**

    - **INFODAS** (Airbus subsidiary, founded 1974) -- SDoT product family built on the L4Re Microkernel OS. Approved up to DEU/EU/NATO SECRET. Products include the SDoT Security Gateway, SDoT Software Data Diode, SDoT Labelling Service (NATO Military Committee approved), and the SDoT Secure Network Card addressing supply chain security.
    - **genua** (Bundesdruckerei Group) -- over 450 employees, extensive BSI certifications. Products include the vs-diode, cyber-diode (OPC UA for Industry 4.0), genugate (first virtualised firewall with BSI CC EAL4+), genuscreen (quantum-resistant key exchange), and genuline (100 Gbit/s VPN). genuconnect deployed to 250,000+ users in the German Armed Forces.

??? info "France -- ANSSI and Eurydice"
    France has a strong national posture with ANSSI as the central authority.

    **ANSSI** operates CSPN (first-level certification), full Common Criteria evaluation, and a Qualification scheme (Standard, Reinforced, High).

    **Eurydice:** In a notable move, ANSSI released open-source software for transferring files through a physical data diode -- one of the few national-authority-released CDS tools available on GitHub.

    **Key vendor: Thales** -- ELIPS-SD network diode holds ANSSI approval up to *Tres Secret Defense*, NATO SECRET, and EU SECRET. Listed on NATO NIAPC.

??? info "Sweden -- Advenica and Saab"
    Sweden joined NATO in March 2024 (the newest member) and has a strong domestic CDS capability.

    **FMV/CSEC** operates Sweden's Common Criteria evaluation scheme.

    **Key vendors:**

    - **Advenica** -- six data diode products covering different form factors and speeds, plus ZoneGuard. Swedish Armed Forces approval with component assurance level N3 for SECRET/TOP SECRET. Common Criteria certified (March 2026).
    - **Saab/Clavister** -- TactiGate XD military-grade CDS listed on NATO NIAPC. TacEdge Trusted Router for NATO Federated Mission Networking.

??? info "Netherlands -- Fox-IT and Sentyron"
    The Netherlands has a significant CDS market.

    **NBV** (part of AIVD) evaluates and approves security products.

    **Key vendors:**

    - **Fox-IT** (NCC Group) -- Fox DataDiode achieves CC EAL7+ and NATO SECRET. One of the most widely deployed high-assurance data diodes globally.
    - **Sentyron** -- CC EAL7+ data diodes certified to NATO TOP SECRET. Recognised by the Dutch government as strategically vital.

??? info "Finland -- Insta"
    Finland joined NATO in April 2023 and has a domestic CDS capability.

    **Insta** -- Secure Gateway and Cross Domain Guard solutions approved by Traficom (Finland's NCSA) for data transfers up to security class II (SECRET). Data diode-based gateway solutions linking networks with different security classifications.

??? info "Denmark -- Arbit"
    Denmark's Centre for Cyber Security (CFCS) serves as an important NATO CDS accreditation authority.

    **Arbit** -- Danish-origin company with CC EAL7+ data diodes (BSI certified) accredited by CFCS to **NATO COSMIC TOP SECRET** -- the highest possible NATO certification. Strategic partnership with Leonardo (Italian defence group) since December 2024.

??? info "Turkey -- Indigenous development"
    Turkey pursues indigenous CDS capabilities, reducing dependence on foreign vendors.

    - **HAVELSAN** -- Data FLOW X data diode
    - **ASELSAN** -- Dumrul domestic critical infrastructure protection

    This aligns with Turkey's broader defence industry self-sufficiency strategy.

---

## NATO-Wide Policy

### Committee structure

NATO CDS policy operates through a layered committee structure:

**Digital Policy Committee (DPC)** -- formerly AC/322 / C3 Board
:   The senior multinational policy committee for NATO's digital initiatives. Renamed from C3 Board in December 2023. Reports directly to the North Atlantic Council. Nine Capability Panels support it, including the **Information Assurance and Cyber Defence Capability Panel** -- the body most directly responsible for CDS technical policy and the NIAPC framework.

**Security Committee (SC)**
:   Examines all NATO security policy. Operates in three configurations: Principals Level, Security Policy format, and **CIS Security (CISS) format** -- the last being directly relevant to CDS.

**Cyber Defence Committee (CDC)**
:   Political governance body for NATO cyber defence. Provides consultation and oversight but the technical CDS work falls under the DPC.

**Cyber Defence Management Board (CDMB)**
:   Working-level coordination of cyber defence across NATO civilian and military bodies.

### The AC/322-D Directive Series

The AC/322-D directives provide the technical implementation framework. Key directives:

| Directive | Subject | CDS relevance |
|-----------|---------|---------------|
| AC/322-D/0030-REV6 | CIS Interconnection | Core NATO standard for cross-domain connections |
| AC/322-D(2019)0041-REV1 | NIAPC establishment / COTS in NATO | Establishes the product catalogue |
| AC/322-D/0048-REV3 | CIS Security Requirements | Technical and administrative cybersecurity |
| AC/322-D(2021)0032 | Cloud Protection ("D32") | Framework for NATO information in commercial cloud |

??? question "What is D25 / D32?"
    "D25" and "D32" are **shorthand references to AC/322-D series directive serial numbers**, not committees or working groups. D32 refers to AC/322-D(2021)0032 -- the NATO Cloud Directive on protection of information in public cloud environments. "D25" likely refers to an earlier directive in the series; the precise mapping could not be confirmed through public sources.

### NIAPC

The **NATO Information Assurance Product Catalogue** is maintained by the NCI Agency and lists products evaluated by national authorities for use in NATO systems. Products are evaluated nationally (by BSI, NCSC, ANSSI, NIAP, CFCS, FMV/CSEC, and others) and then submitted for NIAPC listing. As of April 2026, the data diode category alone contains 26 products. See [Vendors and Products](vendors.md) for the full listing.

### NCI Agency (NCIA)

The NCI Agency is "NATO's technology and cyber hub." It acquires, deploys, and defends NATO communications systems. For CDS, NCIA:

- Maintains NATO's information assurance infrastructure
- Manages the NIAPC product catalogue
- Procures and deploys CDS solutions (e.g. the Information Clearing House -- Release Gateway)
- Operates NCIRC for cyber incident response
- Runs NATO's classified networks

The **ICH-RG (Information Clearing House -- Release Gateway)** is NCIA's cross-domain system for coalition information sharing, tested during NATO exercise Trident Juncture 2015.

### Federated Mission Networking (FMN)

FMN is NATO's framework for rapidly creating mission networks by federating capabilities from multiple nations. It grew from Afghanistan Mission Network experience and inherently creates cross-domain challenges -- mission networks connect capabilities from multiple nations at different classification levels.

### CWIX

The annual **Coalition Warrior Interoperability Exercise** tests cross-domain interoperability in practice. CWIX 2024 featured over 2,500 participants testing nearly 500 capabilities from 42 NATO nations and partners.

---

## Smaller Nations

### Southern Europe

- **Italy** -- ACN (Agenzia per la Cybersicurezza Nazionale) handles certification. Leonardo's strategic agreement with Arbit (December 2024) positions Italy in the NATO CDS market.
- **Spain** -- CCN (Centro Criptologico Nacional) publishes the extensive CCN-STIC guide series for classified system security.
- **Portugal** -- GNS explicitly references NATO AC/322-D directives in its product evaluation framework.
- **Greece** -- newly established NCSA (2024), still building regulatory framework.

### Baltics

- **Estonia** -- hosts NATO CCDCOE (Cooperative Cyber Defence Centre of Excellence) in Tallinn. Digital governance leader but limited CDS-specific infrastructure.
- **Latvia** -- creating a new National Cybersecurity Centre (from September 2024).
- **Lithuania** -- strong cyber defence focus given geopolitical position; part of Nordic-Baltic cooperation.

### South-East Europe

- **Romania** -- hosts NATO facilities including Deveselu missile defence. Growing cyber maturity but no domestic CDS vendors.
- **Bulgaria** -- updated Cybersecurity Act (February 2026) implementing NIS2. No domestic CDS vendors.
- **Croatia, Slovenia, Montenegro, North Macedonia, Albania** -- rely on NATO standards and allied support. Limited publicly documented CDS capabilities.

### Common pattern for newer members

Nations that joined NATO more recently share several characteristics:

1. Limited publicly documented CDS policy (either classified or not yet developed)
2. Reliance on NATO infrastructure and NIAPC-listed products
3. Growing cyber maturity driven by NIS2 implementation
4. No domestic CDS manufacturing capability
5. CDS requirements introduced through NATO accession processes

The maturity gradient tracks closely with NATO membership duration and national defence industrial base development.

---

## Key Observations

The CDS landscape reveals several structural patterns:

1. **Five Eyes dominance** -- the US and UK set the pace for CDS policy and evaluation. Other nations follow.
2. **Franco-German axis in Europe** -- Germany (INFODAS, genua, BSI) and France (Thales, ANSSI) drive continental European CDS standardisation.
3. **Nordic emergence** -- Sweden (Advenica, Saab), Finland (Insta), and Denmark (Arbit) have built significant CDS capabilities, amplified by recent NATO accession.
4. **Sovereignty trend** -- nations increasingly want CDS built and certified within their borders, especially at higher classification levels. This fragments the market further.
5. **Certification fragmentation** -- the lack of effective mutual recognition means vendors must pursue separate certification in each market. See [Certification and Evaluation](../standards/certification.md) for the cost and time implications.
