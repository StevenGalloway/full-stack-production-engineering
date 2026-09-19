# Full-Stack Production Engineering Case Study

This repository documents real production engineering work on a live commercial SaaS platform. Client and product identity are withheld out of contractual confidentiality; the engineering substance — the problems, the decisions, and the reasoning behind them — is not.

The documentation follows the same conventions used in production engineering organizations: Architecture Decision Records (ADRs) capture design rationale, runbooks capture incident response and resolution, and diagrams capture system flow. This mirrors the format of [netflix-architecture-case-study](https://github.com/StevenGalloway/netflix-architecture-case-study) in this profile, but where that repository is a modeled reference architecture, everything here reflects work actually performed.

---

## What This Repository Demonstrates

- Feature design and delivery inside an existing, unfamiliar production codebase — not a greenfield build
- State modeling that preserves backward compatibility for every record that predates a feature
- Incident diagnosis that traces a symptom through the full system (client, server, database, access control) instead of guessing at the nearest plausible cause
- Willingness to revise a stated diagnosis in front of the client once better evidence arrived, rather than defend the first theory
- Long-lived technical documentation practice, including a living context file maintained for an AI coding assistant across sessions
- Operating without a safety net: no CI/CD, no staging environment, and effectively no automated test coverage — every change carries real regression risk

---

## Engagement Domains

| # | Domain | Description |
|---|--------|-------------|
| 01 | [Interactive Feature Development](docs/01-interactive-feature-development/) | Designing and shipping a new canvas-based editing capability into an existing rendering system |
| 02 | [Production Incident Response](docs/02-production-incident-response/) | Diagnosing live customer-reported defects to root cause across a full-stack system |
| 03 | [Legacy System Stewardship](docs/03-legacy-system-stewardship/) | Maintaining and extending an inherited codebase with no prior documentation |

---

## Repository Structure

```
docs/<domain>/
├── README.md          Domain overview, context, and outcome
├── decisions/          Architecture Decision Records (ADRs)
│   └── adr-NNNN-<topic>.md
├── diagrams/            Mermaid diagrams for key flows
│   └── <name>.mmd
└── runbooks/            Incident postmortems and response procedures
    └── <incident-type>.md
```

---

## A Note on Confidentiality

No client name, product name, source code, credentials, or proprietary business logic appears anywhere in this repository. Every description here is deliberately generalized to the level of the underlying engineering problem. Where a detail would make the client identifiable without adding engineering signal, it has been left out.

---

## Contact

- LinkedIn: https://www.linkedin.com/in/stevengalloway/
- Email: stevenpaulgalloway@gmail.com
