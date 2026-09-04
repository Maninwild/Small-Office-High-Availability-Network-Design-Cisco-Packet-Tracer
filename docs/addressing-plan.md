# Reviewed Addressing Plan

This plan keeps the three existing departmental LANs and corrects the two ambiguous areas visible in the topology image: the public server-room range and the shared-looking transit range.

## LANs

| Segment | Network | Default gateway | Suggested hosts |
|---|---|---|---|
| Chairman | `192.168.1.0/24` | `192.168.1.1` on Router1 | Chairman PC `.2`, VC PC `.3` |
| IT Department | `192.168.2.0/24` | `192.168.2.1` on Router0 | PCs `.2`–`.4`, printer `.5` |
| Computer Department | `192.168.3.0/24` | `192.168.3.1` on Main Router | PCs `.2`–`.5`, printer `.6` |
| Server room | `192.168.4.0/24` | `192.168.4.1` on Main Router | server `.2`, laptop `.3` |

All `/24` hosts use subnet mask `255.255.255.0`.

## Router transit links

Each physical point-to-point link gets a separate subnet. This prevents overlapping-interface ambiguity and uses address space efficiently.

| Link | Network and mask | Endpoint A | Endpoint B |
|---|---|---|---|
| Main Router ↔ Router0 | `10.0.0.0/30` (`255.255.255.252`) | Main `10.0.0.1` | Router0 `10.0.0.2` |
| Main Router ↔ Router1 | `10.0.0.4/30` (`255.255.255.252`) | Main `10.0.0.5` | Router1 `10.0.0.6` |
| Router0 ↔ Router1 | `10.0.0.8/30` (`255.255.255.252`) | Router0 `10.0.0.9` | Router1 `10.0.0.10` |

Match these addresses to the actual interface names shown by `show ip interface brief`; interface numbering depends on the router model and installed modules.

## Routing recommendation

Use OSPF process 1 in area 0 on all three routers:

| Router | Networks to advertise |
|---|---|
| Main Router | `192.168.3.0/24`, `192.168.4.0/24`, `10.0.0.0/30`, `10.0.0.4/30` |
| Router0 | `192.168.2.0/24`, `10.0.0.0/30`, `10.0.0.8/30` |
| Router1 | `192.168.1.0/24`, `10.0.0.4/30`, `10.0.0.8/30` |

Make LAN-facing interfaces passive so they advertise their networks without sending OSPF hello traffic to endpoint devices. Leave the router-to-router interfaces non-passive.

## Important checks

- Add the correct default gateway to every PC, printer, server, and laptop.
- Enable each routed interface with `no shutdown`.
- Set clock rate only on the DCE end of a serial connection, if the topology uses serial links.
- Confirm that no two router interfaces use overlapping subnets.
- Do not use `1.0.0.0/24` internally; it is public address space, not an RFC 1918 private range.
