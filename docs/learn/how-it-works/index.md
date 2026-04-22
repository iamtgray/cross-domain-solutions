# How CDS Works

This section covers the core mechanisms that make a cross-domain solution work -- from the formal models that govern data flow to the specific security treatments applied to every piece of data that crosses a boundary.

**[Data Flow Models](data-flow.md)** -- Unidirectional, bidirectional, and multi-level flows. The Bell-LaPadula and Biba models explained in plain English. Store-and-forward versus real-time processing. Interaction patterns including publish-subscribe, request-response, and streaming.

**[Protocol Break](protocol-break.md)** -- The foundational security mechanism in any CDS. Why terminating and reconstructing network protocols at the boundary is non-negotiable, how it works for different protocols (HTTP, SMTP, XMPP, database), and its relationship with Content Disarm and Reconstruct.

**[Treatments and Transforms](treatments.md)** -- The pipeline of security processing that data passes through: content transformation, format verification, dirty word search, CDR, security label checking, schema validation, and more. Import pipelines versus export pipelines, and how treatments are chained.

**[Security Enforcement](security-enforcement.md)** -- How CDS enforce policy in practice: security labels (STANAG 4774/4778), mandatory and attribute-based access control, release authority, the human review spectrum, and audit and monitoring requirements.
