# Security Enforcement

A CDS is only as useful as the policy it enforces. This page covers the mechanisms that make enforcement work: security labels that describe what the data is, access control models that decide what can go where, release authority that governs who approves transfers, human review that handles ambiguity, and audit that keeps everything accountable.

---

## Security Labels

Security labels are machine-readable metadata tags attached to data objects that encode their classification, handling caveats, and release constraints. Without them, a CDS cannot make automated policy decisions about what may cross a boundary.

### STANAG 4774: Label Syntax

NATO's STANAG 4774 (ADatP-4774) defines the standard syntax for confidentiality labels. It supports XML, JSON, and CBOR encodings, and a label contains:

Policy Identifier
:   A globally unique OID identifying which security policy applies (e.g., the NATO security policy, or a national policy).

Classification
:   One of UNCLASSIFIED, RESTRICTED, CONFIDENTIAL, SECRET, or TOP SECRET.

Categories
:   Zero or more category values, each with a type:

    - **Restrictive** -- the recipient must hold ALL listed categories
    - **Permissive** -- the recipient needs just ONE (e.g., "Releasable To: AUS, GBR, USA" -- any of those nations may receive it)
    - **Informative** -- displayed but not enforced

Lifecycle metadata
:   Creation date and mandatory review date.

??? example "Example STANAG 4774 label (XML)"

    ```xml
    <ConfidentialityLabel
        xmlns="urn:nato:stanag:4774:confidentialitymetadatalabel:1:0"
        ReviewDateTime="2034-04-18T09:26:56Z">
      <ConfidentialityInformation>
        <PolicyIdentifier
            URI="urn:oid:1.3.6.1.4.1.31778.102.25">
          CWIX25
        </PolicyIdentifier>
        <Classification>SECRET</Classification>
        <Category
            URI="urn:oid:1.3.6.1.4.1.31778.103.15"
            Type="PERMISSIVE"
            TagName="Releasable To">
          <GenericValue>AUS</GenericValue>
        </Category>
      </ConfidentialityInformation>
      <CreationDateTime>2025-04-18T09:27:52Z</CreationDateTime>
    </ConfidentialityLabel>
    ```

    This label marks data as SECRET under the CWIX25 exercise policy, releasable to Australia.

### STANAG 4778: Metadata Binding

STANAG 4778 (ADatP-4778) defines how labels are bound to the data they protect:

- **Protocol-specific bindings** -- defines how labels attach in different protocols (SMTP binding in ADatP-4778.2, XMPP binding, etc.)
- **Portion marking** -- multiple labels can be associated with different portions of the same data object
- **Cryptographic binding** -- digital signatures verify that the label is valid, correctly associated with the data, that the content has not been modified since binding, and that the originator can be identified

!!! info "STANAGs 4774 and 4778 are the closest thing to a universal CDS standard"

    For security labelling, these two NATO standards are the most widely referenced across all national approaches. They are not universally implemented, but they represent the strongest candidate for international CDS interoperability. If you are designing a CDS and want to future-proof for coalition operations, use STANAGs 4774/4778.

### Other Label Standards

**IC-ISM (Intelligence Community Information Security Marking):** The US Intelligence Community standard for security marking metadata, defined as XML Schema. Encodes classification, dissemination controls, releasability, and handling caveats for intelligence products.

**ESS Security Labels (RFC 2634):** Used in S/MIME email and STANAG 4406 military messaging. Structure parallels STANAG 4774. STANAG 4774 is "expected to become the dominant label format for military use."

---

## How a CDS decides what gets through

Every CDS enforces access control -- the system decides whether data may cross the boundary, and that decision isn't optional or overridable. But the way that decision gets made has evolved.

### The basics: mandatory access control

The simplest form: compare the data's classification against the destination domain's clearance. SECRET data can't flow to an UNCLASSIFIED network. This is mandatory access control (MAC) -- the system enforces the [Bell-LaPadula and Biba rules](../three-types.md) regardless of what any user wants. It's the opposite of discretionary access control (where users decide who gets access to their files).

MAC works well for straightforward high/low boundaries. But real-world CDS decisions are rarely that simple -- data might be SECRET but also releasable only to specific nations, restricted to a particular programme, or carrying caveats that limit its handling.

### Adding attributes: ABAC

Attribute-Based Access Control goes beyond simple classification hierarchies. Instead of just asking "is this SECRET?", the system considers multiple attributes of the data, the recipient, and the context:

- **Data attributes** -- classification, releasability (e.g. "Releasable To: AUS, GBR, USA"), programme caveats, content type
- **Recipient attributes** -- clearance level, nationality, programme membership, role
- **Context** -- which network, what time, what operational context

The STANAG 4774 category mechanism already supports this -- categories can encode nationality restrictions, programme membership, and other attributes beyond simple classification. In practice, most modern CDS policy decisions are ABAC even when the underlying label infrastructure looks like traditional MAC.

