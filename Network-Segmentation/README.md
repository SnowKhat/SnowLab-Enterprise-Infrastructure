

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



# 🟨 LCARS // MISSION 002
# NETWORK SECURITY PERIMETER

> *"It is possible to commit no mistakes and still lose. That is not weakness. That is life."*
> — Captain Jean-Luc Picard

**MISSION:** Network Segmentation  
**ENVIRONMENT:** Oracle VirtualBox / Windows Server 2022  
**DOMAIN:** `snowlab.local`  
**PRIMARY NODE:** `SNOWLAB-DC01`  
**LAB NETWORK:** `10.10.10.0/24`  
**STATUS:** 🟡 IN PROGRESS

---

## 🖖 MISSION BRIEF

SnowLab originally operated using a **Bridged Network Adapter**, placing
virtual laboratory systems directly onto the same network as the production
home environment.

While convenient during initial deployment, this design provided insufficient
separation between experimental infrastructure and production devices.

The objective of Mission 002 is to redesign the SnowLab network so that:

- Laboratory systems operate on a dedicated subnet.
- Internet connectivity remains available.
- Active Directory and DNS remain operational.
- SnowLab is no longer directly bridged onto the production LAN.
- Access between SnowLab and the production network is restricted by policy.

This mission also documents unsuccessful configurations and troubleshooting
performed during the migration.

---

# 🟦 LCARS // INITIAL CONFIGURATION

## Original Network Architecture

`SNOWLAB-DC01` originally used a VirtualBox **Bridged Adapter**.

This resulted in the Domain Controller receiving an address from the physical
home network:

        SNOWLAB-DC01
    192....(nice try redacted)
          │
          ▼
    Home Network
    (nope :] redacted)
          │
          ▼
       Internet

Although functional, the lab shared the same Layer 3 network as production
devices.

### Initial Network Configuration

![Original bridged configuration](images/01-original-bridged-network.png)

The original configuration established the baseline used to verify that the
server was successfully moved away from the production subnet.

---

# 🟨 LCARS // SEGMENT DESIGN

A dedicated VirtualBox NAT Network was created for SnowLab.

**Network Design**

| Parameter | Configuration |
|---|---|
| Network Name | `SnowLab-NAT` |
| Network | `10.10.10.0/24` |
| Gateway | `10.10.10.1` |
| DC Address | `10.10.10.10` |
| DHCP | Enabled |
| IPv6 | Disabled |

### SnowLab NAT Network

![SnowLab NAT network](images/02-snowlab-nat-created.png)

The new logical architecture became:

    SNOWLAB-DC01
    10.10.10.10
          │
          ▼
    SnowLab-NAT
    10.10.10.0/24
          │
          ▼
    VirtualBox NAT
          │
          ▼
       Internet

---

# 🟧 LCARS // ADAPTER MIGRATION

Before modifying the Domain Controller, a VirtualBox snapshot was created:

    Pre-Network-Segmentation

This provided a rollback point if the network migration affected Active
Directory, DNS, or server connectivity.

The VM network adapter was then changed from:

    Bridged Adapter

to:

    NAT Network → SnowLab-NAT

### Virtual Adapter Configuration

![DC attached to SnowLab NAT](images/03-dc-nat-adapter.png)

> **Security Note:** MAC addresses and other unnecessary host identifiers
> were redacted from public screenshots.

---

# 🟩 LCARS // ADDRESSING

Because `SNOWLAB-DC01` provides Active Directory and DNS services, the server
was assigned a static address on the new network.

    IP Address:       10.10.10.10
    Subnet Mask:      255.255.255.0
    Default Gateway:  10.10.10.1
    DNS Server:       127.0.0.1

### Static Address Migration

![Static IP configuration](images/04-static-ip-migration.png)

The server successfully accepted its new address and could communicate with
the SnowLab NAT gateway.

---

# 🟥 LCARS // ANOMALY DETECTED

## Internet Connectivity Failure

Initial testing produced an unexpected result.

The Domain Controller could successfully reach:

    10.10.10.1

but could not reach:

    8.8.8.8

The initial test returned:

    Destination host unreachable.

This indicated that local communication with the virtual gateway was
functional while outbound NAT forwarding was not.

Several possible causes were investigated, including:

- Incorrect static routing.
- Incorrect gateway configuration.
- VirtualBox adapter configuration.
- Host-only network interference.
- NAT Network configuration.

