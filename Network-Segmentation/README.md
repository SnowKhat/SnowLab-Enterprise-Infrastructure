

`STATUS // ACTIVE`
# 🛡️ Operation 002 — Establishing the Security Perimeter
### Network Segmentation & VirtualBox Networking

`LCARS // SECURITY DIRECTIVE 002`  
`STATUS // ACTIVE`

---

## 🟧 MISSION OBJECTIVE

USS SnowLab is currently connected to the home network through a
VirtualBox **Bridged Adapter**.

Before additional systems are deployed, the lab environment will be
redesigned to reduce unnecessary exposure to personal devices on the
home network.

The objective of this mission is to:

- Separate SnowLab systems from the home production network
- Preserve communication between SnowLab virtual machines
- Maintain Internet access when required
- Understand VirtualBox networking modes
- Validate the new network boundary through connectivity testing

> **A secure lab should be free to break without putting the rest of the ship at risk.**

---

## 🟥 CURRENT NETWORK STATE

`NETWORK MODE // BRIDGED ADAPTER`

SNOWLAB-DC01 is currently connected using a Bridged Adapter.

In this configuration, the virtual machine behaves similarly to another
physical device connected directly to the home network.

```text
                    HOME ROUTER
                        │
        ┌───────────────┼───────────────┐
        │               │               │
     HOST PC       PERSONAL DEVICES  SNOWLAB-DC01
                                      Bridged
