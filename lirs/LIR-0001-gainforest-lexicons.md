# LIR-0001: GainForest Lexicons

| Field | Value |
|-------|-------|
| **LIR Number** | 0001 |
| **Title** | GainForest Lexicons |
| **Author** | GainForest Team |
| **Status** | Implemented |
| **Created** | 2024-01-01 |
| **Updated** | 2025-01-29 |

> **Note**: This is a retroactive LIR documenting the GainForest lexicons that are already indexed in Hypergoat.

---

## Abstract

Index the core GainForest lexicons (`app.gainforest.*`) in Hypergoat. These lexicons define biodiversity records (Darwin Core), decentralized evaluator services, and organization data structures used throughout the Hypersphere ecosystem for environmental impact tracking.

---

## Motivation

### Problem Statement

Environmental and biodiversity data is fragmented across centralized platforms. GainForest needs a decentralized infrastructure to:

- Publish biodiversity observations with full provenance
- Enable AI/human evaluator services to annotate data
- Track conservation organizations and their activities
- Maintain data sovereignty for indigenous communities

### Who Benefits

- **Conservation organizations** - Publish and track biodiversity data
- **Researchers** - Query standardized Darwin Core records via GraphQL
- **AI systems** - Process and evaluate biodiversity observations
- **Field workers** - Record observations with proper attribution

### Use Cases

1. **Biodiversity monitoring** - Darwin Core occurrence records for species observations
2. **Quality assessment** - Evaluator services that annotate records with confidence scores
3. **Organization profiles** - Track conservation projects and their data layers
4. **Acoustic monitoring** - Store and query bioacoustic recordings

### Existing Adoption

- GainForest production systems actively publishing to these lexicons
- Hundreds of thousands of occurrence records indexed
- Multiple conservation partners using the infrastructure

---

## Lexicon Specification

### NSIDs

The GainForest lexicons span multiple namespaces:

| Namespace | Description |
|-----------|-------------|
| `app.gainforest.common.*` | Shared definitions (blobs, images, URIs) |
| `app.gainforest.dwc.*` | Darwin Core biodiversity records |
| `app.gainforest.evaluator.*` | Decentralized evaluator services |
| `app.gainforest.organization.*` | Organization profiles and data |

### Schema Reference

Full schemas available at: https://github.com/GainForest/lexicons

### Key Record Types

#### Darwin Core (`app.gainforest.dwc`)

| Lexicon | Type | Description |
|---------|------|-------------|
| `dwc.event` | record | Sampling/collecting event with location and protocol |
| `dwc.occurrence` | record | Single organism observation at a place and time |
| `dwc.measurement` | record | Measurements linked to an occurrence |

#### Evaluator (`app.gainforest.evaluator`)

| Lexicon | Type | Description |
|---------|------|-------------|
| `evaluator.service` | record | Declaration registering an account as an evaluator |
| `evaluator.evaluation` | record | Evaluation result with typed results and confidence |
| `evaluator.subscription` | record | User subscribes to an evaluator's outputs |

#### Organization (`app.gainforest.organization`)

| Lexicon | Type | Description |
|---------|------|-------------|
| `organization.info` | record | Organization profile information |
| `organization.defaultSite` | record | Default site configuration |
| `organization.layer` | record | Map layer definitions |
| `observations/*` | record | Various observation types (dendogram, fauna, flora) |
| `predictions/*` | record | Prediction records |

### Example Record (Darwin Core Occurrence)

```json
{
  "$type": "app.gainforest.dwc.occurrence",
  "basisOfRecord": "HumanObservation",
  "scientificName": "Panthera onca",
  "vernacularName": "Jaguar",
  "eventDate": "2024-06-15T08:30:00.000Z",
  "decimalLatitude": -3.4653,
  "decimalLongitude": -62.2159,
  "country": "Brazil",
  "locality": "Uatumã Biological Reserve",
  "occurrenceStatus": "present",
  "createdAt": "2024-06-15T08:30:00.000Z"
}
```

---

## Hypergoat Changes

- [x] Upload all GainForest lexicon schemas
- [x] Configure Jetstream subscription for all collections
- [x] Trigger backfill for historical data
- [x] Generate GraphQL types dynamically from schemas

### Expected Query Patterns

- By species (`scientificName`)
- By location (coordinates, country, locality)
- By time range (`eventDate`)
- By organization
- By evaluator (for evaluation records)

---

## Impact Indexer Changes

- [x] Add GainForest lexicons to Lexicon Reference at `/lexicon`
- [x] Create occurrence feed visualization with map
- [x] Add audio waveform player for bioacoustic recordings
- [x] Display evaluator service information

---

## Example Queries

### Fetch Recent Occurrences

```graphql
query GetRecentOccurrences {
  appGainforestDwcOccurrence(first: 20) {
    edges {
      node {
        uri
        did
        scientificName
        vernacularName
        eventDate
        decimalLatitude
        decimalLongitude
      }
    }
  }
}
```

### Fetch Evaluations for a Record

```graphql
query GetEvaluations($subjectUri: String!) {
  appGainforestEvaluatorEvaluation(
    where: { subject: { uri: { eq: $subjectUri } } }
  ) {
    edges {
      node {
        uri
        did
        evaluationType
        confidence
        result
      }
    }
  }
}
```

### Subscribe to New Occurrences

```graphql
subscription OnNewOccurrence {
  recordCreated(collection: "app.gainforest.dwc.occurrence") {
    uri
    did
    record
    createdAt
  }
}
```

---

## Security Considerations

- Location data for endangered species may need coordinate fuzzing
- Organization data is public by design
- Evaluator results are attributed to their source DID

---

## Backwards Compatibility

These are established lexicons with existing data. No breaking changes.

---

## References

- [GainForest Lexicons Repository](https://github.com/GainForest/lexicons)
- [Darwin Core Standard](https://dwc.tdwg.org/)
- [AT Protocol Lexicon Spec](https://atproto.com/specs/lexicon)

---

## Changelog

| Date | Change |
|------|--------|
| 2024-01-01 | Initial implementation |
| 2025-01-29 | Retroactive LIR documentation |
