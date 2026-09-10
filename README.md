# Week 01 — Linux Environment Setup

## Cybersecurity Internship — NetworkWalks

> **Internship:** Cybersecurity Internship  
> **Organization:** NetworkWalks  
> **Mentor:** Waqas Karim  
> **Week:** 01  
> **Task:** Linux Environment Setup

---

## 📌 Objective

The objective of this task was to set up a Linux-based cybersecurity laboratory environment using **Kali Linux** inside **Oracle VirtualBox**.

The environment was configured with a custom **VirtualBox NAT Network**, IPv4 addressing, a default gateway, and DNS resolution.

After configuring the environment, network connectivity was verified and a VirtualBox snapshot was created to preserve the working state of the system.

---

## 🛠️ Tools & Technologies

| Tool / Technology | Purpose |
|---|---|
| **7-Zip** | Extracting compressed files and VM packages |
| **Oracle VirtualBox 7.2.16** | Virtualization platform |
| **Kali Linux** | Linux distribution for cybersecurity activities |
| **VirtualBox NAT Network** | Provides network connectivity to the Kali VM |
| **IPv4** | Network addressing |
| **DNS** | Domain name resolution |
| **NetworkManager / nmcli** | Linux network configuration |
| **VirtualBox Snapshot** | Recovery point for the configured environment |

---

# 1. Environment Architecture

The completed environment can be represented as follows:

```text
                         INTERNET
                            │
                            │
                     HOST COMPUTER
                            │
                     ORACLE VIRTUALBOX
                            │
                      NAT NETWORK
                       10.0.0.0/24
                            │
                    ┌───────┴───────┐
                    │               │
               Gateway          Kali Linux
               10.0.0.1         10.0.0.2/24
                                    │
                               DNS: 8.8.8.8
```

### Final Network Configuration

```text
Network Address : 10.0.0.0/24
Subnet Mask     : 255.255.255.0
Gateway         : 10.0.0.1
Kali Linux IP   : 10.0.0.2
DNS Server      : 8.8.8.8
Network Type    : NAT Network
DHCP            : Enabled at VirtualBox NAT Network level
```

---

# 2. Install Required Software

The first step was to prepare the host system with the required tools for creating the Linux virtual environment.

The following software was used:

- 7-Zip
- Oracle VirtualBox
- Kali Linux Virtual Machine

7-Zip was used for extracting compressed files, while Oracle VirtualBox was used to host the Kali Linux virtual machine.

---

# 3. Install Oracle VirtualBox

Oracle VirtualBox was installed as the virtualization platform.

The installed version shown in the installation window is:

```text
Oracle VirtualBox 7.2.16
Architecture: amd64
```

VirtualBox allows Kali Linux to run as an isolated virtual machine on the host operating system.

### Screenshot

![Oracle VirtualBox Installation](screenshots/01-virtualbox-installation.png)

---

# 4. Open VirtualBox Network Configuration

After installing VirtualBox, the VirtualBox Network Manager was accessed through:

```text
File
  → Tools
      → Network
```

The Network Manager provides configuration options for VirtualBox networking components.

The **NAT Networks** section was used to create the network required for the Kali Linux laboratory.

### Screenshot

![VirtualBox Network Manager](screenshots/02-virtualbox-network-menu.png)

---

# 5. Create the NAT Network

A custom NAT Network named:

```text
NatNetwork
```

was created.

The IPv4 network was configured using:

```text
10.0.0.0/24
```

DHCP was enabled for the NAT Network.

### Configuration

| Parameter | Value |
|---|---|
| Network Name | `NatNetwork` |
| IPv4 Prefix | `10.0.0.0/24` |
| DHCP | Enabled |
| IPv6 | Disabled |

### Understanding `/24`

The `/24` CIDR notation corresponds to the subnet mask:

```text
255.255.255.0
```

Therefore:

```text
Network Address   : 10.0.0.0
Usable Range      : 10.0.0.1 – 10.0.0.254
Broadcast Address : 10.0.0.255
```

### Screenshot

![NAT Network Configuration](screenshots/03-nat-network-configuration.png)

---

# 6. Configure the Kali Linux Virtual Machine

The Kali Linux virtual machine was configured and added to Oracle VirtualBox.

The VirtualBox Manager displayed the Kali VM with the following configuration:

```text
Name              : kali
Operating System  : Debian (64-bit)
Base Memory       : 2048 MB
Storage           : 20 GB
```

Kali Linux was selected as the operating system for the cybersecurity laboratory.

### Screenshot

![Kali Linux Virtual Machine](screenshots/04-kali-vm.png)

---

# 7. Configure Kali Network Adapter

The Kali Linux virtual machine's network adapter was configured to use the custom NAT Network.

