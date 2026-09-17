# Cisco 100-110 Exam: Interconnecting Cisco Networking Devices

[![Cisco Certified](https://img.shields.io/badge/Cisco_Certified-100--110_Exam-049fd9?style=for-the-badge&logo=cisco&logoColor=white)](https://learningnetwork.cisco.com/)
[![Track](https://img.shields.io/badge/Track-Routing%20%26%20Switching-049fd9?style=for-the-badge&logo=cisco)](https://learningnetwork.cisco.com/)
[![Level](https://img.shields.io/badge/Level-Entry%20%2F%20Associate-1BA0D7?style=for-the-badge)](https://learningnetwork.cisco.com/)
[![Duration](https://img.shields.io/badge/Duration-90_Minutes-orange?style=for-the-badge)](https://learningnetwork.cisco.com/)
[![Score](https://img.shields.io/badge/Passing_Score-~825%20%2F%201000-blue?style=for-the-badge)](https://learningnetwork.cisco.com/)
[![Practice Partner](https://img.shields.io/badge/Practice_Partner-CertsClub_(20%25_Off_Code:_club20)-28a745?style=for-the-badge&logo=shield)](https://www.certsclub.com/cisco/)

---

## 1. Exam Overview & Candidate Profile

The **Cisco 100-110 Exam (Interconnecting Cisco Networking Devices)** validates a candidate's foundational competencies required to install, configure, operate, and troubleshoot small enterprise branch networks. The operational scope covers fundamental OSI and TCP/IP networking models, IPv4/IPv6 address architectures and VLSM subnet calculation, Layer 2 Cisco Catalyst switchport configurations (VLANs, 802.1Q trunks, port security), basic inter-VLAN routing (Router-on-a-Stick), distance-vector routing dynamics, basic IPv4 ACL filtering, and essential IP infrastructure services including DHCP, DNS, NAT/PAT, and NTP.

### Target Candidate Profile & Roles
* **Junior Network Support Technician**
* **Network Operations Center (NOC) Tier-1 Analyst**
* **Field Service Technician (Enterprise & ISP Access)**
* **Help Desk / Desktop Support Specialist transitioning to Infrastructure**
* **Prerequisites:** No formal prerequisites required. Hands-on experience with basic network cabling, IP addressing, and command-line interfaces is recommended.

---

## 2. Key Exam Specifications

| Parameter | Official Specification |
| :--- | :--- |
| **Exam Code** | 100-110 |
| **Exam Name** | Interconnecting Cisco Networking Devices |
| **Duration** | 90 Minutes |
| **Passing Score** | ~825 / 1000 (Scaled dynamic calibration) |
| **Question Count** | 50–60 questions |
| **Question Formats** | Multiple Choice (single/multiple select), Drag-and-Drop, Simlets (CLI simulation) |
| **Delivery Vendor** | Pearson VUE / OnVUE Online Remote Proctoring |
| **Practice Test Partner** | **[100-110 Practice Test](https://www.certsclub.com/cisco/)** (Coupon: `club20` for 20% off) |

---

## 3. Skills Measured & Blueprint Domain Weighting

| Domain Code | Domain Title | Exam Weight | Key Technical Objectives Covered |
| :--- | :--- | :---: | :--- |
| **1.0** | **Network Fundamentals** | **20%** | OSI vs. TCP/IP model comparison, cabling media (UTP Cat5e/6, single-mode/multimode fiber), IPv4 addressing & VLSM, IPv6 address types (Global Unicast, Unique Local, Link-Local), TCP vs. UDP operational mechanics. |
| **2.0** | **LAN Switching Fundamentals** | **26%** | Ethernet frame structure, MAC address learning and aging, frame flooding, switchport access/trunk modes, IEEE 802.1Q encapsulation, native VLAN configuration, Spanning Tree Protocol (STP) loop prevention basics, port security violation modes. |
| **3.0** | **Routing Fundamentals** | **25%** | Routing table structure (prefix length, Administrative Distance, metric), static routes (default, host, floating), Router-on-a-Stick (802.1Q subinterfaces), distance-vector vs. link-state routing concepts, RIPv2/passive-interface configuration. |
| **4.0** | **Infrastructure Services** | **15%** | DNS resolution flow, DHCP server & client pool configuration, DHCP relay (`ip helper-address`), Inside Source NAT (Static NAT, Dynamic NAT, Port Address Translation / Overload), NTP synchronization. |
| **5.0** | **Infrastructure Maintenance** | **14%** | Cisco IOS CLI navigation, configuration register manipulation (password recovery), ping/traceroute interpretation, CDP/LLDP discovery, Syslog severity levels (0–7), standard numbered IPv4 ACL creation and placement. |

---

## 4. Scenario-Based Technical Practice Questions

### Scenario 1: Switch MAC Address Table Exhaustion & Security Violation
**Topology Background:**  
A network technician connects a Linux workstation running a security auditing script to port `FastEthernet0/1` on a Cisco Catalyst 2960 switch. The switchport is configured as follows:
```text
interface FastEthernet0/1
 switchport mode access
 switchport access vlan 10
 switchport port-security
 switchport port-security maximum 2
 switchport port-security violation restrict
 switchport port-security mac-address sticky
```
The script generates frames with 15 randomized source MAC addresses across the link. What is the immediate operational behavior of the switchport?

* A. The interface enters an `err-disable` state, shuts down the link LED, and increments the SNMP trap counter.
* B. The interface drops frames from unauthorized source MAC addresses, increments the SecurityViolation counter, sends a Syslog message and SNMP trap, and remains operational for authorized MACs.
* C. The switch clears all learned sticky MAC addresses and converts the port into an 802.1Q trunk.
* D. The switch drops all frames from unauthorized MAC addresses silently without logging or generating SNMP traps.

**Correct Answer:** **B**

**Detailed Technical Explanation:**  
Cisco IOS port-security supports three violation actions:
* `protect`: Drops packets with unknown source MAC addresses once the maximum threshold is reached. It does **not** log syslog messages, does not increment the violation counter, and does not send SNMP traps.
* `restrict`: Drops packets from non-negotiated/unauthorized source MACs, **increments the violation counter**, logs a Syslog message at warning severity, and generates an SNMP trap while leaving the physical port operational for previously learned/allowed MAC addresses.
* `shutdown`: Immediately places the interface into the `err-disable` state, turns off the link LED, increments the violation counter, and issues an SNMP trap.
Because `violation restrict` is explicitly set, the switch drops violating traffic and issues notifications without shutting down the link.

---

### Scenario 2: Variable Length Subnet Masking (VLSM) Route Optimization
**Topology Background:**  
An administrator is allocated the parent network block `172.16.8.0/21`. Three branch sites require subnet assignments with minimum host capacity:
* Branch Alpha: 480 host addresses
* Branch Beta: 240 host addresses
* Branch Gamma: 60 host addresses
* Two Point-to-Point WAN serial links: 2 host addresses each

To ensure zero address waste and optimal contiguous summarization, what prefix length should be allocated to Branch Alpha and the two WAN links, respectively?

* A. Branch Alpha: `/23` | WAN Links: `/30`
* B. Branch Alpha: `/22` | WAN Links: `/29`
* C. Branch Alpha: `/24` | WAN Links: `/30`
* D. Branch Alpha: `/23` | WAN Links: `/31`

**Correct Answer:** **A**

**Detailed Technical Explanation:**  
To satisfy host capacity requirements using binary host bits ($2^h - 2 \ge \text{required hosts}$):
1. **Branch Alpha (480 hosts):** $2^9 - 2 = 512 - 2 = 510$ usable hosts. A 9-bit host field requires a 23-bit network mask ($32 - 9 = 23$). Hence, `/23` (255.255.254.0) supports up to 510 hosts.
2. **Branch Beta (240 hosts):** $2^8 - 2 = 254$ usable hosts $\rightarrow$ `/24` (255.255.255.0).
3. **Branch Gamma (60 hosts):** $2^6 - 2 = 62$ usable hosts $\rightarrow$ `/26` (255.255.255.192).
4. **Point-to-Point WAN links (2 hosts each):** $2^2 - 2 = 2$ usable hosts $\rightarrow$ `/30` (255.255.255.252) provides standard point-to-point addressing with dedicated network and broadcast addresses.
* Distractor analysis: Option B allocates `/22` (1022 hosts), which wastes 542 addresses. Option C allocates `/24` (254 hosts), which cannot accommodate Branch Alpha's 480 hosts. Option D specifies `/31` (RFC 3021), which is typically reserved for specialized routed point-to-point links and is not the standard legacy CCENT/100-110 answer.

---

### Scenario 3: 802.1Q Native VLAN Mismatch Diagnostics
**Topology Background:**  
Switch-1 and Switch-2 are connected via trunk link `GigabitEthernet0/1`. An engineer inspects the link after user complaints:
```text
Switch-1# show interfaces trunk
Port        Mode             Encapsulation  Status        Native vlan
Gi0/1       on               802.1q         trunking      10

Switch-2# show interfaces trunk
Port        Mode             Encapsulation  Status        Native vlan
Gi0/1       on               802.1q         trunking      1
```
Cisco Discovery Protocol (CDP) begins logging `%CDP-4-NATIVE_VLAN_MISMATCH: Native VLAN mismatch discovered on GigabitEthernet0/1 (10), with Switch-2 GigabitEthernet0/1 (1)`. What is the technical consequence of this misconfiguration?

* A. The trunk link will immediately enter an `err-disable` state and cease passing all tagged traffic.
* B. Spanning Tree Protocol (STP) will place VLAN 1 and VLAN 10 on the port into a `broken` / `root-inconsistent` state to prevent Layer 2 loops, while untagged traffic from VLAN 10 on Switch-1 will leak into VLAN 1 on Switch-2.
* C. Switch-1 will dynamically renegotiate its native VLAN to VLAN 1 using DTP frames.
* D. All 802.1Q tagged frames for VLANs 20, 30, and 40 are dropped; only native VLAN frames are forwarded.

**Correct Answer:** **B**

**Detailed Technical Explanation:**  
In 802.1Q trunking, frames belonging to the native VLAN are transmitted across the trunk **untagged**. When Switch-1 sends an untagged frame from native VLAN 10, Switch-2 receives it without a tag and assumes it belongs to its own native VLAN (VLAN 1), causing cross-VLAN traffic leakage. Furthermore, Cisco Per-VLAN Spanning Tree Plus (PVST+) detects that BPDUs for VLAN 10 are being received on VLAN 1; to prevent potential bridging loops, PVST+ blocks the mismatched VLANs by placing the port into a `type-inconsistent` or `root-inconsistent` state for those specific VLANs.
* Distractor analysis: Option A is incorrect because native VLAN mismatches do not place physical ports into `err-disable`. Option C is false because DTP negotiates trunking operational state (trunk vs. access), not native VLAN IDs. Option D is incorrect because traffic for other explicitly tagged VLANs continues to forward normally.

---

### Scenario 4: Router-on-a-Stick (ROAS) Subinterface Encapsulation
**Topology Background:**  
A Cisco 2911 router interface `GigabitEthernet0/0` is connected to trunk port `GigabitEthernet0/24` on a Catalyst 2960 switch. The administrator intends to route between VLAN 20 (Subnet `192.168.20.0/24`) and VLAN 30 (Subnet `192.168.30.0/24`).
The router is configured as follows:
```text
interface GigabitEthernet0/0.20
 ip address 192.168.20.1 255.255.255.0
 encapsulation dot1Q 20
!
interface GigabitEthernet0/0.30
 ip address 192.168.30.1 255.255.255.0
!
interface GigabitEthernet0/0
 no shutdown
```
Hosts in VLAN 30 cannot communicate with their default gateway (`192.168.30.1`). What is the root cause?

* A. The physical interface `GigabitEthernet0/0` must be assigned an IP address in VLAN 1.
* B. Subinterface `GigabitEthernet0/0.30` is missing the command `encapsulation dot1Q 30`.
* C. The command `switchport mode trunk` must be executed under the router's physical interface.
* D. Subinterfaces require the `no shutdown` command to transition out of the administratively down state.

**Correct Answer:** **B**

**Detailed Technical Explanation:**  
On Cisco IOS routers, a subinterface cannot bind to an IP address or process tagged Ethernet frames without first defining the Layer 2 encapsulation type and 802.1Q VLAN tag ID via the `encapsulation dot1Q <vlan-id>` command. In fact, if an administrator attempts to configure an IP address before setting the encapsulation on a subinterface, Cisco IOS rejects the command with `% Configuring IP routing on a LAN subinterface is only allowed if that subinterface is already configured as part of an 802.10, 802.1Q, or ISL vLAN.`
* Distractor analysis: Option A is incorrect because the base physical interface in a ROAS topology should remain without an IP address to avoid accidental native routing overlaps. Option C is invalid because routers do not use switchport commands. Option D is incorrect because subinterfaces inherit their administrative up/down state directly from the parent physical interface.

---

### Scenario 5: Port Address Translation (PAT / NAT Overload) Diagnostics
**Topology Background:**  
An enterprise edge router R1 connects a private LAN (`192.168.1.0/24`) to the ISP via public interface `Serial0/0/0` (IP `203.0.113.2/30`). Internal clients report that web browsing fails. The router's configuration displays:
```text
interface GigabitEthernet0/0
 ip address 192.168.1.1 255.255.255.0
 ip nat inside
!
interface Serial0/0/0
 ip address 203.0.113.2 255.255.255.252
 ip nat outside
!
ip nat inside source list 1 interface Serial0/0/0
access-list 1 permit 192.168.1.0 0.0.0.255
```
What configuration modification is mandatory to allow multiple LAN users to concurrently browse the Internet through the single public IP address?

* A. Change `access-list 1 permit 192.168.1.0 0.0.0.255` to `access-list 1 permit 192.168.1.0 255.255.255.0`.
* B. Append the keyword `overload` to the `ip nat inside source list 1 interface Serial0/0/0` command.
* C. Configure `ip nat outside source list 1 interface Serial0/0/0`.
* D. Create a dynamic NAT pool containing at least 254 public IP addresses.

**Correct Answer:** **B**

**Detailed Technical Explanation:**  
Without the `overload` parameter, the command `ip nat inside source list 1 interface Serial0/0/0` provisions **Dynamic NAT (1-to-1)** without port translation. Consequently, only the very first LAN host that initiates an outbound connection gets mapped to `203.0.113.2`. All subsequent connections from other hosts are dropped because no free global IP addresses remain in the pool. Appending `overload` enables **Port Address Translation (PAT)** (RFC 3022), which tracks unique Layer 4 source port numbers (TCP/UDP), allowing up to ~65,000 concurrent sessions over a single public IP.
* Distractor analysis: Option A incorrectly uses a subnet mask instead of a wildcard mask. Option C reverses NAT direction. Option D requires purchasing expensive public subnets, which defeats the purpose of PAT.

---

### Scenario 6: Standard Access Control List (ACL) Processing Logic
**Topology Background:**  
An administrator implements a standard numbered IPv4 ACL on router R1 to restrict management access to the vty lines:
```text
access-list 10 deny host 10.1.1.50
access-list 10 permit 10.1.1.0 0.0.0.255
access-list 10 deny any
!
line vty 0 4
 access-class 10 in
 transport input ssh
```
Host `10.1.1.50` attempts an SSH session to the router, followed by host `10.1.2.10`. What are the respective outcomes?

* A. Both `10.1.1.50` and `10.1.2.10` are permitted access because line vty filtering ignores standard ACL statements.
* B. Host `10.1.1.50` is denied access; Host `10.1.2.10` is denied access by the explicit `deny any` statement.
* C. Host `10.1.1.50` is denied access; Host `10.1.2.10` is permitted access because `permit 10.1.1.0` matches classful networks.
* D. Host `10.1.1.50` is permitted access because standard ACLs match destination IP addresses rather than source IPs.

**Correct Answer:** **B**

**Detailed Technical Explanation:**  
Cisco IOS ACLs operate using top-to-bottom sequential rule evaluation with an immediate first-match exit and an implicit `deny all` at the end:
1. Host `10.1.1.50` matches line 1 (`deny host 10.1.1.50`), so the packet is dropped immediately.
2. Host `10.1.2.10` does not match line 1 or line 2 (`permit 10.1.1.0 0.0.0.255`). It reaches line 3 (`deny any`) and is dropped.
* Distractor analysis: Option A is incorrect because `access-class <num> in` strictly enforces access restrictions on vty lines. Option C is false because wildcard `0.0.0.255` strictly limits matching to the third octet matching `1`. Option D is incorrect because standard ACLs (IDs 1–99) inspect **source** IP addresses exclusively.

---

### Scenario 7: DHCP Relay Agent (`ip helper-address`) Mechanics
**Topology Background:**  
Clients in VLAN 50 (Subnet `192.168.50.0/24`) connect to access switch SW1. SW1 uplinks to router R1 on interface `GigabitEthernet0/0.50`. The enterprise DHCP server resides in the server farm at IP `10.10.10.100`.
When PC-1 boots, it broadcasts a `DHCPDISCOVER` packet on the local segment:
```text
Source IP: 0.0.0.0 | Destination IP: 255.255.255.255
Source Port: UDP 68 | Destination Port: UDP 67
```
What command must be configured, and on which interface, so that R1 relays this broadcast across the routed network to the unicast address of the DHCP server?

* A. Under `interface GigabitEthernet0/0.50`, configure `ip helper-address 10.10.10.100`
* B. Under `interface GigabitEthernet0/0`, configure `dhcp-relay server 10.10.10.100`
* C. Under global configuration, configure `ip dhcp relay target 10.10.10.100`
* D. Under the DHCP server-facing interface, configure `ip helper-address 192.168.50.1`

**Correct Answer:** **A**

**Detailed Technical Explanation:**  
Routers by default drop Layer 2 and Layer 3 broadcasts ($255.255.255.255$). To enable DHCP clients on a remote subnet to reach a centralized DHCP server, the router interface attached to the client subnet must act as a **DHCP Relay Agent**. Applying `ip helper-address <server-ip>` under the client-facing interface (`Gi0/0.50`) instructs the router to intercept UDP broadcasts on port 67, convert them into unicast packets with a destination IP of `10.10.10.100`, populate the `GIADDR` (Gateway IP Address) field with `192.168.50.1` (so the server knows which pool to select addresses from), and route the packet.
* Distractor analysis: Options B and C use non-existent Cisco IOS commands. Option D places the helper address on the outbound server interface, where it will never intercept incoming client broadcasts.

---

### Scenario 8: Administrative Distance (AD) and Floating Static Route Selection
**Topology Background:**  
A branch router R1 has two WAN paths to reach corporate headquarters (`10.0.0.0/8`):
1. Primary path learned via OSPF with a metric of 20.
2. Backup cellular LTE connection via next-hop gateway `198.51.100.1`.

The administrator executes:
```text
ip route 10.0.0.0 255.0.0.0 198.51.100.1 115
```
While both links are healthy, what will `show ip route 10.0.0.0` reveal, and what will happen if the primary OSPF link fails?

* A. Both paths will appear in the routing table and load-balance traffic unequally.
* B. Only the static route will appear in the routing table because static routes always override dynamic routing protocols.
* C. Only the OSPF route will appear in the routing table; upon OSPF failure, the floating static route (AD 115) will immediately be installed into the RIB.
* D. The router will drop the command with an error stating that the prefix is already known via OSPF.

**Correct Answer:** **C**

**Detailed Technical Explanation:**  
Cisco IOS uses **Administrative Distance (AD)** to determine route believability when multiple sources offer paths to the exact same prefix:
* Connected: 0
* Static (default): 1
* EIGRP (Internal): 90
* **OSPF:** 110
* Floating Static (in this scenario): **115**
* RIP: 120
* External EIGRP: 170
Because OSPF has an AD of 110, which is lower (more preferred) than 115, Cisco IOS installs only the OSPF route into the Routing Information Base (RIB). If the OSPF adjacency drops and the route is withdrawn, the floating static route (AD 115) immediately populates the routing table.
* Distractor analysis: Option A is incorrect because equal or unequal-cost load balancing only occurs among routes with identical AD. Option B is incorrect because a floating static route intentionally assigns an AD higher than the protocol it backs up. Option D is false because Cisco IOS supports multiple route sources for the same prefix.

---

### Scenario 9: Cisco IOS Password Recovery & Configuration Register Manipulation
**Topology Background:**  
A technician is performing password recovery on a Cisco 1941 ISR router after an administrator lost the enable secret password. The technician interrupts the boot sequence using the break key, accesses the ROMMON prompt (`rommon 1 >`), and modifies the configuration register to bypass the startup configuration in NVRAM.
Which value must be entered at the ROMMON prompt, and what is its binary significance?

* A. `confreg 0x2102` (sets bit 8 to ignore NVRAM)
* B. `confreg 0x2142` (sets bit 6 to ignore NVRAM)
* C. `confreg 0x2101` (forces boot into bootstrap ROM)
* D. `confreg 0x2000` (disables the console port)

**Correct Answer:** **B**

**Detailed Technical Explanation:**  
The Cisco IOS configuration register is a 16-bit software register. The default operating value is **`0x2102`** (which boots the system image from Flash and reads `startup-config` from NVRAM).
During password recovery:
* Setting the register to **`0x2142`** turns on **Bit 6** (the 6th bit in the register, value `0x0040`). Bit 6 instructs the Cisco bootloader to completely ignore the contents of NVRAM during power-on, presenting the technician with a fresh setup dialogue without erasing the existing configuration file.
* Once logged in, the technician copies NVRAM to running-config, changes the password, resets the register back to `0x2102` via `config-register 0x2102`, and saves.
* Distractor analysis: Option A is the default production register value. Option C boots the router into ROM monitor mode. Option D is an invalid configuration register setting.

---

### Scenario 10: IPv6 Link-Local Addressing & EUI-64 Host Identifier Generation
**Topology Background:**  
A network technician configures IPv6 routing on a Cisco router interface `GigabitEthernet0/0`:
```text
interface GigabitEthernet0/0
 mac-address 0014.a89d.6b30
 ipv6 address 2001:db8:acad:1::/64 eui-64
```
According to IEEE EUI-64 translation standards (RFC 4291), what is the calculated interface identifier (host portion) and resulting Link-Local IPv6 address automatically assigned to this interface?

* A. Interface ID: `0214:a8ff:fe9d:6b30` | Link-Local: `fe80::214:a8ff:fe9d:6b30`
* B. Interface ID: `0014:a8ff:fe9d:6b30` | Link-Local: `fe80::14:a8ff:fe9d:6b30`
* C. Interface ID: `0214:a800:009d:6b30` | Link-Local: `fe80::214:a800:009d:6b30`
* D. Interface ID: `fffe:0014:a89d:6b30` | Link-Local: `fe80::fffe:0014:a89d:6b30`

**Correct Answer:** **A**

**Detailed Technical Explanation:**  
The IEEE EUI-64 format converts a 48-bit MAC address into a 64-bit interface identifier through a deterministic two-step process:
1. **Split and Insert `FFFE`:** The 48-bit MAC (`00:14:A8 : 9D:6B:30`) is split into two 24-bit halves, and the hex delimiter `FF:FE` is inserted into the middle:
   $$\text{0014:A8} \rightarrow \text{0014:A8} + \text{FF:FE} + \text{9D:6B30} = \text{0014:A8FF:FE9D:6B30}$$
2. **Invert the Universal/Local (U/L) bit:** The 7th bit of the first byte is inverted:
   * First byte: `0x00` in binary is `00000000`.
   * Inverting the 7th bit results in `00000010` (`0x02`).
   * The modified identifier becomes **`0214:A8FF:FE9D:6B30`**.
3. **Link-Local Address Generation:** Combining the standard Link-Local prefix `FE80::/10` with the 64-bit EUI-64 interface ID produces `fe80::214:a8ff:fe9d:6b30`.
* Distractor analysis: Option B forgot to flip the 7th bit (U/L bit). Option C inserted zeros instead of `FFFE`. Option D prepended `FFFE` to the front of the MAC address rather than inserting it between the OUI and device identifier.

---

## 5. Recommended Study Resources & Official Documentation

* [Cisco Learning Network: 100-110 Exam Topics](https://learningnetwork.cisco.com/)
* [Cisco IOS Software Configuration Guides](https://www.cisco.com/c/en/us/support/index.html)
* [100-110 Practice Test - CertsClub](https://www.certsclub.com/cisco/) (Use coupon `club20` for 20% off)
* [RFC 3022: Traditional IP Network Address Translator (NAT / PAT)](https://datatracker.ietf.org/doc/html/rfc3022)
* [RFC 4291: IP Version 6 Addressing Architecture (EUI-64)](https://datatracker.ietf.org/doc/html/rfc4291)
* [Pearson VUE Cisco Certification Portal](https://home.pearsonvue.com/cisco)

---

## 6. SEO Keywords & Search Index Topics

```
100-110, 100-110 exam, 100-110 practice test, 100-110 study guide, cisco 100-110,
cisco 100-110 exam questions, cisco icnd1, interconnecting cisco networking devices,
cisco certification practice test, certsclub 100-110, cisco ccna prep,
cisco switchport port-security, vlsm subnetting, router-on-a-stick, 802.1q trunking,
nat overload pat, dhcp helper-address, cisco password recovery 0x2142, eui-64 ipv6
```

---

## 7. Community Discussions & Contributions

* **Questions & Case Discussions:** Open a discussion in [GitHub Discussions](../../discussions) to review protocol behavior, topology designs, or CLI anomalies.
* **Issue Reporting:** If you detect a typo or errata in any scenario, submit a ticket via [GitHub Issues](../../issues).
* **Lab Topology Submissions:** Pull requests containing Cisco Packet Tracer (`.pkt`), Cisco Modeling Labs (`.yaml`), or EVE-NG topologies matching these scenarios are encouraged.

---
*Maintained by the Cisco Certified Curriculum Community. Contributions and pull requests are welcomed.*
