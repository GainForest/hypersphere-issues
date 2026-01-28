# LIR-0003: Hypercerts Lexicons

| Field | Value |
|-------|-------|
| **LIR Number** | 0003 |
| **Title** | Hypercerts Lexicons |
| **Author** | Hypercerts Organization |
| **Status** | Implemented |
| **Created** | 2024-01-01 |
| **Updated** | 2025-01-29 |

> **Note**: This is a retroactive LIR documenting the Hypercerts lexicons that are already indexed in Hypergoat.

---

## Abstract

Index the Hypercerts protocol lexicons (`org.hypercerts.*` and `app.certified.*`) in Hypergoat. These lexicons define the core data structures for tracking impact work, contributions, evaluations, measurements, and funding in the Hypercerts v0.2 protocol on AT Protocol.

---

## Motivation

### Problem Statement

Impact certification and funding attribution lack standardized, decentralized infrastructure. Hypercerts provides a protocol for:

- Recording and certifying impact work with proper attribution
- Tracking contributions from multiple parties with weights
- Evaluating and measuring impact outcomes
- Managing rights and funding flows
- Supporting location-based and badge-based attestations

### Who Benefits

- **Impact creators** - Record verifiable impact work claims
- **Funders** - Track funding contributions and receipts
- **Evaluators** - Provide assessments of impact claims
- **Projects** - Organize activities into collections with proper attribution
- **Researchers** - Query standardized impact data via GraphQL

### Use Cases

1. **Impact certification** - Create hypercert activity records with contributor attribution
2. **Project organization** - Group activities into collections/projects
3. **Impact evaluation** - Third-party assessments with scores and measurements
4. **Funding tracking** - Record funding receipts and attribution
5. **Badge systems** - Award and display achievement badges

### Existing Adoption