The following settings were used:

```text
Enable Network Adapter : Enabled
Attached to            : NAT Network
Network Name           : NatNetwork
Adapter Type           : Intel PRO/1000 MT Desktop
Virtual Cable Connected: Enabled
```

This allows the Kali VM to communicate through the configured VirtualBox NAT Network.

### Screenshot

![Kali Network Adapter Configuration](screenshots/05-kali-network-adapter.png)

---

# 8. Start Kali Linux

After configuring the virtual machine, Kali Linux was successfully started.

The Kali desktop environment loaded successfully, confirming that the virtual machine was operational.

### Screenshot

![Kali Linux Desktop](screenshots/06-kali-desktop.png)

---

# 9. Open NetworkManager

After starting Kali Linux, the NetworkManager interface was accessed from the system tray.

The following option was selected:

```text
NetworkManager
      ↓
Edit Connections...
```

This provides access to the network connection configuration.

The active wired connection was subsequently configured with manual IPv4 parameters.

### Screenshot

![Kali NetworkManager](screenshots/07-network-manager.png)

---

# 10. Inspect the Network Interface

The current network interface configuration was inspected using:

```bash
ip a
```

The command displays:

- Network interfaces
- Interface state
- MAC address
- IPv4 addresses
- IPv6 addresses
- Network prefix information

The initial output showed the `eth0` interface with an address in the `10.0.0.0/24` network.

The screenshot shows:

```text
eth0
10.0.0.3/24
```

This address was assigned dynamically while DHCP was enabled on the VirtualBox NAT Network.

### Command

```bash
ip a
```

### Screenshot

![Kali IP Address](screenshots/08-ip-address.png)

---

# 11. Configure Static IPv4 Address

The wired connection was then configured manually through the IPv4 Settings section.

The following configuration was entered:

| Parameter | Configuration |
|---|---|
| Method | Manual |
| IPv4 Address | `10.0.0.2` |
| Netmask | `/24` |
| Gateway | `10.0.0.1` |
| DNS Server | `8.8.8.8` |

### Why use a static IP?

A predictable IP address is useful in cybersecurity laboratories because future activities may involve:

- Network scanning
- Packet analysis
- Client/server communication
- SIEM configuration
- Vulnerability assessment
- Firewall configuration
- Network monitoring
- Attack and defense simulations

Using a static address makes the lab environment easier to manage.

### Screenshot

![Manual IPv4 Configuration](screenshots/09-ipv4-configuration.png)

---

# 12. Configure and Restart the Network Connection

NetworkManager was used to modify and restart the wired network connection.

The following command was executed:

```bash
sudo nmcli connection modify "Wired connection 1" ipv4.dad-timeout 0
```

The network connection was then deactivated:

```bash
sudo nmcli connection down "Wired connection 1"
```

The connection was brought back up using:

```bash
sudo nmcli connection up "Wired connection 1"
```

The terminal confirmed that the connection was successfully deactivated and activated.

---

# 13. Verify Internet Connectivity

After configuring the network, connectivity was tested using:

```bash
ping google.com
```

The command successfully resolved the hostname and received ICMP echo replies.

Example output:

```text
PING google.com (...)

64 bytes from ...: icmp_seq=1 ttl=64 time=16.1 ms
64 bytes from ...: icmp_seq=2 ttl=64 time=16.2 ms
64 bytes from ...: icmp_seq=3 ttl=64 time=15.3 ms
64 bytes from ...: icmp_seq=4 ttl=64 time=16.9 ms
```

This confirms that the Kali Linux machine was able to communicate with an Internet host.

### What this test verifies

The successful `ping google.com` test demonstrates:

1. The Kali network interface is operational.
2. The default gateway is providing network connectivity.
3. Internet traffic is successfully passing through the NAT Network.
4. DNS resolution is working because `google.com` was resolved to an IP address.
5. ICMP responses are being received from the remote host.

### Screenshot

![Internet Connectivity Test](screenshots/10-network-verification.png)

---

# 14. Network Verification Commands

The following commands can be used to troubleshoot and verify the Linux network configuration.

### Display network interfaces

```bash
ip a
```

### Display routing table

```bash
ip route
```

### Display DNS configuration

```bash
cat /etc/resolv.conf
```

### Test the NAT gateway

```bash
ping -c 4 10.0.0.1
```

### Test Internet connectivity without DNS

```bash
ping -c 4 8.8.8.8
```

### Test DNS resolution and Internet connectivity

```bash
ping -c 4 google.com
```

A useful troubleshooting sequence is:

```text
Kali Network Interface
        ↓
Default Gateway
        ↓
Internet IP Address
        ↓
DNS Resolution
        ↓
Domain Connectivity
```

