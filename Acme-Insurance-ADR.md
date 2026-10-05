Context

Acme Insurance’s strategic goals (from the motivation diagram) include:
	•	Revenue Increase → Enable new sales channels, aggregator access.
	•	Cost Reduction → Reduce IT operations overhead, standardize systems.
	•	Customer Self-Service → Provide digital access without IT bottlenecks.
	•	IT System Standardization → Align with corporate IT, prefer COTS, reuse existing software when possible.

Technical landscape:
	•	Multiple legacy systems (claims, rating engine) hosted on-premises.
	•	Corporate IT favors managed services to reduce infra footprint.
	•	APIs will need secure hybrid connectivity to on-premises data sources during transition.
	•	Time-to-market is critical — new capabilities must roll out quickly with minimal operational friction.

⸻

Decision

We will deploy all new APIs and integrations to CloudHub 2.0 in the MuleSoft Anypoint Platform, with hybrid connectivity to on-premises systems via VPN or MuleSoft VPC Peering.

Key aspects:
	1.	Primary runtime → CloudHub 2.0 for production and non-production workloads.
	2.	Hybrid connectivity to legacy systems for data access until COTS replacements are live.
	3.	Zero-downtime rolling deployments via CH2 for faster iteration and release cadence.
	4.	Governance integration with Anypoint API Governance and Anypoint Monitoring.
	5.	Standardized deployment pipeline via CI/CD for all environments.

⸻

Rationale
	•	Time-to-market: CH2 is fully managed with Kubernetes-based pods, reducing infra setup time and enabling rapid scaling.
	•	Cost reduction: Eliminates need for Acme to maintain complex on-prem Mule clusters (RTF/standalone).
	•	Standardization: Centralized policies, logging, and deployment processes align with corporate IT requirements.
	•	Scalability: CH2 supports both horizontal and vertical scaling natively.
	•	Security: Enforced API Manager policies and VPC isolation in CH2 ensure compliance.

⸻

Consequences

Positive:
	•	Minimal IT operations effort.
	•	Consistent deployment process across all environments.
	•	Easy integration with API Governance for compliance checks.
	•	Rapid onboarding of new projects.

Negative/Trade-offs:
	•	Some workloads that require deep OS/network customization cannot run in CH2 (would require RTF or on-prem fallback).
	•	Reliance on MuleSoft’s managed infrastructure (vendor lock-in).
	•	Latency to on-prem systems mitigated but not eliminated via VPN/peering.

⸻

Alternatives Considered
	1.	Runtime Fabric (RTF) – Rejected for now due to higher infra management needs and no immediate requirement for full control/customization.
	2.	On-Premise Mule Runtimes – Rejected due to high operational overhead and slower release cadence.
	3.	Hybrid CH2 + RTF – Considered for the future if regulatory changes require on-prem processing for specific workloads.

⸻

Decision Diagram (Mermaid)

```mermaid
flowchart TD
    A[Business Drivers]-->B[Revenue Increase\nFast Delivery Needed]
    A-->C[Cost Reduction\nLower Ops Overhead]
    A-->D[Customer Self-Service\nNeed Scalability]
    A-->E[IT Standardization\nCentral Governance]

    B-->F
    C-->F
    D-->F
    E-->F

    F{Deployment Model Choice}

    F-->G[CloudHub 2.0]
    G-->H[Managed Kubernetes Pods]
    G-->I[Zero-Downtime Deployments]
    G-->J[Hybrid Connectivity\nOn-Prem Systems]
```
