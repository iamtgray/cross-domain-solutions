# Software CDS Architectures

This page covers the concrete architectural patterns for building software-based cross-domain solutions -- from traditional virtualised guards through to cloud-native patterns, data mesh, and Zero Trust integration. Each pattern has different assurance, scalability, and deployment characteristics.

For the foundational considerations that inform these architectural choices (OS security, virtualisation limitations, attack surface), see [key considerations](considerations.md).

## Virtualised guard architecture

The traditional software guard architecture uses virtualisation to host CDS components in isolated VMs on a separation kernel or hardened hypervisor:

```
+------------------------------------------------------------------+
|  Separation Kernel / Type-1 Hypervisor                           |
|                                                                  |
|  +----------------+  +------------------+  +------------------+  |
|  | LOW Domain VM  |  | Guard VM         |  | HIGH Domain VM   |  |
|  | (untrusted     |  | (content inspect,|  | (trusted         |  |
|  |  network facing)|  |  policy enforce) |  |  network facing) |  |
|  +----------------+  +------------------+  +------------------+  |
|         |                    |                      |            |
|         +--- controlled ---->+<--- controlled ------+            |
|              info flow           info flow                       |
+------------------------------------------------------------------+
|  Hardware (dedicated or shared)                                  |
+------------------------------------------------------------------+
```

Each VM runs a single security function. The separation kernel (LynxSecure, INTEGRITY, seL4-based, or PikeOS) mediates all inter-VM communication through formally defined information flow channels. Applications are immutably allocated the minimum resources required to perform their task.

Key properties:

- Each VM has its own OS instance (potentially different OSes for different roles -- supporting OS heterogeneity for [Raise the Bar](../standards/certification.md) compliance)
- The guard VM performs content checking and policy enforcement
- Information flow between VMs is controlled by the separation kernel, not by any guest OS
- Compromise of one VM does not compromise others, assuming kernel correctness

This is the highest-assurance software CDS architecture. It is the software analogue of a [hardware guard](../hardware/guards.md) with physically separate subsystems.

## Containerised guard architecture

A lighter-weight approach using containers within a hardened host:

```
+------------------------------------------------------------------+
|  Hardened Host OS (SELinux/AppArmor mandatory)                   |
|                                                                  |
|  +-------------+  +---------------+  +------------------------+  |
|  | Ingress     |  | Content       |  | Egress                 |  |
|  | Container   |  | Inspection    |  | Container              |  |
|  | (protocol   |  | Container(s)  |  | (protocol rebuild,     |  |
|  |  termination)|  | (AV, DLP,    |  |  delivery)             |  |
|  |             |  |  format check)|  |                        |  |
|  +-------------+  +---------------+  +------------------------+  |
+------------------------------------------------------------------+
```

This trades assurance for operational flexibility:

- Easier to deploy, scale, and update individual components
- Kubernetes orchestration can manage guard component lifecycle
- But containers share the host kernel -- a kernel compromise defeats all isolation
- Suitable for lower-sensitivity boundaries (OFFICIAL to OFFICIAL-SENSITIVE, or between business domains)

!!! warning "Container isolation is not domain separation"

    Containers are not sufficient as the primary isolation boundary between different security classifications. They share the host kernel, and container escape vulnerabilities have been demonstrated repeatedly. See the [container isolation assessment](considerations.md) for details. Use containers for defence-in-depth within a domain, not as the security boundary between domains.

## Microservices-based CDS

A microservices architecture decomposes the CDS into independently deployable services:

| Service | Responsibility | Notes |
|---------|---------------|-------|
| **Protocol Terminator** | Terminates inbound protocol, extracts payload | One per supported protocol (HTTP, SMTP, etc.) |
| **Content Transformer** | Converts complex formats to simple verifiable ones | Should run on the low (untrusted) side -- parsing untrusted content is inherently risky |
| **Content Verifier** | Validates transformed content against policy | Runs on the trusted side after the [protocol break](../how-it-works/protocol-break.md) |
| **Policy Engine** | Evaluates cross-domain transfer rules | Attribute-based or label-based decisions |
| **Audit Logger** | Records all transfer decisions and content metadata | Must be tamper-evident |
| **Protocol Rebuilder** | Reconstructs outbound protocol with verified payload | Clean rebuild, not pass-through |

Benefits: each service can be developed, tested, and certified independently. Services can be scaled independently (content inspection is typically the bottleneck). Different languages per service (Rust for parsers, Go for orchestration). Failure isolation means one service crash does not take down the guard.

Risks: inter-service communication becomes an attack surface. Service mesh complexity increases the TCB. Harder to evaluate as a complete system. The orchestration layer (Kubernetes) becomes security-critical.

## API gateway as enforcement point

API gateways can serve as a software CDS enforcement point for API-mediated cross-domain access:

```
Domain A  --->  API Gateway (CDS)  --->  Domain B
                    |
                    +-- Authentication / Authorisation
                    +-- Content inspection
                    +-- Rate limiting
                    +-- Schema validation
                    +-- Data loss prevention
                    +-- Audit logging
                    +-- Protocol transformation
```

