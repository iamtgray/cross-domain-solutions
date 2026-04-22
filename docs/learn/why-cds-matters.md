# Why CDS Matters

Organisations segregate their networks into security domains for good reason: to protect information at different classification levels. But segregation creates a fundamental tension -- people need to share information across those boundaries to do their jobs.

CDS exists to resolve that tension.

---

## The Information Sharing Dilemma

Without CDS, organisations face a binary choice:

**Total isolation** -- networks stay air-gapped. No information flows. Operational effectiveness suffers. People resort to manual workarounds: USB sticks, re-typing data, printing and scanning. These workarounds introduce their own security risks and are rarely audited.

**Direct connection** -- networks are connected via standard networking. Classified systems are exposed to attack and data exfiltration. The boundary between SECRET and UNCLASSIFIED becomes a firewall rule away from catastrophe.

CDS provides a third path: **controlled, policy-mediated connectivity** that allows information to flow while enforcing security rules about what can cross, in which direction, and under what conditions.

Think of it as a spectrum:

| | Total Isolation | CDS | Direct Connection |
|---|---|---|---|
| **Information sharing** | None | Controlled | Unrestricted |
| **Security** | Maximum (in theory) | High, policy-enforced | Minimal |
| **Operational effectiveness** | Poor | Good | Best (until breach) |
| **Workaround risk** | High (USB, sneakernet) | Low | N/A |
| **Auditability** | None (workarounds invisible) | Full (every transfer logged) | Partial |

The NCSC explicitly warns that poorly designed CDS implementations cause users to seek "less secure alternatives such as USB sticks." A CDS must be usable enough that people actually use it, rather than working around it.

---

## Real-World Contexts

### Intelligence sharing

The original and still primary use case. Intelligence agencies operate networks at multiple classification levels -- UNCLASSIFIED, SECRET, TOP SECRET, plus compartments and caveats. Analysts need to pull open-source information up into classified environments, push finished intelligence products down to consumers with lower clearances, and share across compartments with different need-to-know restrictions.

### Coalition operations

Allied nations conducting joint military operations need to share operational data in real time, but each nation has its own classification system and release rules. CDS enforces national release policies -- markings like "REL TO USA, GBR, AUS" -- while enabling the speed that time-critical operations demand.

### Critical infrastructure (IT/OT convergence)

The convergence of IT and operational technology creates an acute need for controlled connectivity:

- **Nuclear facilities** -- the US NRC mandates data diodes for nuclear plant networks
- **Railways** -- French ANSSI (2013) mandated that only unidirectional technology is permitted for connecting Class 3 railway networks; firewalls are explicitly forbidden
- **Energy, water, manufacturing** -- SCADA monitoring data must flow to IT networks for analytics without creating attack paths back to safety-critical control systems
- **Aviation** -- flight control systems must be separated from passenger infotainment systems

### Enterprise

CDS patterns are increasingly adopted outside government:

- **Financial services** -- protecting trading systems while maintaining market data feeds
- **Legal** -- protecting client-privileged information while enabling controlled sharing with courts and regulators
- **Healthcare** -- protecting patient data under GDPR and HIPAA while enabling clinical and research data sharing

---

## What Happens Without CDS

The consequences of getting cross-domain wrong fall into two categories, and both are severe:

!!! danger "Confidentiality failure"

    Classified information reaches adversaries. Sources and methods are compromised. National security is damaged. In the intelligence context, people can die.

!!! danger "Integrity failure"

    Malware or hostile content enters a protected network. In a critical infrastructure context, this means attacks reaching safety-critical control systems -- power grids, water treatment, nuclear facilities, railway signalling.

The dual nature of the threat is important. Historically, CDS focused on preventing data *leaving* classified domains (confidentiality). Increasingly, the threat of data *entering* classified domains -- malware, exploits, hostile content -- is equally concerning. This is why modern CDS deploy [Content Disarm and Reconstruct (CDR)](how-it-works/treatments.md) as a core treatment in import pipelines.

---

## Growth Drivers

The CDS market is substantial and growing fast.

!!! info "Market size"

    - **2025 market size:** $3.75 billion
    - **2031 forecast:** $7.29 billion
    - **CAGR (2026--2031):** 11.45%
    - **Aerospace and defence share:** 49.64% of the market
    - **Top 5 vendors:** ~55% market share

    Source: Mordor Intelligence

Four factors are driving growth:

Escalating classified data flows
:   More data needs to move between classification levels. The volume of cross-domain transfers is increasing across every sector.

Zero Trust mandates
:   US Executive Order 14028 and the DoD Zero Trust Strategy require explicit domain boundary controls. CDS implements continuous verification at domain boundaries -- exactly what Zero Trust demands. See [Protocol Break](how-it-works/protocol-break.md) for how CDS achieves this.

AI/ML data pipelines
:   Training and inference pipelines increasingly need to cross security boundaries -- pulling training data from lower-classification sources into higher-classification environments, or pushing model outputs down to operational consumers.

Cloud migration
:   Government adoption of commercial cloud (AWS GovCloud, Azure Government) creates new CDS demand. Cloud CDS is the fastest-growing deployment model at 12.55% CAGR, though it remains the least mature segment with few certified products.

---

## The Stakes

CDS sits at the intersection of two competing imperatives: the need to protect classified information and the need to share it. Get the balance wrong in either direction and the consequences are serious.

Too much restriction, and people cannot do their jobs. They find workarounds. Those workarounds are unaudited, uncontrolled, and often less secure than no CDS at all.

Too little restriction, and classified data leaks. Malware enters protected networks. Safety-critical systems are compromised.

The field exists because both extremes are unacceptable. The [three types of CDS](three-types.md) -- access, transfer, and multi-level -- each represent a different approach to striking that balance, with different trade-offs in security assurance, operational flexibility, and cost.