---

# 15. Create VirtualBox Snapshot

After successfully completing and testing the Linux environment, a VirtualBox snapshot was created.

The snapshot was named:

```text
first snapshot
```

The snapshot provides a recovery point for the configured Kali Linux environment.

If future cybersecurity exercises modify or break the system configuration, the VM can be restored to this known-good state.

### Screenshot

![VirtualBox Snapshot](screenshots/11-virtualbox-snapshot.png)

---

# 16. Final Environment

The final environment consists of:

```text
┌─────────────────────────────────────┐
│          Host Computer              │
│                                     │
│       Oracle VirtualBox 7.2.16      │
│                │                    │
│                ▼                    │
│       ┌───────────────────┐         │
│       │    Kali Linux     │         │
│       │                   │         │
│       │ IP: 10.0.0.2/24  │         │
│       │ GW: 10.0.0.1      │         │
│       │ DNS: 8.8.8.8      │         │
│       └─────────┬─────────┘         │
│                 │                   │
│          NatNetwork                 │
│          10.0.0.0/24               │
└─────────────────┬───────────────────┘
                  │
                  ▼
               Internet
```

---

# 17. Verification Summary

| Component | Status |
|---|---|
| 7-Zip | ✅ Installed |
| Oracle VirtualBox | ✅ Installed |
| Kali Linux VM | ✅ Configured |
| NAT Network | ✅ Configured |
| IPv4 Network | ✅ `10.0.0.0/24` |
| Kali Static IP | ✅ `10.0.0.2/24` |
| Gateway | ✅ `10.0.0.1` |
| DNS | ✅ `8.8.8.8` |
| Network Connection | ✅ Active |
| Internet Connectivity | ✅ Verified |
| DNS Resolution | ✅ Verified |
| VirtualBox Snapshot | ✅ Created |

---

# 18. Key Concepts Learned

During this task, I gained practical experience with:

- Virtual machine deployment
- Oracle VirtualBox
- Kali Linux
- NAT networking
- IPv4 addressing
- CIDR notation
- `/24` subnetting
- Default gateways
- DNS configuration
- DHCP vs. static IP addressing
- Linux network interfaces
- NetworkManager
- `nmcli`
- `ip` networking commands
- ICMP and `ping`
- Basic network troubleshooting
- Virtual machine snapshots

---

# 19. Important Networking Concepts

### NAT Network

A NAT Network allows multiple virtual machines to communicate with each other while also providing Internet access through the host system.

### IPv4 Address

The Kali machine was configured with:

```text
10.0.0.2
```

This identifies the Kali machine within the virtual network.

### Subnet

The `/24` prefix:

```text
10.0.0.0/24
```

defines the local network.

### Gateway

The gateway:

```text
10.0.0.1
```

acts as the route from the Kali VM toward external networks.

### DNS

The DNS server:

```text
8.8.8.8
```

is used to translate domain names such as:

```text
google.com
```

into IP addresses.

---

# 20. Conclusion

The Linux cybersecurity environment was successfully established using Kali Linux and Oracle VirtualBox.

A custom NAT Network using the `10.0.0.0/24` address space was created, and Kali Linux was configured with the static IPv4 address `10.0.0.2/24`, gateway `10.0.0.1`, and DNS server `8.8.8.8`.

The network configuration was tested successfully using `ping google.com`, confirming both DNS resolution and Internet connectivity.

Finally, a VirtualBox snapshot was created to preserve the working state of the environment and provide a reliable recovery point for future cybersecurity exercises.

This environment will serve as the foundation for subsequent practical cybersecurity activities during the internship.

---

## 📁 Repository Structure

```text
Week-01-Linux-Environment/
│
├── README.md
│
└── screenshots/
    ├── 01-virtualbox-installation.png
    ├── 02-virtualbox-network-menu.png
    ├── 03-nat-network-configuration.png
    ├── 04-kali-vm.png
    ├── 05-kali-network-adapter.png
    ├── 06-kali-desktop.png
    ├── 07-network-manager.png
    ├── 08-ip-address.png
    ├── 09-ipv4-configuration.png
    ├── 10-network-verification.png
    └── 11-virtualbox-snapshot.png
```

---

## 🧑‍💻 Internship Progress

```text
Week 01
└── Linux Environment Setup
    ├── VirtualBox Installation       ✅
    ├── Kali Linux Setup              ✅
    ├── NAT Network Configuration     ✅
    ├── IPv4 Configuration            ✅
    ├── DNS Configuration             ✅
    ├── Connectivity Verification     ✅
    └── Snapshot Creation             ✅
```

**Status: Week 01 Task Completed ✅**
