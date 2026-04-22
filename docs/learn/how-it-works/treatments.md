# Treatments and Transforms

In a CDS, a "treatment" is any processing step applied to data as it crosses a security boundary. A single transfer might pass through a dozen different treatments -- each one checking for a different category of risk, each one preparing the data for the next stage.

The NCSC compares this to airport security: multiple sequential checks, where each prepares the data for the next. Assurance is not gained at a single point but through the pipeline.

---

## The Treatment Pipeline

Data entering or leaving a security domain passes through a chain of treatments. The chain is directional -- different treatments apply to import flows (low-to-high) versus export flows (high-to-low) -- and it is ordered so that each stage can trust the output of the previous one.

### Import Pipeline (Low-to-High)

The primary concern is **integrity**: preventing malware, exploits, and corrupted content from entering the trusted domain.

1. **Transformation** (untrusted side) -- convert complex formats to simpler, verifiable ones
2. **[Protocol break](protocol-break.md) and flow control** -- terminate the connection, pass payload via simplified protocol
3. **Verification** (trusted side) -- syntactic and semantic checks on the now-simple content

The NCSC places the transformation engine on the untrusted side because "we assume that the transformation engine could be compromised." Complex, vulnerable parsing happens where compromise is least damaging.

### Export Pipeline (High-to-Low)

The primary concern is **confidentiality**: ensuring classified content is not disclosed.

1. **Release authorisation** -- determining whether the data may be released
2. **Release control** -- enforcing that only authorised data passes
3. **Content validation** -- keyword scanning, DLP, [security label](security-enforcement.md) checks
4. **Human review** (where required) -- manual inspection and approval

The design principle: security-critical controls should be "single purpose and suitably hardened," and the architecture should avoid a "monolithic technology stack providing multiple security functions."

---

## Treatment Types

### 1. Content Transformation

Converts complex, untrusted data formats into simpler, verifiable formats before they cross the boundary. The NCSC describes this as designed "with the aim of neutering any malicious code present in the content."

Nested content requires recursive handling: "Nested content should be un-packed, transformed if required, and verified."

### 2. Content Inspection and Verification

After transformation, the verification engine performs two kinds of checks:

- **Syntactic verification** ensures "the structure and syntax of the object are correct"
- **Semantic verification** ensures "the meaning is valid in the context of the operation or business process"

For structured data, the Isode M-Guard implements this through multiple rule mechanisms: XML Schema (protocol structure), XPath (element-level checks), Schematron (flexible rules), and Relax NG (modern XML specification).

### 3. Format Verification

The CDS must reliably determine what a file actually is and validate its structure. The NCSC requires that "content format should be verified robustly and consistently at each step."

!!! info "Hardware verification preferred"

    The NCSC states that software-only format verification is "usually unachievable for complex parsing software." Hardware specifically designed to verify simple data types is preferred. The hybrid approach uses hardware for structure validation and software to check that "each data field is valid for the end application."

    This is a remarkably strong statement from a national authority and has direct implications for choosing between hardware and software CDS.

### 4. Dirty Word Searching

Keyword scanning applies pre-defined word lists against outbound content to detect classified terms, codewords, or sensitive identifiers that should not cross the boundary. The NCSC describes this as one of several "heuristic measures, such as keyword scanning, data loss prevention tooling, or manual second-person review."

Dirty word searching is typically one treatment in a broader export chain -- necessary but not sufficient on its own.

### 5. Metadata Stripping and Enrichment

Export flows should not contain superfluous information: "Data exports should not contain any superfluous user data or machine generated information" except information "specifically required for the intelligibility of the exported data."

**Stripping** removes hidden metadata that could leak sensitive information: author names, revision history, tracked changes, GPS coordinates in images, internal network paths.

**Enrichment** adds required metadata: [security labels](security-enforcement.md), provenance markings, or handling instructions.

### 6. Security Label Checking

The CDS checks data labels against catalogues of allowed labels to enforce [information flow policy](security-enforcement.md). The Isode M-Guard supports STANAG 4774/4778 NATO Confidentiality Metadata Labels, checking labels to "prevent leak of sensitive data."

Label checking is the mechanism that connects the data's classification to the policy about what can cross the boundary.

### 7. Content Disarm and Reconstruct (CDR)

CDR is the content-level complement to [protocol break](protocol-break.md). Where protocol break strips and reconstructs the transport layer, CDR strips and reconstructs the content itself.

