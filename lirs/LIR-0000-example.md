# LIR-0000: Darwin Core Occurrence Records (Example)

| Field | Value |
|-------|-------|
| **LIR Number** | 0000 |
| **Title** | Darwin Core Occurrence Records |
| **Author** | GainForest Team |
| **Status** | Implemented |
| **Created** | 2024-06-01 |
| **Updated** | 2024-06-15 |

> **Note**: This is an example LIR to demonstrate the format. LIR-0000 is reserved for documentation purposes.

---

## Abstract

Add support for indexing Darwin Core occurrence records (`app.gainforest.dwc.occurrence`) in Hypergoat. This lexicon represents biodiversity observations including species sightings, bioacoustic recordings, and environmental monitoring data. Indexing this data enables real-time biodiversity monitoring and conservation impact tracking.

---

## Motivation

### Problem Statement

Biodiversity data is fragmented across multiple centralized databases (GBIF, iNaturalist, eBird). Researchers and conservationists need a decentralized way to publish and query species occurrence data that:

- Maintains data sovereignty for indigenous communities
- Enables real-time streaming of observations
- Supports multimedia (audio, images, video)
- Integrates with the AT Protocol identity system

### Who Benefits

- **Conservation organizations** - Track species populations in real-time
- **Researchers** - Query biodiversity data via GraphQL
- **Field workers** - Publish observations from remote locations
- **AI systems** - Process real-time species detection data

### Use Cases

1. **Bioacoustic monitoring** - Stream audio detections from forest sensors
2. **Camera trap data** - Publish wildlife imagery with species classifications
3. **Community science** - Enable decentralized species reporting
4. **Impact verification** - Prove conservation outcomes on-chain

### Existing Adoption

- GainForest has deployed 50+ acoustic sensors using this lexicon
- Over 100,000 occurrence records already published to AT Protocol
- Multiple conservation partners actively using the format

---

## Lexicon Specification

### NSID

```
app.gainforest.dwc.occurrence
```

### Schema Reference

Based on [Darwin Core Standard](https://dwc.tdwg.org/) with AT Protocol adaptations.

### Full Schema

```json
{
  "lexicon": 1,
  "id": "app.gainforest.dwc.occurrence",
  "defs": {
    "main": {
      "type": "record",
      "description": "A Darwin Core occurrence record representing a species observation",
      "key": "tid",
      "record": {
        "type": "object",
        "required": ["scientificName", "eventDate"],
        "properties": {
          "occurrenceID": {
            "type": "string",
            "description": "Unique identifier for this occurrence"
          },
          "scientificName": {
            "type": "string",
            "description": "Scientific name of the observed taxon"
          },
          "vernacularName": {
            "type": "string",
            "description": "Common name of the species"
          },
          "eventDate": {
            "type": "string",
            "format": "datetime",
            "description": "Date/time of the observation"
          },
          "decimalLatitude": {
            "type": "number",
            "description": "Latitude in decimal degrees"
          },
          "decimalLongitude": {
            "type": "number",
            "description": "Longitude in decimal degrees"
          },
          "country": {
            "type": "string",
            "description": "Country where observation occurred"
          },
          "locality": {
            "type": "string",
            "description": "Specific location description"
          },
          "basisOfRecord": {
            "type": "string",
            "description": "Type of evidence (HumanObservation, MachineObservation, etc.)"
          },
          "media": {
            "type": "array",
            "items": {
              "type": "ref",
              "ref": "#mediaItem"
            },
            "description": "Associated media files"
          },
          "identificationRemarks": {
            "type": "string",
            "description": "Notes on species identification"
          },
          "confidence": {
            "type": "number",
            "minimum": 0,
            "maximum": 1,
            "description": "Confidence score for ML-based identifications"
          }
        }
      }
    },
    "mediaItem": {
      "type": "object",
      "required": ["type", "url"],
      "properties": {
        "type": {
          "type": "string",
          "knownValues": ["audio", "image", "video", "spectrogram"]
        },
        "url": {
          "type": "string",
          "format": "uri"
        },
        "mimeType": {
          "type": "string"
        }
      }
    }
  }
}
```

### Example Record

```json
{
  "$type": "app.gainforest.dwc.occurrence",
  "occurrenceID": "GF-2024-001234",
  "scientificName": "Panthera onca",
  "vernacularName": "Jaguar",
  "eventDate": "2024-06-15T08:30:00.000Z",
  "decimalLatitude": -3.4653,
  "decimalLongitude": -62.2159,
  "country": "Brazil",
  "locality": "Uatumã Biological Reserve",
  "basisOfRecord": "MachineObservation",
  "media": [
    {
      "type": "image",
      "url": "https://cdn.gainforest.earth/occurrences/jaguar-001234.jpg",
      "mimeType": "image/jpeg"
    }
  ],
  "confidence": 0.94,
  "identificationRemarks": "Identified by camera trap AI model v2.1"
}
```

---

## Hypergoat Changes

- [x] Upload lexicon schema
- [x] Configure Jetstream subscription for `app.gainforest.dwc.occurrence`
- [x] Trigger backfill for existing records
- [x] No custom indexing requirements

### Expected Query Patterns

1. **By location** - Occurrences within geographic bounds
2. **By species** - Filter by `scientificName`
3. **By time range** - Recent observations
4. **By author** - Records from specific observers/sensors
5. **Aggregations** - Species counts by location

---

## Impact Indexer Changes

- [x] Add to Lexicon Reference page at `/lexicon/app.gainforest.dwc.occurrence`
- [x] Create Occurrence Feed component with map visualization
- [x] Add audio waveform player for bioacoustic recordings
- [x] Update `/feed` page to include occurrence stream

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
        media {
          type
          url
        }
      }
    }
    pageInfo {
      hasNextPage
      endCursor
    }
  }
}
```

### Fetch by Species

```graphql
query GetJaguarSightings {
  appGainforestDwcOccurrence(
    where: { scientificName: { eq: "Panthera onca" } }
    first: 50
  ) {
    edges {
      node {
        uri
        eventDate
        decimalLatitude
        decimalLongitude
        locality
        confidence
      }
    }
    totalCount
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

- **Location sensitivity**: Some species locations may need to be fuzzed to prevent poaching
- **No PII**: Occurrence records do not contain personal information
- **Public data**: All occurrence data is intended to be publicly accessible

---

## Backwards Compatibility

This is a new lexicon with no backwards compatibility concerns.

---

## References

- [Darwin Core Standard](https://dwc.tdwg.org/)
- [GainForest Documentation](https://docs.gainforest.earth/)
- [AT Protocol Lexicon Spec](https://atproto.com/specs/lexicon)

---

## Changelog

| Date | Change |
|------|--------|
| 2024-06-01 | Initial draft |
| 2024-06-10 | Added media support |
| 2024-06-15 | Implemented and deployed |
