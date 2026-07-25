---
name: install-community-skills
description: Install useful agent skills into Hermes from community directories (clawhub.ai, skills.sh / Vercel skills, anthropics/skills, vercel-labs repos). Hermes installs from a raw SKILL.md URL; the catalog sites do not give that URL directly — derive it from GitHub. Recurring request "install skills that are good for my goal".
triggers:
  - user pastes a clawhub.ai or skills.sh link and says install or pasang skill
  - user asks to install good skills for their project
  - user says cari skill berguna or install dari skills.sh or install dari clawhub
---

# Installing Community Skills into Hermes

Hermes installs a skill from a **direct raw SKILL.md URL** or `owner/repo/path` identifier:
```
hermes skills install "<RAW_URL>" --name <name> --force --yes
```
Community catalogs (clawhub.ai, skills.sh) show an `openclaw skills install ...` CLI command
that does NOT work in Hermes — you must resolve the raw GitHub URL yourself.

## Where the raw files actually live
- **clawhub.ai** `@author/name` → raw URL:
  `https://clawhub.ai/skills/<name>/SKILL.md` (Hermes fetched it fine via that path).
- **skills.sh** (Vercel) pages 308-redirect to GitHub `vercel-labs/skills` / `vercel-labs/agent-skills`.
  The raw path has a `skills/` prefix. Find it via GitHub API trees:
  ```
  curl -s "https://api.github.com/repos/vercel-labs/skills/git/trees/main?recursive=1" \
    | grep -oE '"path": "[^"]*SKILL.md"'
  ```
  Then raw: `https://raw.githubusercontent.com/vercel-labs/skills/main/skills/<name>/SKILL.md`
- **anthropics/skills**: `https://raw.githubusercontent.com/anthropics/skills/main/skills/<name>/SKILL.md`

## Workflow
1. From the catalog page, get the skill slug (name) and author.
2. Resolve the raw URL (use the GitHub API tree trick above for skills.sh — branch is `main`).
3. `hermes skills install "<raw_url>" --name <name> --force --yes`.
4. Verify: `hermes skills list | grep <name>`.

## Pitfalls
- skills.sh URL `https://skills.sh/s/.../SKILL.md` returns HTML (Next.js shell), not the file.
  Always go through raw.githubusercontent.com.
- Branch may be `main` (not `master`) for these repos.
- Some catalog skills are OpenClaw-only references (e.g. clawhub "Nft" is just a glossary).
  Judge usefulness before installing — for NFT image gen the local `nft-bulk-generation` skill
  is what actually does the work; `web3-docs` (clawhub) is the useful EVM reference.
- `--force --yes` is needed because the scanner flags community sources.
