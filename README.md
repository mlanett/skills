# Skills

## Table of Contents

- distributed-systems: Use to build or review a robust distributed system.
- plain-english: Use when writing human-oriented documents, specs, or TDDs.

## Installation

### Everything

This month (July '26), I find this to be a good installation method:

git clone github.com/mlanett/skills ~/.agents            # Take over the user global skills.
mkdir -p ~/.cursor/ && ln -s ~/.agents/skills ~/.cursor/ # Take over Cursor skills.
mkdir -p ~/.claude/ && ln -s ~/.agents/skills ~/.claude/ # Take over Claude skills.

## Everything Else

- pnpx skills update --global

### Claude Code (aka The Anthropic Way)

#### Add the marketplace (one-time)

`/plugin marketplace add mlanett/skills`

#### Install the plugin

`/plugin install gsd@mlanett`

After installation, skills are available as `/gsd:skill-name` (e.g., `/gsd:plain-text`).

#### Auto-prompting in service repos

To make the marketplace available automatically in other repos, add it to `extraKnownMarketplaces` in the project's `.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": ["mlanett/skills"]
}
```

## Debugging

cd ~
ls -ald .agents .cursor .claude
ls -ald .agents/skills .cursor/skills .claude/skills
jq -r '.skills | to_entries | .[] | "\(.value.source): \(.value.skillPath)"' ~/.agents/.skill-lock.json | sort