The gateway terminates the connection from the source domain, authenticates and authorises against cross-domain policy, inspects and validates content, enforces classification labels on API payloads, and rebuilds the request to the destination domain (protocol break).

!!! info "Limitations"

    API gateways (Kong, Apigee, AWS API Gateway) are not designed or evaluated as security guards. The gateway software is a single point of trust. This pattern is suitable for lower-sensitivity boundaries or as one layer in a defence-in-depth architecture, not as a standalone high-assurance CDS.

## Software-defined data diodes

Software data diodes attempt to replicate the unidirectional flow property of hardware [data diodes](../hardware/data-diodes.md) in software:

- **Network stack enforcement** -- configure network interfaces to be transmit-only or receive-only at the driver level
- **Verified firewall rules** -- stateless firewall rules proved to permit only one-way traffic
- **Proxy-based** -- a receiving proxy accepts data, writes to a queue; a sending proxy reads and transmits with no return path configured
- **Application-level** -- UDP-only transmission with no acknowledgment channel, using FEC for reliability

Software data diodes are fundamentally weaker than hardware diodes because a software bug or misconfiguration can create a return channel, the OS kernel provides bidirectional networking by default, and covert channels through timing or error signals are harder to eliminate. There is no physics-based guarantee.

They may be appropriate for internal network segmentation where hardware diodes are cost-prohibitive, temporary deployments, or environments where the threat model does not include sophisticated covert channel exploitation. For anything requiring high assurance, use a [hardware data diode](../hardware/data-diodes.md).

## Cloud-native CDS

### AWS patterns

#### Multi-account strategy as domain separation

The AWS multi-account pattern is the primary mechanism for separating security domains in the cloud:

- Each security domain gets its own AWS account (or set of accounts)
- AWS Organizations provides governance boundaries
- Service Control Policies (SCPs) enforce invariants across accounts
- Account boundaries provide IAM isolation -- credentials in one account cannot access another by default

#### Cross-domain connectivity

| AWS Service | CDS Role | Notes |
|-------------|----------|-------|
| **VPC Peering** | Direct network path between domains | Simplest, but no inline inspection |
| **Transit Gateway** | Centralised hub for inter-domain routing | Can route traffic through inspection VPCs |
| **PrivateLink** | Service-level cross-domain access | Exposes specific services, not networks; unidirectional by design |
| **AWS Network Firewall** | Inline traffic inspection | Stateful inspection, IDS/IPS -- but not a certified guard |
| **Gateway Load Balancer** | Insert third-party security appliances inline | Can chain virtual guard appliances |

#### Inspection VPC pattern

A common AWS cross-domain architecture:

```
Domain A VPC  --->  Inspection VPC  --->  Domain B VPC
                        |
                        +-- Network Firewall
                        +-- Guard appliance (virtual)
                        +-- DLP engine
                        +-- Content inspection
```

Transit Gateway routes all inter-domain traffic through the inspection VPC, which hosts the CDS enforcement components. This is architecturally equivalent to a traditional [guard](../hardware/guards.md) deployment but virtualised.

#### AWS DataZone

DataZone provides a data governance layer that supports cross-domain data sharing patterns. It enables cataloguing, discovery, sharing, and governing of data with fine-grained controls ensuring the right data is accessed by the right user for the right purpose.

CDS relevance: data cataloguing maps assets to security domains, governed workflows enforce approval processes for cross-domain access, and fine-grained access controls enforce need-to-know. DataZone is not a CDS in the traditional sense -- it does not perform content inspection or provide a security boundary. It is a governance and policy layer that complements infrastructure-level CDS controls.

#### AWS Lake Formation

Lake Formation provides fine-grained data access controls with column-level and row-level security, tag-based access control (label data with classification tags, then apply permissions based on tags), cross-account sharing of specific tables or columns, and comprehensive auditing via CloudTrail.

This enables a pattern where different security domains (AWS accounts) access a shared data lake, but each domain only sees the data elements they are authorised for -- a form of software-enforced multi-level access.

### Azure Government patterns

Azure Government provides cloud infrastructure for US DoD workloads at various classification levels:

- **Azure Government regions** (US Gov Arizona, Texas, Virginia) -- FedRAMP High, DoD IL4, DoD IL5 authorised with 150+ services
- **US DoD regions** (US DoD Central, East) -- dedicated DoD-only regions achieving IL5 through exclusive DoD tenant separation
- **Azure Government Secret/Top Secret** -- separate regions for classified workloads (IL6)

Azure achieves separation through physical network isolation (Azure Government runs on a separate network from commercial Azure), separate identity model, and tenant isolation across compute, storage, and networking.

!!! warning "Cloud separation is not CDS"

    Cross-domain data transfer between classification levels (e.g., IL5 to IL6) still requires traditional CDS guard appliances. Cloud account boundaries provide network and identity separation, but lack content inspection, formal evaluation, and physical isolation. They are necessary but not sufficient.

