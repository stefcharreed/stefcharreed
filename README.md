# Stefan Reed

Network engineer (NOC tech → network engineer), moving deeper into network security
and automation (NetDevOps). I build tooling around the networks I run, not just
configs.

[LinkedIn](https://www.linkedin.com/in/stefan-c-reed/)

## Building a self-hosted NetDevOps platform

Four pieces, designed to compose rather than stand alone:

| Stage | Project | Status |
|---|---|---|
| 1. Detect drift | [netmiko-config-audit](https://github.com/stefcharreed/netmiko-config-audit) — pulls running-configs over SSH, tracks per-device drift in git, exposes it via an MCP server | v1.1, feature-complete |
| 2. Knowledge base | [ccnp-encor-skills](https://github.com/stefcharreed/ccnp-encor-skills) — CCNP ENCOR study notes, structured as reusable, AI-queryable skill files | in progress |
| 3. Capture behavior | Syslog event pipeline | not started |
| 4. Diagnose it | An agent that composes stages 1 and 2 via MCP to reason about real network problems, not just static topics | in progress, private repo — [ask me about it](https://www.linkedin.com/in/stefan-c-reed/) |

## CCNP ENCOR (350-401) — studying in public

Notes get turned into installable [Claude Code](https://claude.com/claude-code)
skills as I go, not just static docs — each topic ships with real IOS-XE config
patterns, verification commands, and a troubleshooting checklist.

→ Full roadmap and progress: [ccnp-encor-skills](https://github.com/stefcharreed/ccnp-encor-skills)

| Domain (ENCOR weight) | Status |
|---|---|
| Architecture (15%) | Not started |
| Virtualization (10%) | Not started |
| Infrastructure (30%) | In progress — L2/L3 forwarding, STP/RSTP/MST, EtherChannel, IP routing, EIGRP, OSPF (incl. multi-area + OSPFv3) |
| Network Assurance (10%) | Not started |
| Security (20%) | Not started |
| Automation (15%) | Not started |

## Other projects

- [network-observability](https://github.com/stefcharreed/network-observability)

## How I work

I use Claude Code as part of my study and build process — turning raw notes and lab
output into structured, reusable references instead of one-off documents. The
`ccnp-encor-skills` repo is the clearest example of that in practice.
