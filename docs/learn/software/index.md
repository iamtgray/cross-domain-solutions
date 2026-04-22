# Software CDS

Software cross-domain solutions are the emerging frontier. They offer dramatically more flexibility, scalability, and cloud compatibility than [hardware CDS](../hardware/index.md) -- but they come with a harder certification path and a fundamentally different assurance basis.

Where hardware CDS derives security from physics (a fibre optic transmitter without a receiver physically cannot leak data backwards), software CDS derives security from the correctness of code, configuration, and the underlying platform. This is a different kind of trust. It can be very strong -- formally verified separation kernels like seL4 have mathematical proofs of correctness -- but it is never as simple as "the return fibre does not exist."

The trade-off is real. Software CDS enables things hardware cannot: cloud deployment, dynamic scaling, rapid policy changes, complex content inspection, and integration with modern API-first architectures. But it also introduces risks that hardware avoids: larger attack surfaces, side-channel vulnerabilities on shared infrastructure, and the ever-present tension between patching and certification.

This section covers three aspects of software CDS:

**Key considerations**
:   What makes software CDS different from hardware, how OS and virtualisation choices affect assurance, why containers are insufficient as a security boundary, and when software CDS is appropriate vs when hardware is required. [Read more](considerations.md).

**Architectures**
:   The concrete architectural patterns for software CDS -- virtualised guards, containerised approaches, microservices, API gateways, software-defined data diodes, cloud-native CDS on AWS and Azure, data mesh parallels, and Zero Trust integration. [Read more](architectures.md).

**Hardware vs software trade-offs**
:   A comprehensive comparison across security assurance, certification, cost, scalability, performance, deployment speed, and more -- plus a decision framework to help choose the right approach. [Read more](hardware-vs-software.md).

For the highest classification boundaries (SECRET and above), most national frameworks still require [hardware CDS](../hardware/index.md). But for OFFICIAL, OFFICIAL-SENSITIVE, and business domain boundaries, software CDS is increasingly the pragmatic choice -- and the industry trend is firmly in this direction.
