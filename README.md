# Claude ecosystem census

An open, daily-refreshed census of the Claude Code ecosystem: **4,271
repositories** (MCP servers, skills, subagents, hooks, plugins, templates, tools
and awesome lists) with licence, maintenance and provenance data for each one.

Published under **CC BY 4.0**. Attribution: ClaudeWave (https://claudewave.com).

## Why this exists

Installing an MCP server into Claude Code is a one-line command. Working out
what licence it shipped under, whether it has been touched in six months, or
whether its README tells you to pipe a remote script into a shell, is not.

Nobody publishes that as a file you can grep. This is that file.

## What is in here

| File | Size | Contents |
|---|---|---|
| [`data/repos.csv`](data/repos.csv) | ~1.4 MB | One row per repository: owner, category, stars, forks, language, licence, topics, dates, trust score and flags |
| [`data/trust.json`](data/trust.json) | ~1.3 MB | Same population as structured records, with the flag codes and tier for each repo |

Both are regenerated nightly from the live catalogue at 05:00 CET and pushed
here by the workflow in [`.github/workflows/refresh.yml`](.github/workflows/refresh.yml).

### Fields that need explaining

- **`trust_score`** (0-100, higher is safer). A deterministic heuristic over
  maintenance signals, fork ratio, licence, owner history and README patterns.
  No model is involved in this number.
- **`trust_method`** is `heuristic` for the whole population. A small subset also
  carries a deeper model-assisted review of the file tree, manifests and README;
  at the time of writing that subset is **54 repositories**, so treat it as a
  sample and not as a second census.
- **`trust_flags`** are human-readable reasons. **95.5% of all flags are metadata
  hygiene** (no licence, no description, unmaintained), not vulnerabilities.

## What the numbers say

Snapshot of 2026-08-13. Every one of these is reproducible from `repos.csv`.

| | |
|---|---|
| Repositories censused | 4,271 |
| No machine-readable licence | **944 (22.1%)** |
| ...of which no licence at all | 551 |
| ...of which present but unidentifiable | 393 |
| Unmaintained (no recent commits) | 585 |
| AGPL-3.0 | 134, of which **80 are MCP servers** |
| READMEs documenting a `curl \| sh` install | 70, of which 27 are MCP servers |
| Not MCP servers | 53% of the catalogue |

Two findings worth pulling out:

**Unidentifiable rarely means careless.** The largest repositories in the
NOASSERTION bucket are n8n (200k stars), dify (152k) and open-webui (148k). A
detector returns NOASSERTION when a project ships a modified or bespoke licence,
which in this ecosystem usually means source-available with commercial or
branding conditions. A compliance check that only greps for the presence of a
`LICENSE` file passes all of them.

**AGPL and MCP interact in a way people miss.** An MCP server runs as a network
service, which is precisely the case AGPL section 13 was written for. 80 of the
134 AGPL repositories here are MCP servers. AGPL is a legitimate and widely used
licence; the point is to read it before deploying one for other people to use.

## Reproduce it yourself

```bash
curl -sL https://raw.githubusercontent.com/elephantpink-dev/claude-ecosystem-census/main/data/repos.csv -o repos.csv

python3 - <<'EOF'
import csv
rows = list(csv.DictReader(open('repos.csv')))
unclear = [r for r in rows if (r['license'] or '').strip() in ('', 'NOASSERTION')]
print(len(unclear), 'of', len(rows), f"({100*len(unclear)/len(rows):.1f}%)")
EOF
```

## What this is not

**It is not a security audit, and it does not claim to be one.** It measures
licence, maintenance and provenance. It does not analyse code paths, does not
assign CVEs and does not carry severity tiers. For deep MCP security analysis
with rule identifiers, better work than this exists.

The `curl | sh` count is a pattern match over README text, not a verified
install path, so some of those 70 will be documentation examples. The raw count
is published rather than a hand-curated one, because a curated figure could not
be reproduced from the file.

A low trust score means "open this before you install it", not "this is
malicious".

## Corrections

If a repository is misclassified, open an issue. Licence detection in particular
is blunt: NOASSERTION covers everything from a typo in a `LICENSE` file to a
carefully drafted commercial licence, and the census cannot tell them apart.

## Source

Generated from the catalogue at [claudewave.com](https://claudewave.com), a free
directory of the Claude ecosystem. The full report with the by-category
breakdown is at
[claudewave.com/en/mcp-security](https://claudewave.com/en/mcp-security).

Built and maintained by [ElephantPink](https://elephantpink.com). Not affiliated
with Anthropic.

## Licence

[CC BY 4.0](LICENSE). Use it, redistribute it, build on it. Attribution:
"ClaudeWave (https://claudewave.com)".
