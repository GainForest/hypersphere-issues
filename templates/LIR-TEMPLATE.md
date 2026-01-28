# LIR-XXXX: [Title]

| Field | Value |
|-------|-------|
| **LIR Number** | XXXX |
| **Title** | [Short descriptive title] |
| **Author** | [Your name / handle / DID] |
| **Status** | Draft |
| **Created** | YYYY-MM-DD |
| **Updated** | YYYY-MM-DD |

---

## Abstract

[A brief (2-3 sentence) summary of the lexicon being proposed and why it should be indexed.]

---

## Motivation

[Explain why this lexicon should be added to Hypersphere. Include:]

- What problem does this solve?
- Who will benefit from this data being indexed?
- What use cases are enabled?
- Is there existing adoption of this lexicon?

---

## Lexicon Specification

### NSID

```
[your.namespace.collection.name]
```

### Schema Reference

[Link to the lexicon schema if publicly available, or include the full schema below.]

### Full Schema

```json
{
  "lexicon": 1,
  "id": "your.namespace.collection.name",
  "defs": {
    "main": {
      "type": "record",
      "description": "[Description of the record type]",
      "key": "tid",
      "record": {
        "type": "object",
        "required": ["field1", "field2"],
        "properties": {
          "field1": {
            "type": "string",
            "description": "[Field description]"
          },
          "field2": {
            "type": "integer",
            "description": "[Field description]"
          }
        }
      }
    }
  }
}
```

### Example Record

```json
{
  "$type": "your.namespace.collection.name",
  "field1": "example value",
  "field2": 42,
  "createdAt": "2024-01-15T10:30:00.000Z"
}
```

---

## Hypergoat Changes

[Describe what needs to happen in Hypergoat:]

- [ ] Upload lexicon schema
- [ ] Configure Jetstream subscription
- [ ] Trigger backfill (if applicable)
- [ ] Any custom indexing requirements

### Expected Query Patterns

[Describe how users will typically query this data:]

- By author (DID)
- By time range
- By specific fields
- Aggregations needed?

---

## Impact Indexer Changes

[Describe what documentation or UI changes are needed in Impact Indexer:]

- [ ] Add to Lexicon Reference page
- [ ] Create collection-specific visualization (if applicable)
- [ ] Update API documentation
- [ ] Other changes

---

## Example Queries

### Fetch Recent Records

```graphql
query GetRecent {
  yourNamespaceCollectionName(first: 10) {
    edges {
      node {
        uri
        did
        field1
        field2
        createdAt
      }
    }
  }
}
```

### Fetch by Author

```graphql
query GetByAuthor($did: String!) {
  yourNamespaceCollectionName(did: $did, first: 20) {
    edges {
      node {
        uri
        field1
        field2
      }
    }
  }
}
```

### Subscribe to New Records

```graphql
subscription OnNewRecord {
  recordCreated(collection: "your.namespace.collection.name") {
    uri
    did
    record
  }
}
```

---

## Security Considerations

[Describe any security or privacy considerations:]

- Does this data contain PII?
- Are there any access control requirements?
- Should certain fields be filtered?

---

## Backwards Compatibility

[Describe any backwards compatibility concerns:]

- Is this a new lexicon or an update?
- Will existing queries continue to work?

---

## References

- [Link to lexicon definition repository]
- [Link to related documentation]
- [Link to existing implementations]

---

## Changelog

| Date | Change |
|------|--------|
| YYYY-MM-DD | Initial draft |