CDR "does not determine or detect malware's functionality" -- instead, it takes a preventive approach by removing all unapproved components regardless of whether they are known to be malicious. It deconstructs files, removes elements that do not match the file type's standards or set policies, then rebuilds clean versions.

**Three levels of CDR:**

| Level | Approach | Security | Fidelity |
|---|---|---|---|
| **Level 1** | Flatten and convert to PDF | Maximum | Lowest -- original format lost |
| **Level 2** | Strip active content, preserve file type | High | Good -- format preserved, macros removed |
| **Level 3** | Eliminate all risk, preserve type, integrity, and active content | Good | Highest -- content and structure preserved |

CDR is effective against zero-day vulnerabilities because it removes all potentially malicious code rather than relying on known threat signatures. Glasswall's implementation "does not depend on malware signatures or prior knowledge of threats."

!!! info "CDR is being absorbed into the guard pipeline"

    CDR was briefly a standalone product category. It is now being integrated directly into [guard](../three-types.md) products as a treatment stage. Everfox's acquisition of Deep Secure exemplifies this trend. When evaluating CDS, look for CDR as part of the guard rather than as a separate product.

### 8. Schema Validation

For structured data (XML, JSON, database records), schema validation ensures conformance to an expected structure. The M-Guard implements this through configurable Application Profiles with sets of rule catalogues. Each guard instance can enable rules from loaded catalogues, providing flexible, per-deployment validation.

### 9. Image and Document Sanitisation

Image sanitisation re-encodes images to strip steganographic content and embedded metadata. Document sanitisation removes macros, scripts, embedded objects, and active content. The NCSC requires that "verification components should ensure all potentially active content has been removed."

### 10. Anti-Covert-Channel Treatment

Covert channels exploit encoding variants, timing differences, or other subtle signals to smuggle information across a boundary. The M-Guard applies content normalisation to guard "against covert channels and attacks using encoding variants." Rate limiting also reduces covert channel bandwidth by providing a "mechanism to limit the rate of messages."

### 11. Redaction

Redaction removes or masks specific portions of content before release. This is closely related to human review for high-to-low transfers, where a trained reviewer examines, redacts classified content, and approves release. Automated redaction can handle simpler cases based on pattern matching or label-driven rules.

### 12. Business Rule Checks

Beyond structural and security checks, guards can enforce business-level rules that validate whether content makes sense in the operational context. For example: checking that a message's subject matter matches the originator's authorised topics, or that database query results fall within expected value ranges.

---

## Treatment Summary Table

| Treatment | Primary Direction | Purpose |
|---|---|---|
| Content transformation | Import | Convert complex formats to simple, verifiable ones |
| Content inspection / verification | Both | Syntactic and semantic correctness checks |
| Format verification | Import | Confirm file type and validate structure |
| Dirty word searching | Export | Detect classified terms and codewords |
| Metadata stripping | Export | Remove hidden information that could leak |
| Metadata enrichment | Both | Add required labels and handling markings |
| Security label checking | Both | Enforce [information flow policy](security-enforcement.md) based on classification |
| CDR | Import | Deconstruct and rebuild files to known-good standard |
| Schema validation | Both | Validate structured data against expected schemas |
| Image / document sanitisation | Import | Remove steganographic content, macros, active content |
| Anti-covert-channel | Both | Normalise content and limit rates to prevent signalling |
| Redaction | Export | Remove or mask classified portions |
| Business rule checks | Both | Validate operational context and business logic |

---

## Policy-Driven Treatment Selection

Treatment selection is driven by security policy, which itself reflects the threat model and risk appetite. The NCSC guidance is to "transfer only what is necessary to achieve the required business outcomes" and to "choose simple protocols and strip unneeded information where possible."

In practice, the treatment pipeline is configured per data type and per flow direction. Different file types need different treatments. An incoming Office document needs CDR and active content removal. An outgoing intelligence report needs dirty word search, label checking, metadata stripping, and possibly human review. A streaming sensor feed needs lightweight format verification and rate limiting.

The [security enforcement](security-enforcement.md) mechanisms -- labels, access control, release authority -- govern *what* gets through. The treatments govern *how* it gets processed on the way.

For the protocol-level foundation that these treatments build on, see [Protocol Break](protocol-break.md). For the formal models governing data flow direction, see [Data Flow Models](data-flow.md).