- Hypercerts protocol in production use
- Multiple organizations publishing impact claims
- Active development at [hypercerts-org/hypercerts-lexicon](https://github.com/hypercerts-org/hypercerts-lexicon)

---

## Lexicon Specification

### NSIDs

The Hypercerts lexicons span multiple namespaces:

| Namespace | Description |
|-----------|-------------|
| `org.hypercerts.claim.*` | Core claim records (activities, collections, evaluations, etc.) |
| `org.hypercerts.funding.*` | Funding-related records |
| `org.hypercerts.helper.*` | Helper records (work scope tags) |
| `app.certified.*` | Shared certified lexicons (locations, badges) |

### Schema Reference

Full schemas available at: https://github.com/hypercerts-org/hypercerts-lexicon

NPM package: `@hypercerts-org/lexicon`

### Key Record Types

#### Claims (`org.hypercerts.claim`)

| Lexicon | Type | Description |
|---------|------|-------------|
| `claim.activity` | record | A hypercert record tracking impact work |
| `claim.collection` | record | Collection/group of items (activities and/or other collections) |
| `claim.attachment` | record | Commentary, context, evidence, or documentary material |
| `claim.contributorInformation` | record | Contributor identity, display name, and image |
| `claim.contributionDetails` | record | Contribution role, description, and timeframe |
| `claim.evaluation` | record | Evaluation of a hypercert record with scores |
| `claim.measurement` | record | Measurement data related to impact |
| `claim.rights` | record | Rights definitions (transferability, conditions) |

#### Funding (`org.hypercerts.funding`)

| Lexicon | Type | Description |
|---------|------|-------------|
| `funding.receipt` | record | Funding receipt for a payment between users |

#### Helper (`org.hypercerts.helper`)

| Lexicon | Type | Description |
|---------|------|-------------|
| `helper.workScopeTag` | record | Reusable scope atom for work scope expressions |

#### Certified (`app.certified`)

| Lexicon | Type | Description |
|---------|------|-------------|
| `certified.location` | record | Geographic location reference with SRS |
| `certified.badge.definition` | record | Badge type definition |
| `certified.badge.award` | record | Badge awarded to a subject |
| `certified.badge.response` | record | Response to a badge award |

### Example Records

#### Activity (Hypercert)

```json
{
  "$type": "org.hypercerts.claim.activity",
  "title": "Reforestation Project Q1 2024",
  "shortDescription": "Planted 10,000 native trees in degraded forest areas",
  "workScope": "Environmental conservation",
  "startDate": "2024-01-01T00:00:00Z",
  "endDate": "2024-03-31T23:59:59Z",
  "contributors": [
    {
      "contributorIdentity": "did:plc:contributor1",
      "contributionWeight": "0.6",
      "contributionDetails": "Project lead and coordination"
    },
    {
      "contributorIdentity": "did:plc:contributor2",
      "contributionWeight": "0.4",
      "contributionDetails": "Field operations"
    }
  ],
  "createdAt": "2024-04-01T10:00:00.000Z"
}
```

#### Collection (Project)

```json
{
  "$type": "org.hypercerts.claim.collection",
  "type": "project",
  "title": "Climate Action Initiative 2024",
  "shortDescription": "A comprehensive climate action program",
  "items": [
    {
      "itemIdentifier": {
        "uri": "at://did:plc:alice/org.hypercerts.claim.activity/abc123",
        "cid": "..."
      },
      "itemWeight": "0.5"
    },
    {
      "itemIdentifier": {
        "uri": "at://did:plc:bob/org.hypercerts.claim.activity/def456",
        "cid": "..."
      },
      "itemWeight": "0.5"
    }
  ],
  "createdAt": "2024-04-01T10:00:00.000Z"
}
```

#### Evaluation

```json
{
  "$type": "org.hypercerts.claim.evaluation",
  "subject": {
    "uri": "at://did:plc:alice/org.hypercerts.claim.activity/abc123",
    "cid": "..."
  },
  "evaluators": ["did:plc:evaluator1"],
  "summary": "Verified impact through satellite imagery and field visits",
  "score": {
    "min": 1,
    "max": 5,
    "value": 4
  },
  "createdAt": "2024-05-01T10:00:00.000Z"
}
```

#### Funding Receipt

```json
{
  "$type": "org.hypercerts.funding.receipt",
  "from": { "did": "did:plc:funder" },
  "to": "Climate Action Initiative",
  "amount": "10000",
  "currency": "USD",
  "paymentRail": "onchain",
  "paymentNetwork": "ethereum",
  "transactionId": "0x123...",
  "for": "at://did:plc:alice/org.hypercerts.claim.collection/project1",
  "occurredAt": "2024-03-15T14:30:00.000Z",
  "createdAt": "2024-03-15T14:35:00.000Z"
}
```

---

## Hypergoat Changes

- [x] Upload all Hypercerts lexicon schemas
- [x] Configure Jetstream subscription for all collections
- [x] Trigger backfill for historical data
- [x] Generate GraphQL types dynamically from schemas

### Expected Query Patterns

- Activities by contributor (DID)
- Activities by time range
- Collections/projects with nested items
- Evaluations by subject
- Measurements by metric type
- Funding receipts by sender/recipient
- Badges by awardee

---

## Impact Indexer Changes

- [x] Add Hypercerts lexicons to Lexicon Reference at `/lexicon`
- [x] Display activity records with contributor information
- [x] Support collection/project hierarchies
- [x] Show evaluation scores and measurements

---

## Example Queries

### Fetch Activities by Contributor

```graphql
query GetActivitiesByContributor($did: String!) {
  orgHypercertsClaimActivity(
    where: { contributors: { contributorIdentity: { eq: $did } } }
    first: 20
  ) {
    edges {
      node {
        uri
        title
        shortDescription
        startDate
        endDate
        contributors {
          contributorIdentity
          contributionWeight
        }
      }
    }
  }
}
```

### Fetch Collections (Projects)

```graphql
query GetProjects {
  orgHypercertsClaimCollection(
    where: { type: { eq: "project" } }
    first: 20
  ) {
    edges {
      node {
        uri
        title
        shortDescription
        items {
          itemIdentifier {
            uri
          }
          itemWeight
        }
      }
    }
  }
}
```

### Fetch Evaluations for an Activity

```graphql
query GetEvaluations($activityUri: String!) {
  orgHypercertsClaimEvaluation(
    where: { subject: { uri: { eq: $activityUri } } }
  ) {
    edges {
      node {
        uri
        did
        summary
        score {
          min
          max
          value
        }
        createdAt
      }
    }
  }
}
```

### Fetch Funding Receipts

```graphql
query GetFundingReceipts($recipientDid: String!) {
  orgHypercertsFundingReceipt(
    where: { to: { eq: $recipientDid } }
    first: 50
  ) {
    edges {
      node {
        uri
        from { did }
        amount
        currency
        paymentRail
        occurredAt
      }
    }
    totalCount
  }
}
```

### Subscribe to New Activities

```graphql
subscription OnNewActivity {
  recordCreated(collection: "org.hypercerts.claim.activity") {
    uri
    did
    record
    createdAt
  }
}
```

---

## Security Considerations

- Activities and contributions are public and attributed
- Funding receipts may have anonymous senders (from field optional)
- Evaluations are attributed to evaluator DIDs
- No PII beyond what users choose to publish

---

## Backwards Compatibility

These are established lexicons with existing data. The Hypercerts team maintains backwards compatibility through semantic versioning of their NPM package.

---

## References

- [Hypercerts Lexicon Repository](https://github.com/hypercerts-org/hypercerts-lexicon)
- [Hypercerts Protocol](https://hypercerts.org)
- [NPM Package](https://www.npmjs.com/package/@hypercerts-org/lexicon)
- [AT Protocol Lexicon Spec](https://atproto.com/specs/lexicon)

---

## Changelog

| Date | Change |
|------|--------|
| 2024-01-01 | Initial implementation |
| 2025-01-29 | Retroactive LIR documentation |