For a deeper look at how ABAC works with security labels and data-centric security, see [Data-Centric Security on AWS](https://iamtgray.github.io/dcs-data-exchange/).

### NATO's model: CMBAC

NATO formalises this as CMBAC (Confidentiality Metadata-Based Access Control, pronounced "come back"), defined in STANAG 4474. It works by comparing [STANAG 4774 security labels](#stanag-4774-label-syntax) against security clearances within the context of a Security Policy Information File (SPIF) -- essentially a machine-readable rulebook that defines what each label means and who may receive data carrying it.

The SPIF handles label structure definitions, display markings (including multilingual support), access control rules, and policy equivalence mappings for cross-domain scenarios (i.e. translating between different national security policies at a coalition boundary).

---

## Release Authority and Control

The NCSC distinguishes two related concepts for export:

Release Authorisation
:   Deciding whether data *can* be released. This is a policy decision -- who has the authority to approve the release of specific data to a specific destination?

Release Control
:   Enforcing that *only* authorised data actually passes. This is a technical enforcement mechanism -- ensuring that the release decision is faithfully implemented.

The CDS must ensure that "release of the data is appropriately authorised." This authorisation can come from "the originating user or other authorising users" and must "take into account business processes required to sanction information release."

A strong binding between authorisation and release is required to ensure "only approved data is released outside of the system." For transactional protocols, the CDS must also "correlate requests and responses" to prevent a response passing "without a corresponding request of the correct type."

---

## The Human Review Spectrum

The level of human involvement in release decisions depends on the risk profile of the transfer. In practice, it is a spectrum:

| Approach | How it works | When used |
|---|---|---|
| **Fully automated** | Security labels unambiguously permit release; data format verified and sanitised; policy rules are deterministic | Low-risk, well-labelled structured data; high-volume flows |
| **Automated with flagging** | Automated checks process most transfers; anomalies flagged for human review | Medium-risk flows where most data is routine |
| **Human-in-the-loop** | Trained reviewer examines, redacts, and approves each transfer | High-risk high-to-low transfers; complex or ambiguous content |
| **Fully manual** | Every transfer requires explicit human approval | Highest-risk transfers; TOP SECRET to lower domains |

Transfer criteria range from "simple (antivirus + whitelist) to complex (multiple filters + human review + redaction + approval)."

!!! warning "Human review has limits"

    Human reviewers are subject to fatigue, error, and insider threat. The research identifies this as an open problem with no near-term solution. ML-assisted triage is emerging but is not yet trusted by accreditors for autonomous decision-making in certified CDS. For high-volume flows, the practical reality is that fully manual review does not scale.

---

## Audit and Monitoring

The NCSC states that monitoring a CDS is "key to maintaining the security of the solution and the integrity of the information it protects." Seven monitoring areas are defined:

1. **Configuration changes** -- any changes to system configuration should be monitored
2. **Export monitoring** -- all exports monitored to detect unauthorised releases and to clarify what data has been exfiltrated in the event of compromise
3. **Import monitoring** -- all imports monitored to detect malicious content
4. **Component failures** -- errors and failures monitored to identify potential attacks
5. **Process execution** -- unusual behaviour in components handling complex data could indicate an attack
6. **External boundary network monitoring** -- detect inbound connections from unknown sources
7. **Internal network monitoring** -- detect unauthorised outbound connection attempts indicating a compromised host

The updated NCSC design principles add that "data flows can be clearly traced end-to-end" with operations recorded and session information correlated back to initiators.

The CDS should feed audit data to the organisation's SIEM system. Comprehensive logging of all transfers -- who sent what, when, to where, what [treatments](treatments.md) were applied, what the release decision was -- creates the evidence chain for both operational monitoring and forensic reconstruction.

---

## Non-Repudiation

Non-repudiation ensures that a transfer cannot be denied after the fact. CDS achieve this through several mechanisms:

**Cryptographic binding (STANAG 4778):** Digital signatures on security label bindings provide verification that the label was validly created, proof of correct association between label and data, content integrity assurance, and originator validation.

**Audit trails:** Comprehensive logging of all transfers creates an evidence chain. Export monitoring specifically supports forensic reconstruction of "what data has been ex-filtrated in the event of a compromise."

**Mutual authentication:** Strong peer authentication validates both sides of a CDS connection. The M-Guard requires "two way strong authentication to validate peers" and "will always validate IP address of connecting application."

**Certification and accreditation:** US cross-domain systems must be certified via Common Criteria. EAL 5 or higher is required to export from a SECRET domain to an UNCLASSIFIED domain. The NCDSMO maintains the baseline list -- systems must be on this list before DoD deployment.

---

For how security labels and access control interact with the [treatment pipeline](treatments.md), and for the [protocol-level mechanisms](protocol-break.md) that enforce flow control, see the companion pages. For the formal models that govern data flow direction, see [Data Flow Models](data-flow.md). For the different CDS types and their security arguments, see [The Three Types of CDS](../three-types.md).
