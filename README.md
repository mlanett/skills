# Computer-aided Development

## Table of Contents

- distributed-systems: Use to build or review a robust distributed system.
- plain-english: Use when writing human-oriented documents, specs, or TDDs.

## Installation

### Claude Code

#### Add the marketplace (one-time)

`/plugin marketplace add mlanett/skills`

#### Install the plugin

`/plugin install cad@mlanett`

After installation, skills are available as `/cad:skill-name` (e.g., `/cad:plain-text`).

#### Auto-prompting in service repos

To make the marketplace available automatically in other repos, add it to `extraKnownMarketplaces` in the project's `.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": ["mlanett/skills"]
}
```
