# Stefan Reed

Network engineer (NOC tech → network engineer), moving deeper into network security
and automation (NetDevOps). I build tooling around the networks I run, not just
configs — and lately, tooling that turns what I'm studying into something a machine
can actually use.

Current focus: a CCNP knowledge base built as structured, queryable skill files —
**ENCOR is complete and ENARSI is underway** — plus two private tools built on top of
it. Working direction is firewall and security operations.

[LinkedIn](https://www.linkedin.com/in/stefan-c-reed/)

---

## CCNP — studying in public

**ENCOR 350-401 is complete: 32 topics, all six blueprint domains, the whole Official
Cert Guide.** They aren't notes. Each one is an installable
[Claude Code](https://claude.com/claude-code) skill that loads only when it's
relevant to what you're actually doing — and each ships with the same structure:

- **Config Patterns** — real IOS-XE, no pseudocode, and each block says whether it was
  transcribed from the source or written to fill a gap the source left
- **Design Baseline** — best-practice defaults with **a named, real source per row**.
  No source, no row. Every row also carries *legitimate reasons to deviate*, because
  real networks deviate from best practice for good and bad reasons — so a deviation
  is a **question** for the operator, never automatically a finding
- **Intent Questions** — "what is this supposed to be doing here?" answered *before*
  any `show` command
- **Troubleshooting Checklist** — ordered, and step 0 is always intent vs. observed
- **Common Pitfalls** — the things that actually cost people hours
- **Exam Preparation Tasks** — the chapter's own Key Topics table, mapped row by row to
  where the skill covers it. **A row that can't be mapped is marked `gap`**, so the map
  doubles as a coverage audit of the skill itself rather than a claim of completeness

The Design Baseline and Intent Questions sections exist because working engineers
told me the first version was missing them. That feedback is credited in the repo.

→ **[ccnp-encor-skills](https://github.com/stefcharreed/ccnp-encor-skills)** — both
catalogs, the full roadmap, and the `/ccnp-note` and `/enarsi-note` commands that
generate each skill from raw study notes

### ENCOR 350-401 — complete

| Domain (ENCOR weight) | Coverage |
|---|---|
| **Architecture (15%)** | **2 topics** — hierarchical campus design (access/distribution/core, network blocks, HA/SSO/NSF), and fabric: SD-Access (LISP/VXLAN/TrustSec, DNA Center layers) + SD-WAN (vManage/vSmart/vBond, OMP, AAR, Cloud OnRamp) |
| **Virtualization (10%)** | **2 topics** — overlay tunnels (GRE, IPsec IKEv1/IKEv2, VTI, LISP, VXLAN), and virtualization: VMs/containers/vSwitches, the ETSI NFV framework, VNF I/O (OVS-DPDK, PCI passthrough, SR-IOV), Cisco ENFV/NFVIS |
| **Infrastructure (30%)** | **22 topics** — L2/L3 forwarding, STP/RSTP/MST + protection, EtherChannel, IP routing, EIGRP, OSPF (multi-area + OSPFv3), BGP (+ advanced), multicast, QoS, IP services (NTP/PTP/FHRP/NAT), and the full wireless block: RF & modulation, infrastructure/CAPWAP, roaming & location services, troubleshooting |
| **Network Assurance (10%)** | **1 topic** — ping/traceroute/debug discipline, SNMP (MIBs, OIDs, traps), syslog, NetFlow & Flexible NetFlow, SPAN/RSPAN/ERSPAN, IP SLA, Catalyst Center Assurance |
| **Security (20%)** | **3 topics** — wireless client authentication (WPA3/SAE, EAP & 802.1x, four-way handshake, WebAuth); secure network access control (Cisco SAFE, the Secure portfolio, 802.1x/MAB/WebAuth, TrustSec, MACsec); device access control & infrastructure security (privilege levels, AAA/TACACS+, ZBFW, CoPP, hardening) |
| **Automation (15%)** | **2 topics** — network programmability (REST APIs, JSON/XML, DNA Center & vManage APIs, YANG/NETCONF/RESTCONF, Python) and automation tools (EEM + Tcl, Puppet, Chef, SaltStack, Ansible, Puppet Bolt, Salt SSH) |

Two blueprint topics — DHCP and first-hop security — have no standalone OCG chapter, so
the roadmap says that plainly instead of leaving them as permanently open checkboxes.

### ENARSI 300-410 — just started

Same template, second catalog, in the same repo. ENARSI revisits several protocols ENCOR
already covers — EIGRP, OSPF, BGP, ACLs, CoPP, AAA, SNMP, NetFlow, IP SLA,
NETCONF/RESTCONF — at greater depth and with a troubleshooting rather than an
implementation slant. Where that happens the ENARSI skill captures **the delta** and
cross-references the ENCOR skill, rather than duplicating it.

The generator enforces that: `/enarsi-note` reads `/ccnp-note`'s template by reference
instead of copying it, so the two catalogs can't drift apart, and it checks for an
existing ENCOR skill on the same protocol before writing a new one.

---

## Two private projects built on that catalog

*Both are private — they involve real device interaction patterns and lab state. If
either sounds interesting, **[reach out and ask me about it](https://www.linkedin.com/in/stefan-c-reed/)**;
I'm happy to walk through the architecture.*

### A multi-skill network troubleshooting agent

Composes the skill catalog with the drift auditor below, over MCP, to reason about
real problems across several topic areas at once — the way an engineer actually
troubleshoots, rather than one isolated topic at a time.

The design decision I care about most: **retrieval, not reasoning.** The agent's job
is to surface the right structured knowledge and walk a real checklist — not to
freelance an answer. And because the interface is MCP rather than a vendor SDK, the
"head" is swappable by construction: any MCP-capable client can drive it.

### A CCNP lab trainer that grades me on my own gear

Pick a topic, get a generated objective, build it on real equipment, get graded.
The parts that made it interesting to design:

- **Two grading axes, because one isn't enough.** Verification output answers *did it
  converge?*; config comparison answers *is it complete?* A perfect-looking config
  that never converged should fail, and so should a working hack. The config half is
  **scoped to the objective's own lines** — grading OSPF must never fail you over an
  unrelated NTP setting.
- **An AI answer key that's safe to be graded by.** A generator proposes the target
  config, an **independent reviewer agent checks it against the cited skill catalog**,
  and only then do I approve it. An unverified AI answer key is worse than no tool —
  it teaches you the wrong thing with confidence, and as the learner you're the least
  able to catch it.
- **Graduated hints.** Level 1 points you back at the intent questions and *cannot*
  reveal the answer. You have to spend hints to get to the diff.
- **Traffic probes for objectives no `show` command can prove.** `show ip nat
  translations` is empty until traffic flows; an ACL only proves itself on a denial —
  so that objective's passing condition is a probe that **fails**, plus a second probe
  that must still succeed, which catches a lazy deny-everything answer.
- **Infrastructure noise can't poison the record.** If a lab host is down, the attempt
  reports *inconclusive* — never a failure against the log of what I actually need to
  study.

---

## The wider platform

*In active development — one piece is shipped and hardware-validated, the rest are in
progress or planned. Posted so you can watch it take shape, not as a finished product.*

| Stage | Project | Status |
|---|---|---|
| 1. Detect drift | **[netmiko-config-audit](https://github.com/stefcharreed/netmiko-config-audit)** — pulls running-configs over SSH, tracks per-device drift in git, exposes it over MCP with human-gated baseline promotion | **Shipped — validated against physical Cisco gear** |
| 2. Knowledge base | [ccnp-encor-skills](https://github.com/stefcharreed/ccnp-encor-skills) — see above | **ENCOR complete; ENARSI active** |
| 3. Observe behavior | [network-observability](https://github.com/stefcharreed/network-observability) — Prometheus/Grafana/Alertmanager/snmp_exporter as code, with drift surfaced as its own dashboard panel and alert | Dockerized and CI-validated; live SNMP validation next |
| 4. Capture logs | Syslog event pipeline | not started |
| 5. Diagnose it | The troubleshooting agent above | private — [ask me](https://www.linkedin.com/in/stefan-c-reed/) |
| — | **[batfish-compliance-audit](https://github.com/stefcharreed/batfish-compliance-audit)** — runs the fleet's collected baselines through [Batfish](https://www.batfish.org/) to ask a different question from drift: not "did this device change," but "does the *intended* config satisfy policy at all" | v1 shipped — 40 tests, CI on 3.10–3.12, [threat model](https://github.com/stefcharreed/batfish-compliance-audit/blob/main/THREAT-MODEL.md) written. CLI exercised end-to-end against a real local Batfish container, against sanitized fixtures rather than real fleet baselines |
| — | [paloalto-config-audit](https://github.com/stefcharreed/paloalto-config-audit) — the stage-1 drift pattern applied to PAN-OS/Panorama security rules | offline pipeline complete — 95-test suite, CI on Python 3.10–3.12, threat model and a real-firewall runbook written; **on the back burner** pending that validation session |

---

## How I work

I use Claude Code as part of how I study and build — turning raw notes and lab output
into structured, reusable references instead of one-off documents, then building tools
that consume those references. `ccnp-encor-skills` is the clearest public example, and
the lab trainer above is what happens when you point that same knowledge base back at
yourself and let it grade you.

**[stef-claude-skills](https://github.com/stefcharreed/stef-claude-skills)** — two
Claude Code plugins:

- **bias-council** — a blind panel of subagents pressure-tests a decision or plan
- **repo-scan** — scores Claude Code repos with security gates before you install one

A rule I hold everything to: **fixture-tested and gear-validated are not the same
claim.** Every repo says which one it is. The skill catalog holds itself to the same
line — each one states whether its config came from the source material or was written
to fill a gap, and whether it's been run on real gear.
