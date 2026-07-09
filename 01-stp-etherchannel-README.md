# Lab 01 — Spanning Tree Protocol & EtherChannel

## Objective

Prevent Layer 2 loops in a redundant switched network using STP/RSTP, then optimize bandwidth and provide link redundancy using EtherChannel — all verified in a three-switch multilayer topology with inter-VLAN routing via SVIs.

## Topology

![Topology](./topology.png)

- 3x multilayer switches
- Redundant trunk links between switches (looped by design, to force STP into action)
- VLANs routed via SVIs (Layer 3 switching)

## What I configured

**Classic STP (802.1D / PVST+)**
- Root bridge election (manipulated priority to control root placement)
- Verified port roles (Root Port, Designated Port, Blocking Port) and states
- Applied STP toolkit features: BPDU Guard, PortFast, Root Guard, Loop Guard

**Rapid STP (RSTP)**
- Migrated from PVST+ to Rapid-PVST+
- Compared convergence behavior and port states (RSTP vs classic STP)

**EtherChannel**
- Configured PAgP and LACP negotiation modes between switches
- Verified load balancing behavior across bundled links
- Bundled redundant trunk links into a single logical link to eliminate STP blocking on those paths while retaining redundancy

## Key commands used

```
spanning-tree vlan 10 root primary
spanning-tree portfast
spanning-tree bpduguard enable
spanning-tree guard root
spanning-tree mode rapid-pvst

interface range gi0/1-2
 channel-group 1 mode active   ! LACP
 channel-group 1 mode desirable ! PAgP
```

## Verification

```
show spanning-tree
show spanning-tree summary
show etherchannel summary
show etherchannel port-channel
```

## What I learned / issues hit

- Root Guard vs BPDU Guard serve different purposes — Root Guard stops a port from *becoming* root, BPDU Guard shuts the port down entirely on receiving a BPDU. Easy to mix these up early on.
- EtherChannel bundling requires matching config (speed, duplex, trunk settings) on all member ports — a single mismatch silently keeps the port out of the bundle.
- RSTP's port states (Discarding/Learning/Forwarding) map differently to classic STP's five states, which took some memorization work to keep straight.

## Configs

See [`/configs`](./configs) for sanitized switch configurations from this lab.
