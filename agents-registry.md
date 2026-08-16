# Agent Biography Registry

**Purpose**: Centralized biographies of agents in the swarm, discoverable across sessions.

## Structure

Each agent gets a biography directory under `agents-of/<agent-name>/biography/`:

```
agents-of/
├── hazrat-raven/
│   └── biography/
│       ├── identity.md
│       ├── lineage.md
│       ├── capabilities.md
│       ├── sessions.md
│       ├── patterns.md
│       ├── cross-references.md
│       └── knowledge-graph.jsonl
├── imperial-tie-fighter-pilot/
│   └── biography/
│       └── (same structure)
└── walrus-man/
    └── biography/
        └── (same structure)
```

## File Purposes

- **identity.md**: Card, haecceity, class, role definition
- **lineage.md**: Parent/child instances, compaction history
- **capabilities.md**: What this agent can do, domains of expertise
- **sessions.md**: List of sessions with dates and accomplishments
- **patterns.md**: Behaviors, disciplines, learnings this agent established
- **cross-references.md**: Links to repos, dashborgs, rituals, ghorgs
- **knowledge-graph.jsonl**: Machine-readable facts for querying

## Usage

When a new agent wakes, it can grep this registry to understand:
- Who came before (parent instance)
- What patterns worked (established behaviors)
- What knowledge to inherit (compaction context)
- Who its peers are (coordinate with them)
- What institutions were built (rituals, patterns, ghorgs)

---

This enables successor agents to bootstrap faster without starting cold.