The Windows routing table was inspected and corrected during troubleshooting.

Despite having a valid default route:

    0.0.0.0/0 → 10.10.10.1

external connectivity continued to fail.

---

# 🟨 LCARS // ROOT CAUSE ANALYSIS

Troubleshooting moved from the guest operating system to the VirtualBox
hypervisor.

Using `VBoxManage` on the host:

    VBoxManage list natnets

confirmed:

    Name:         SnowLab-NAT
    Network:      10.10.10.0/24
    Gateway:      10.10.10.1
    DHCP Server:  Yes

This verified that the gateway and subnet configuration were correct.

The NAT Network service was then explicitly started:

    VBoxManage natnetwork start --netname "SnowLab-NAT"

Following initialization of the NAT service, external connectivity succeeded.

---

# 🟢 LCARS // CONNECTIVITY RESTORED

Network testing was repeated.

    ping 10.10.10.1
    Result: PASS

    ping 8.8.8.8
    Result: PASS

### Connectivity Validation

![Successful gateway and Internet connectivity](images/05-connectivity-validation.png)

This demonstrated that `SNOWLAB-DC01` could reach both its virtual gateway and
external networks without returning to Bridged networking.

---

# 🟦 LCARS // DIRECTORY SERVICES VALIDATION

Because the IP address of a Domain Controller had changed, Active Directory
and DNS functionality were validated before proceeding.

DNS resolution confirmed:

    snowlab.local → 10.10.10.10

Domain Controller discovery was then tested:

    nltest /dsgetdc:snowlab.local

The system successfully discovered:

    \\SNOWLAB-DC01.SnowLab.local
    Address: \\10.10.10.10

### Active Directory Validation

![Active Directory validation](images/06-active-directory-validation.png)

Additional validation confirmed:

- DNS resolution operational.
- Domain Controller discovery operational.
- `SYSVOL` available.
- `NETLOGON` available.
- AD DS services operational.
- `dcdiag` completed with core tests passing.

The migration therefore preserved the Domain Controller's core services.

---

# 🟥 LCARS // SECURITY VALIDATION

With Internet and Active Directory functionality restored, the final test was
whether SnowLab could still initiate communication with the production home
network.

Test target:

    (host home network)

Results:

    ping (My home network)
    Result: REACHABLE

    tracert -d (home network)
    Result: REACHABLE

### Isolation Validation

![Failed isolation validation](images/07-isolation-test-failed.png)

This test revealed an important distinction:

> Moving the VM from Bridged networking to NAT separated the virtual machine
> from direct Layer 2 participation on the home network, but NAT alone did not
> enforce the desired security policy preventing SnowLab from initiating
> connections toward the production LAN.

The segmentation objective is therefore **not yet complete**.

---

# 🟪 LCARS // LESSONS LEARNED

This phase demonstrated the difference between **network separation** and
**security isolation**.

The migration successfully:

- Removed the Domain Controller from Bridged networking.
- Created a dedicated `10.10.10.0/24` laboratory subnet.
- Preserved Internet connectivity.
- Preserved Active Directory.
- Preserved DNS.
- Established a recoverable snapshot before migration.

However, testing demonstrated that VirtualBox NAT alone does not satisfy the
desired firewall policy.

Rather than considering the successful NAT migration the end of the project,
the failed isolation test became the design requirement for the next phase.

---

# 🟡 LCARS // MISSION STATUS

    NETWORK MIGRATION ............. COMPLETE
    STATIC ADDRESSING ............. COMPLETE
    INTERNET CONNECTIVITY ......... OPERATIONAL
    ACTIVE DIRECTORY .............. OPERATIONAL
    DNS ........................... OPERATIONAL
    PRODUCTION LAN ISOLATION ...... INCOMPLETE
    FIREWALL PERIMETER ............ REQUIRED

**MISSION STATUS: ACTIVE**

---

# 🔴 NEXT MISSION PHASE

## FIREWALL PERIMETER

The next phase will introduce an explicit firewall/routing boundary capable
of enforcing policy between SnowLab and the production network.

Target policy:

    SnowLab → Internet       ALLOW
    SnowLab → Home LAN       BLOCK
    Home LAN → SnowLab       BLOCK / EXPLICITLY CONTROLLED

The design will then be validated through connectivity and routing tests.

**LCARS // MISSION CONTINUES**




