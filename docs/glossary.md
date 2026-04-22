# Glossary

Key terms used across the cross-domain solutions field, organised by theme. For deeper coverage of any topic, follow the cross-references to relevant site pages.

---

## Security Models

Bell-LaPadula Model (BLP)
:   A formal state-machine model (1973) addressing **confidentiality**. Enforces "no read up, no write down" -- subjects cannot read above their clearance or write below their level. The foundational model for all CDS design. [Data diodes](learn/hardware/data-diodes.md) enforce the star property (no write down) in hardware.

Biba Model
:   A formal model (1977) addressing **data integrity** -- the inverse of BLP. Enforces "no read down, no write up." Underpins import path design in CDS: incoming data from untrusted networks must be sanitised to maintain the integrity of the higher domain. CDR is essentially a Biba integrity mechanism.

Clark-Wilson Model
:   An integrity model (1987) using well-formed transactions rather than simple read/write rules. Data enters as an Unconstrained Data Item, is transformed by inspection and sanitisation (Transformation Procedures), and exits as verified. Maps well to how [guards](learn/hardware/guards.md) process data.

Mandatory Access Control (MAC)
:   Access control enforced centrally by the system, not overridable by users. Security labels on subjects and objects are checked by the kernel for every access attempt. Requires an "extremely high degree of robustness." Implemented in SELinux (NSA, open-sourced 2000), AppArmor, and separation kernels.

Discretionary Access Control (DAC)
:   Access control where users decide whether to grant access to others. Contrasts with MAC -- DAC tolerates risk management at user discretion, while MAC enforces risk avoidance. CDS environments typically use MAC for cross-domain enforcement.

Information Flow Control
:   The formal study of rules governing how information moves through a system. Key concepts include explicit flows (direct assignment), implicit flows (leakage through control structures), side channels (timing, power), noninterference (attacker cannot distinguish computations), and declassification (controlled release from high to low).

Tranquility Principle
:   The principle that security classifications do not change while being referenced. **Strong tranquility:** levels never change during operation. **Weak tranquility:** levels may change but never in a way that violates policy. Relevant to how CDS handle data during processing.

---

## Architecture Components

Cross-Domain Solution (CDS)
:   A form of controlled interface that provides the ability to manually or automatically access and transfer information between different security domains. Three types: access solutions, transfer solutions, and multi-level solutions. See [What is a CDS?](learn/what-is-a-cds.md) and [The Three Types](learn/three-types.md).

Cross-Domain Guard
:   A device that allows communication between separate networks subject to configured constraints. Unlike firewalls, guards control information at the **business level** (content and meaning, not just ports and protocols) and provide **assurance of effectiveness under attack and failure**. See [Guards](learn/hardware/guards.md).

Data Diode
:   A network device that allows data to travel in **only one direction**, enforced by hardware -- typically modified fibre-optic links with transmit or receive transceivers physically disconnected. No reported cases of data diodes being bypassed. See [Data Diodes](learn/hardware/data-diodes.md).

Separation Kernel
:   Hardware, firmware, or software whose primary function is to establish, isolate, and separate multiple partitions and control information flow between them. Each partition appears to be a separate, isolated machine. The foundational layer for MILS architectures. First certified product: INTEGRITY-178B (2008). Formally verified implementations include seL4 and Muen.

Protocol Break
:   The termination and re-creation of a network protocol at a security boundary. Rather than passing packets through, the CDS terminates the inbound session, extracts data, performs security checks, and creates a new session on the other side. Prevents protocol-level attacks from traversing the boundary. See [Protocol Break](learn/how-it-works/protocol-break.md).

High Assurance Guard (HAG)
:   A multilevel security device for communicating between different security domains (e.g. NIPRNet to SIPRNet). Runs multiple subsystems for different classification levels. Requires an evaluated mandatory integrity policy, a multilevel kernel, and [Common Criteria](learn/standards/international.md) evaluation at EAL5+ for SECRET-to-UNCLASSIFIED export.

Assured Pipeline
:   A data transfer mechanism incorporating multiple stages of security processing (format verification, content inspection, malware scanning, policy enforcement) in a defined sequence. Designed so that failure of one stage does not allow data to bypass subsequent stages.

Multilevel Security (MLS)
:   Storing data at multiple classification levels in a single system with label-based access control. True MLS requires a highly trusted operating system. Contrasts with Multiple Single Level (MSL) where each level is physically separated. See [The Three Types](learn/three-types.md).

Multiple Independent Levels of Security (MILS)
:   A high-assurance architecture using separation kernels to create isolated partitions with controlled information flows. Must satisfy NEAT properties: Non-bypassable, Evaluatable, Always-invoked, Tamperproof.

Defence in Depth
:   A layered security approach where multiple independent mechanisms are applied so that failure of one does not compromise overall security. NCSC explicitly recommends this for CDS design.

Trusted Computing Base (TCB)
:   The totality of protection mechanisms (hardware, firmware, software) responsible for enforcing security policy. For CDS and MLS systems, the TCB must be trustworthy because all information is physically accessible to the operating system.

---

## Content Processing

Content Inspection
:   Examination of data content (not just metadata) as it crosses a security boundary. Includes format verification, schema validation, dirty word searches, and deep content inspection. See [Treatments and Transforms](learn/how-it-works/treatments.md).

Deep Content Inspection (DCI)
:   Thorough examination of internal file structure and content, going beyond metadata checks. Parses files to component elements, verifies internal consistency, checks embedded objects, and analyses all nesting levels. Examines what a file *is*, not just what it *claims to be*.

