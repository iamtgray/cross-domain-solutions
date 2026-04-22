# Hardware CDS

Hardware cross-domain solutions are the gold standard of assurance. Their security properties are rooted in physics, not software -- a fibre optic transmitter without a receiver physically cannot leak data backwards, no matter what code is running on the systems it connects.

This matters because hardware-enforced security boundaries cannot be bypassed by software vulnerabilities, zero-day exploits, or misconfiguration. Even if every computer on both sides of a hardware CDS is fully compromised, the security boundary holds.

Hardware CDS comes in two main flavours:

**Data diodes**
:   One-way transfer devices that use optical or electrical isolation to guarantee data can only flow in a single direction. The simplest and highest-assurance form of CDS, with zero reported breaches in decades of deployment. [Read more about data diodes](data-diodes.md).

**Guards**
:   Bidirectional devices that inspect, filter, and control data flowing between security domains. More capable than diodes but with a larger attack surface and more complex certification path. [Read more about guards](guards.md).

Both types share a set of [key characteristics](characteristics.md) that define what makes a CDS "hardware" -- physical separation, tamper resistance, TEMPEST compliance, hardware root of trust, and non-bypassable security enforcement.

If you are trying to decide between hardware and software approaches, the [hardware vs software trade-off analysis](../software/hardware-vs-software.md) covers the decision criteria in detail. For lower-sensitivity boundaries or cloud deployments, [software CDS](../software/index.md) may be more appropriate -- but for SECRET and above, hardware remains the requirement in most national frameworks.