### Cloud account boundaries as domain separation

| Property | Cloud Account Boundary | Traditional CDS |
|----------|----------------------|-----------------|
| Identity isolation | Yes (separate IAM) | Yes |
| Network isolation | Yes (separate VPCs by default) | Yes |
| Content inspection | No (requires additional services) | Yes (core function) |
| Formal evaluation | No | Yes (Common Criteria, etc.) |
| Physical isolation | No (shared infrastructure) | Often yes |
| Side-channel resistance | Limited | Strong (hardware CDS) |

## Data mesh parallels with CDS

Data mesh architecture shares several concepts with cross-domain solutions:

| Data Mesh Principle | CDS Parallel |
|--------------------|-------------|
| **Domain Ownership** -- teams own their data | Security domains own their information assets |
| **Data as a Product** -- high-quality data for other domains | Cross-domain transfers with quality and integrity guarantees |
| **Self-serve Data Platform** -- domain-agnostic tooling | Shared CDS infrastructure serving multiple domain pairs |
| **Federated Governance** -- interoperability through standardisation | Cross-domain security policy managed by a central authority |

### Data contracts as cross-domain policy

Data contracts define the structure, format, semantics, quality, and terms of use for exchanging data between a provider and consumers. For CDS purposes, they can encode:

- Allowed data formats (schema validation replacing content inspection for structured data)
- Classification labels as metadata fields
- Quality requirements equivalent to integrity checks
- Access conditions and sanitisation rules

This is a promising pattern for structured data exchange across domain boundaries, particularly in [data-centric CDS approaches](../how-it-works/treatments.md) where the focus is on protecting individual data objects rather than network zones.

### Multi-tenant platform considerations

When a data platform serves multiple security domains: tenant isolation must hold at rest, in transit, and during processing. Cross-domain queries must enforce authorisation and filter results to clearance level. Aggregate results must not leak information about individual domain records (inference control). And data lineage must track origin and transformations for audit.

## Zero Trust and CDS

Zero Trust Architecture (ZTA) and CDS are converging.

### How they align

Both reject perimeter-based trust. Both require continuous verification. Both move toward resource-centric protection rather than network-zone protection.

ZTA enhances CDS through:

- **Micro-segmentation** within domains reduces the impact of a compromised CDS
- **Continuous authentication** strengthens the "always-invoked" property
- **Software-defined perimeters** can dynamically create cross-domain access paths that exist only for the duration of an authorised session
- **Attribute-based access control (ABAC)** enables fine-grained policy considering user attributes, data classification, device posture, and context

### How they differ

ZTA is not a replacement for CDS in classified environments:

- ZTA does not address content inspection -- it manages access, not data transformation
- ZTA trust decisions are made by software that may itself be compromised
- ZTA does not provide the formal assurance levels required for classification boundaries
- ZTA is complementary: ZTA within domains, CDS between domains

## Protocol-specific software CDS

### Web/HTTP

Forward proxy with content inspection terminates HTTPS, inspects content, re-encrypts and forwards. Reverse proxy as guard exposes selected services from higher to lower domains. HTML sanitisation strips scripts, active content, and embedded objects. The NCSC warns about content-based attack protection as a key CDS principle -- web content is a primary attack vector.

### Email/SMTP

The most mature software CDS category. Content inspection covers attachment scanning, DLP keyword checks, and format validation. Header sanitisation removes or rewrites headers that could leak internal domain information. Attachment handling: strip, convert to safe formats, or quarantine. SPF/DKIM/DMARC validation at domain boundaries.

### Database

SQL proxy guards intercept queries and results, enforcing column/row-level access based on cross-domain policy. View-based access exposes only authorised data elements. The AWS Lake Formation pattern provides tag-based column/row access control across accounts. Inference control prevents aggregation queries from revealing classified individual records.

### Messaging (Kafka)

Topic-level access control per domain. Schema Registry enforcement through Avro/Protobuf validation. Message-level filtering based on classification labels. Cross-cluster replication with content-based routing. Dead letter queues for messages failing cross-domain policy checks.

### API (REST/GraphQL)

API gateway as guard with authentication, authorisation, schema validation, and content inspection. Response filtering strips fields based on consumer domain authorisation. Rate limiting prevents data exfiltration through high-volume calls. OAuth/OIDC scopes map security domain authorisations to API scopes.

### File transfer

The most traditional CDS use case and the one most thoroughly covered by NCSC guidance. The import pattern: transformation (low-side) to protocol break to verification (high-side) to destination. Transformation should be a low-side activity because parsing untrusted content is inherently risky. Format conversion turns complex formats (DOCX, XLSX) into simple verifiable ones (plain text, CSV, TIFF). Metadata stripping removes hidden metadata, tracked changes, and embedded objects.

For a comparison of how these software approaches measure up against [hardware CDS](../hardware/index.md), see the [hardware vs software trade-off analysis](hardware-vs-software.md).
