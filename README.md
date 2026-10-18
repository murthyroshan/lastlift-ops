<div align="center">

# lastlift-ops

**The operations inbox of [LastLift](https://github.com/murthyroshan/LastLift): repair and protect tickets for lifts, filed by an agent only after a human approves them.**

[Live site](https://lastlift.vercel.app) · [Main repository](https://github.com/murthyroshan/LastLift) · [Open tickets](https://github.com/murthyroshan/lastlift-ops/issues)

</div>

---

This repository holds no code. Every issue here was written by LastLift's **Dispatcher** agent, from facts in a FalkorDB graph of Boston's MBTA stations, and filed only after a person pressed **Approve**.

It is kept separate from the code on purpose: the token that files tickets can read and write issues in this repository and nothing else.

## How a ticket gets here

```
MBTA lift alert ─▶ Monitor ─▶ Analyst ─▶ Dispatcher ─▶ human approval ─▶ issue in this repository
                  opens an     picks a     proposes a      Approve or        dossier, labels,
                  incident     playbook    ticket only if  Reject, in        a drawing of the
                               from the    the graph has   LastLift's        station's step-free
                               graph       not changed     approvals inbox   graph
```

1. **Monitor** reads the MBTA's live elevator alerts and opens an incident for each lift alert.
2. **Analyst** runs certified max-flow on the station graph and picks a playbook: `ACCESS_LOST` when platforms lose step-free access, `PREVENTIVE` when a working lift has become the last step-free link.
3. **Dispatcher** turns that into a proposal, guarded by the graph's state version: if anything changed since the analysis, the proposal is superseded instead of filed.
4. **A person decides.** Nothing is filed automatically; an approval files one issue here.

## What each ticket contains

| Part | What it is |
|---|---|
| Title | `[LastLift] Repair lift …` (the lift is closed and platforms lose access) or `[LastLift] Protect lift …` (it still works but is now the last lift) |
| Platform table | Each affected platform and direction, its relationship to the lift, how many lift routes remain, and the minimum cut sets |
| History | Closures on record since 2024 and hours closed, from the MBTA's elevator archives |
| Station drawing | A Mermaid drawing of the station's step-free graph |
| Labels | `lastlift`, `lift-<id>` and `priority-1` (repair now) or `priority-2` (protect) |

## Safeguards

- **Human in the loop:** every ticket is an approved proposal; the default mode only drafts.
- **Operator-only filing:** with filing switched on, approvals are accepted only from the operator's own machine; the public site stays in draft mode.
- **One ticket per lift:** a hidden marker and the `lift-<id>` label dedupe; a second approval for a lift with an open issue adds a comment instead.
- **Least privilege:** a fine-grained token limited to this repository's issues.

## Tickets so far

| # | Lift | Station | Why |
|---|---|---|---|
| [#1](https://github.com/murthyroshan/lastlift-ops/issues/1) | 891 | Downtown Crossing | Protect: with lift 869 closed, 891 is the last step-free link for the Oak Grove and Alewife platforms, both directions |
| [#2](https://github.com/murthyroshan/lastlift-ops/issues/2) | 885 | Revere Beach | Repair now: closed, and the Wonderland platform loses step-free access |

---

<sub>Unofficial decision-support prototype, built for Graph Hacks (WeMakeDevs × FalkorDB). Not affiliated with or endorsed by the MBTA or MassDOT; the station model is built from MBTA open data and may differ from conditions on site. Always check [mbta.com/alerts/access](https://www.mbta.com/alerts/access) before travelling.</sub>
