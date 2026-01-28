# Hypercollective

Central repository for feature requests, governance proposals, and Lexicon Indexing Requests (LIRs) for the open AT Protocol impact ecosystem.

## What is the Hypercollective?

An open AT Protocol ecosystem for environmental impact tracking, biodiversity data, and decentralized public goods infrastructure. Key components include:

| Project | Description | Live URL |
|---------|-------------|----------|
| **Hypergoat** | Go ATProto AppView that indexes Lexicon-defined records and exposes them via GraphQL | [hypergoat.vercel.app](https://hypergoat.vercel.app) |
| **Impact Indexer** | Data explorer - Real-time visualization of indexed data | [impactindexer.org](https://impactindexer.org) |

### Live Services

- **Hypergoat GraphQL API**: https://hypergoat.vercel.app/graphql
- **Hypergoat GraphiQL**: https://hypergoat.vercel.app/graphiql
- **Impact Indexer**: https://impactindexer.org
- **Lexicon Reference**: https://impactindexer.org/lexicon

## Governance Processes

### Lexicon Indexing Request (LIR)

A **LIR** is a formal proposal to add a new Lexicon to the indexing infrastructure. This includes:

1. Adding the lexicon to Hypergoat for indexing
2. Updating Impact Indexer documentation and visualization

**Quick Links:**
- [LIR Template](./templates/LIR-TEMPLATE.md) - Start a new request
- [Governance Process](./GOVERNANCE.md) - Full process documentation
- [Example LIR](./lirs/LIR-0000-example.md) - See a completed example

### How to Submit a LIR

#### Option 1: GitHub Issue (Recommended)

1. Go to [Issues](../../issues) and click "New Issue"
2. Select "Lexicon Indexing Request"
3. Fill out the form

#### Option 2: Pull Request

1. Copy `templates/LIR-TEMPLATE.md` to `lirs/LIR-XXXX-your-title.md`
2. Fill out all sections
3. Submit a PR

## Directory Structure

```
hypercollective/
├── README.md              # This file
├── GOVERNANCE.md          # LIR process & decision criteria
├── templates/
│   └── LIR-TEMPLATE.md    # Blank LIR template
└── lirs/
    └── LIR-0000-example.md  # Example LIR
```

## Related Repositories

| Repository | Purpose |
|------------|---------|
| [GainForest/lexicons](https://github.com/GainForest/lexicons) | GainForest ATProto lexicon schemas |
| [hypercerts-org/hypercerts-lexicon](https://github.com/hypercerts-org/hypercerts-lexicon) | Hypercerts protocol lexicon schemas |
| [hypercollective](https://tangled.org/gainforest.earth/hypercollective) | This repository - governance & feature requests |

## Contributing Organizations

- [GainForest](https://gainforest.earth) - Environmental impact & biodiversity
- [Hypercerts](https://hypercerts.org) - Impact certification protocol
- [Ma Earth](https://maearth.com) - Regenerative land funding & education

## Resources

- [AT Protocol Documentation](https://atproto.com/docs)

## License

This repository is licensed under the Apache License 2.0.
