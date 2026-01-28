# Hypercollective Governance

This document describes the governance processes for the Hypercollective, including how to propose changes, the review process, and decision criteria.

## Lexicon Indexing Request (LIR)

A **Lexicon Indexing Request (LIR)** is a formal proposal to add a new AT Protocol Lexicon to the indexing infrastructure.

### What is a Lexicon?

In the AT Protocol, a **Lexicon** defines the schema for a record type. Lexicons use NSIDs (Namespaced Identifiers) like:

- `app.bsky.feed.post` - Bluesky posts
- `org.hypercerts.claim.activity` - Hypercerts activity records
- `app.gainforest.dwc.occurrence` - Darwin Core biodiversity occurrences

When Hypergoat indexes a lexicon, it:
1. Subscribes to real-time events for that collection via Jetstream
2. Stores records in the database
3. Generates GraphQL types and resolvers dynamically
4. Exposes the data via the GraphQL API

### LIR States

```
┌─────────┐     ┌──────────────┐     ┌──────────┐     ┌─────────────┐
│  Draft  │ ──► │ Under Review │ ──► │ Accepted │ ──► │ Implemented │
└─────────┘     └──────────────┘     └──────────┘     └─────────────┘
                       │
                       │
                       ▼
                 ┌──────────┐
                 │ Rejected │
                 └──────────┘
```

| State | Description |
|-------|-------------|
| **Draft** | Initial proposal, may be incomplete |
| **Under Review** | Being actively discussed by maintainers |
| **Accepted** | Approved for implementation |
| **Implemented** | Deployed to production |
| **Rejected** | Not accepted (with documented reasoning) |

### LIR Numbering

LIRs are numbered sequentially: `LIR-0001`, `LIR-0002`, etc.

The filename format is: `LIR-XXXX-short-title.md`

Example: `LIR-0001-darwin-core-occurrences.md`

---

## Decision Criteria

When reviewing a LIR, maintainers consider the following criteria:

### Required

- [ ] **Valid Lexicon Schema** - The lexicon must have a valid JSON schema that conforms to the AT Protocol Lexicon specification
- [ ] **Published NSID** - The NSID must be properly namespaced (e.g., under a domain you control)
- [ ] **Clear Use Case** - The proposal must explain why this data should be indexed

### Strongly Encouraged

- [ ] **Existing Adoption** - Are there already records being created with this lexicon?
- [ ] **Community Demand** - Is there demonstrated interest from multiple parties?
- [ ] **Data Quality** - Is the schema well-designed with appropriate types and validation?

### Considerations

- [ ] **Maintenance Burden** - How much ongoing maintenance will this require?
- [ ] **Storage Impact** - Will this significantly increase database size?
- [ ] **Query Patterns** - Are the expected query patterns efficient?
- [ ] **Privacy/Security** - Does the data have any sensitive content?

### Automatic Acceptance

The following lexicons are automatically accepted:

1. **Hypercerts lexicons** (`org.hypercerts.*`)
2. **GainForest lexicons** (`app.gainforest.*`)
3. **Core AT Protocol lexicons** (as needed for ecosystem functionality)

---

## Review Process

### 1. Submission

Author submits a LIR via:
- GitHub Issue (recommended)
- Pull Request with markdown file

### 2. Initial Triage (1-3 days)

A maintainer will:
- Assign a LIR number if not already assigned
- Check for completeness
- Request clarifications if needed
- Move to "Under Review" status

### 3. Discussion Period (1-2 weeks)

- Community feedback is gathered
- Technical feasibility is assessed
- Questions are addressed by the author

### 4. Decision

Maintainers will:
- **Accept** - Move forward with implementation
- **Request Changes** - Ask for modifications before acceptance
- **Reject** - Decline with documented reasoning

### 5. Implementation

Once accepted:
1. Lexicon is uploaded to Hypergoat
2. Backfill is triggered (if historical data exists)
3. Impact Indexer documentation is updated
4. LIR status is updated to "Implemented"

---

## How to Write a Good LIR

### Do

- Provide a clear, concise abstract
- Explain the motivation and use cases
- Include a complete lexicon schema
- Show example records
- Provide sample GraphQL queries

### Don't

- Submit incomplete lexicon schemas
- Propose lexicons without clear use cases
- Request indexing of personal/private data
- Duplicate existing functionality

---

## Governance Roles

| Role | Responsibilities |
|------|------------------|
| **Author** | Writes and champions the LIR |
| **Maintainer** | Reviews LIRs, makes decisions |
| **Implementer** | Adds lexicon to Hypergoat |
| **Documenter** | Updates Impact Indexer docs |

Current maintainers:
- GainForest core team

---

## Amendments

This governance document may be updated via pull request. Significant changes require approval from at least two maintainers.

---

## Questions?

Open a discussion or issue in this repository.
