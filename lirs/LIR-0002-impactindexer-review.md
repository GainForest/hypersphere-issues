# LIR-0002: Impact Indexer Review System

| Field | Value |
|-------|-------|
| **LIR Number** | 0002 |
| **Title** | Impact Indexer Review System |
| **Author** | GainForest Team |
| **Status** | Implemented |
| **Created** | 2025-01-26 |
| **Updated** | 2025-01-29 |

> **Note**: This is a retroactive LIR documenting the Impact Indexer review lexicons that are now indexed in Hypergoat.

---

## Abstract

Index the Impact Indexer review lexicons (`org.impactindexer.review.*`) in Hypergoat. These lexicons enable a decentralized review system where users can comment on and like AT-Proto entities including records, users, PDSes, and lexicons. This enables community engagement and feedback directly on [impactindexer.org](https://impactindexer.org).

---

## Motivation

### Problem Statement

The Hypersphere Explorer (Impact Indexer) displays records from across the AT Protocol network, but users have no way to:

- Leave feedback or comments on records they view
- Like or endorse high-quality data
- Discuss lexicon schemas and their usage
- Provide reviews of PDS providers

### Who Benefits

- **Data consumers** - Leave feedback on records they use
- **Data publishers** - Receive community validation
- **Researchers** - Discuss methodology via comments
- **Developers** - Review and discuss lexicon designs

### Use Cases

1. **Record feedback** - Comment on biodiversity occurrences with corrections or additional context
2. **User endorsement** - Like profiles of trusted data contributors
3. **PDS reviews** - Community feedback on PDS reliability
4. **Lexicon discussion** - Comments on schema design decisions

### Existing Adoption

- New lexicons created for Impact Indexer v2
- Review UI implemented at impactindexer.org/review

---

## Lexicon Specification

### NSIDs

```
org.impactindexer.review.defs
org.impactindexer.review.comment
org.impactindexer.review.like
```

### Schema Reference

Full schemas at: https://github.com/GainForest/lexicons/tree/main/lexicons/org/impactindexer/review

### Full Schemas

#### Shared Definitions (`org.impactindexer.review.defs`)

```json
{
  "lexicon": 1,
  "id": "org.impactindexer.review.defs",
  "defs": {
    "subjectType": {
      "type": "string",
      "knownValues": ["record", "user", "pds", "lexicon"]
    },
    "subjectRef": {
      "type": "object",
      "required": ["uri", "type"],
      "properties": {
        "uri": { "type": "string", "maxLength": 8192 },
        "type": { "type": "ref", "ref": "#subjectType" },
        "cid": { "type": "string", "maxLength": 128 }
      }
    }
  }
}
```

#### Comment (`org.impactindexer.review.comment`)

```json
{
  "lexicon": 1,
  "id": "org.impactindexer.review.comment",
  "defs": {
    "main": {
      "type": "record",
      "key": "tid",
      "record": {
        "type": "object",
        "required": ["subject", "text", "createdAt"],
        "properties": {
          "subject": { "type": "ref", "ref": "org.impactindexer.review.defs#subjectRef" },
          "text": { "type": "string", "maxLength": 20480, "maxGraphemes": 6000 },
          "replyTo": { "type": "string", "format": "at-uri" },
          "createdAt": { "type": "string", "format": "datetime" }
        }
      }
    }
  }
}
```

#### Like (`org.impactindexer.review.like`)

```json
{
  "lexicon": 1,
  "id": "org.impactindexer.review.like",
  "defs": {
    "main": {
      "type": "record",
      "key": "tid",
      "record": {
        "type": "object",
        "required": ["subject", "createdAt"],
        "properties": {
          "subject": { "type": "ref", "ref": "org.impactindexer.review.defs#subjectRef" },
          "createdAt": { "type": "string", "format": "datetime" }
        }
      }
    }
  }
}
```

### Example Records

#### Comment on a Record

```json
{
  "$type": "org.impactindexer.review.comment",
  "subject": {
    "uri": "at://did:plc:abc123/app.gainforest.dwc.occurrence/3lbq...",
    "type": "record"
  },
  "text": "Great observation! The coordinates match the known range for this species.",
  "createdAt": "2025-01-26T10:30:00.000Z"
}
```

#### Like on a User

```json
{
  "$type": "org.impactindexer.review.like",
  "subject": {
    "uri": "did:plc:trusted-contributor",
    "type": "user"
  },
  "createdAt": "2025-01-26T10:30:00.000Z"
}
```

#### Comment on a Lexicon

```json
{
  "$type": "org.impactindexer.review.comment",
  "subject": {
    "uri": "app.gainforest.dwc.occurrence",
    "type": "lexicon"
  },
  "text": "Would be great to add support for `occurrenceEvidence` field from DwC.",
  "createdAt": "2025-01-26T10:30:00.000Z"
}
```

---

## Hypergoat Changes

- [x] Upload review lexicon schemas
- [x] Configure Jetstream subscription for `org.impactindexer.review.*`
- [x] No backfill needed (new lexicons)

### Expected Query Patterns

- Comments/likes by subject URI
- Comments/likes by author DID
- Threaded comment replies (via `replyTo`)
- Like counts per subject
- Recent activity feed

---

## Impact Indexer Changes

- [x] Add review lexicons to Lexicon Reference at `/lexicon/org.impactindexer.review.*`
- [x] Create review UI at `/review`
- [x] Display comment counts and like counts on records
- [x] Enable inline commenting on record detail pages

---

## Example Queries

### Fetch Comments for a Record

```graphql
query GetComments($subjectUri: String!) {
  orgImpactindexerReviewComment(
    where: { subject: { uri: { eq: $subjectUri } } }
    first: 50
  ) {
    edges {
      node {
        uri
        did
        text
        replyTo
        createdAt
      }
    }
    totalCount
  }
}
```

### Fetch Like Count for a Subject

```graphql
query GetLikeCount($subjectUri: String!) {
  orgImpactindexerReviewLike(
    where: { subject: { uri: { eq: $subjectUri } } }
  ) {
    totalCount
  }
}
```

### Check if User Liked a Subject

```graphql
query DidUserLike($subjectUri: String!, $userDid: String!) {
  orgImpactindexerReviewLike(
    where: { 
      subject: { uri: { eq: $subjectUri } }
      did: { eq: $userDid }
    }
  ) {
    edges {
      node {
        uri
      }
    }
  }
}
```

### Subscribe to New Comments

```graphql
subscription OnNewComment {
  recordCreated(collection: "org.impactindexer.review.comment") {
    uri
    did
    record
    createdAt
  }
}
```

---

## Security Considerations

- Comments are public and attributed to the author's DID
- No moderation system yet (future enhancement)
- One like per subject per user (enforced by rkey uniqueness)

---

## Backwards Compatibility

New lexicons with no backwards compatibility concerns.

---

## References

- [GainForest Lexicons - Review](https://github.com/GainForest/lexicons/tree/main/lexicons/org/impactindexer/review)
- [Impact Indexer](https://impactindexer.org)
- [AT Protocol Lexicon Spec](https://atproto.com/specs/lexicon)

---

## Changelog

| Date | Change |
|------|--------|
| 2025-01-26 | Initial implementation |
| 2025-01-29 | Retroactive LIR documentation |