Content Disarm and Reconstruct (CDR)
:   Technology that removes potentially malicious code from files by deconstructing them, removing elements that do not match standards or policy, and rebuilding clean versions. Three levels: basic (flatten to PDF), intermediate (strip active content, preserve format), advanced (eliminate risk while maintaining file type and active content). See [Treatments and Transforms](learn/how-it-works/treatments.md).

Sanitisation
:   Removing or neutralising dangerous or sensitive content from data before it crosses a security boundary. Broader than CDR -- encompasses stripping metadata, removing macros, converting formats, redacting classified content, and neutralising active code.

Threat Stripping
:   Removing entire categories of potentially dangerous content (all macros, all scripts, all active content) regardless of whether they are actually malicious. The "assume everything is hostile" approach. Closely related to CDR.

Format Verification
:   Checking that a file's actual internal structure matches its claimed type. A file claiming to be JPEG should contain valid JPEG data, not executable code with a .jpg extension. Prevents format-confusion attacks.

Schema Validation
:   Verifying that structured data (XML, JSON, database records) conforms to an expected schema before crossing a boundary. Prevents injection attacks and structure manipulation.

Dirty Word Search
:   Scanning text against a blacklist of phrases indicating potentially classified content. Basic but widely used, typically supplemented by more sophisticated inspection methods.

---

## Security Labels and Classification

Security Domain
:   An environment that implements a security policy and is administered by a single authority. A set of assets and resources subject to a common security policy. CDS exist at the boundaries between security domains. See [What is a CDS?](learn/what-is-a-cds.md).

Classification Level
:   A hierarchical designation (UNCLASSIFIED, OFFICIAL, SECRET, TOP SECRET) indicating information sensitivity and the damage from unauthorised disclosure. Different nations use different schemes. See [Classification Systems](learn/standards/classification.md).

Security Label
:   Metadata attached to information specifying classification level, caveats, and releasability. Used by MAC systems and CDS guards to make access and transfer decisions. See [International Standards](learn/standards/international.md) for STANAG 4774/4778 and RFC 2634.

Data Marking
:   Applying security metadata to information objects -- classification level, caveats, releasability, handling instructions, originator information. May be embedded in metadata, applied as visual markings, or encoded in structured labels.

Caveat
:   An additional restriction alongside classification that limits access based on nationality, programme membership, or need-to-know. Examples: SCI compartments (US), STRAP markings (UK), codewords. Two documents may share a classification level but have different caveats requiring different handling. See [Classification Systems](learn/standards/classification.md).

Releasability
:   A marking specifying which nations, organisations, or groups may receive the information. Example: "REL TO USA, GBR, AUS, CAN, NZL" (Five Eyes). CDS enforce releasability by checking markings against destination domain authorisation.

High Side
:   The network or domain at the higher classification level in a cross-domain connection. Contains more sensitive information with stricter access controls.

Low Side
:   The network or domain at the lower classification level. May be unclassified, lower-classification, or a less-trusted external network.

Downgrading
:   Controlled release of information from a higher classification domain to a lower one. Requires verification that content does not exceed the destination domain's ceiling. May involve automated inspection, human review, or both.

Upgrading
:   Transferring information from a lower domain to a higher one. Less risky for confidentiality (data moves to more protection) but introduces integrity risks -- the higher domain must trust the incoming data is not malicious.

---

## Assurance and Accreditation

Accreditation
:   The formal process of approving a CDS for operational use in a specific deployment. Considers the product, its configuration, the environment, threats, and residual risk. Distinct from certification (which is product-focused). See [Certification and Evaluation](learn/standards/certification.md).

Certification
:   Evaluation that a CDS product meets defined security requirements (e.g. Common Criteria, NCSC PBA). Product-focused. A certified product may never be deployed; deployment requires accreditation. See [Certification and Evaluation](learn/standards/certification.md).

High Assurance
:   Confidence sufficient for protecting the most sensitive information. Requires rigorous development, formal methods, independent testing, and evaluation at EAL5 or above (or equivalent).

Medium Robustness
:   Assurance below high assurance, typically EAL3 or EAL4. Suitable for lower-risk CDS -- connections between peer networks at the same classification but different caveats, or small classification differentials.

Common Criteria
:   International standard (ISO/IEC 15408) for evaluating IT security products. EAL1 (lowest) through EAL7 (highest). CDS typically require EAL4+ to EAL7 depending on the [classification pair](learn/standards/classification.md). See [International Standards](learn/standards/international.md).

Lab-Based Security Assessment (LBSA)
:   Rigorous testing mandated by the US [Raise the Bar](learn/standards/certification.md) initiative. Subjects CDS to vulnerability analysis and penetration testing in a controlled laboratory. Goes beyond standard CC evaluation.

Release Authority
:   The person or role authorised to approve information release from one domain to another. For automated CDS, the release authority defines the policies enforced. For human-review guards, a trained reviewer acts as release authority for each transfer.

Digital Policy / Release Policy
:   Machine-readable rules that a CDS enforces when determining whether a transfer is permitted. Range from simple (antivirus scan + whitelist) to complex (multiple content filters + mandatory human review + redaction).

Data Spillage
:   Unauthorised transfer of classified information to a domain not authorised to hold it. An ever-present concern in CDS design. Remediation requires identifying all copies, removing them, and potentially re-accrediting affected systems.
