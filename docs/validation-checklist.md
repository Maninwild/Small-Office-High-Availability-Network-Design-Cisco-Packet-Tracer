# Connectivity and Failover Validation

Record the command output or Packet Tracer screenshot for each check. A topology should be described as highly available only after the normal-path and failure-path tests pass.

## 1. Interface state

On every router:

```text
show ip interface brief
```

- [ ] Every required interface is `up/up`.
- [ ] Interface addresses and masks match the addressing plan.
- [ ] No interfaces use overlapping IP networks.

## 2. Routing state

On every router:

```text
show ip route
show ip ospf neighbor
```

- [ ] Connected LAN and transit routes are present.
- [ ] Remote LANs appear as OSPF routes.
- [ ] Each router forms the expected OSPF neighbor relationships.

## 3. Endpoint configuration

- [ ] Every endpoint has the correct IP address and `/24` mask.
- [ ] Every endpoint uses the router LAN interface as its default gateway.
- [ ] No duplicate endpoint addresses exist.

## 4. Normal connectivity

From one endpoint in each LAN, ping:

- [ ] Its default gateway.
- [ ] An endpoint in each of the other three LANs.
- [ ] The server at `192.168.4.2`.

Use `tracert` from at least one departmental PC to the server and save the normal path.

## 5. Link-failure tests

Test one transit link at a time:

1. Shut down one router-to-router interface or disconnect that link.
2. Allow OSPF to reconverge.
3. Repeat the cross-LAN pings and traceroute.
4. Confirm traffic uses the two remaining sides of the router triangle.
5. Restore the link and confirm the routing table recovers.

- [ ] Main Router ↔ Router0 failure passes.
- [ ] Main Router ↔ Router1 failure passes.
- [ ] Router0 ↔ Router1 failure passes.

Packet loss during convergence can occur. Persistent failure after convergence means the topology has physical redundancy but not working routing redundancy.

## 6. Evidence to commit

- [ ] Updated `network-design.pkt`.
- [ ] Final topology image with masks or an addressing-table reference.
- [ ] Router interface summaries.
- [ ] Normal routing tables and OSPF neighbors.
- [ ] At least one successful before/after failover trace.
