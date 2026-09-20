Multi-Subnet DHCP with Relay and Failover
Lease Dynamics and Exhaustion Analysis

A networking mini-project that demonstrates "DHCP operation across multiple subnets using DHCP Relay, DHCP Failover, and Lease Exhaustion Analysis".

The project is implemented in an "Ubuntu-based containerized networking environment" using "Docker, Containerlab, ISC DHCP Server, tcpdump, and Wireshark".


Project Overview

In a multi-subnet network, DHCP clients cannot directly communicate with a DHCP server located in another subnet because DHCP uses broadcast messages.

This project solves that problem using a "DHCP Relay Agent" and improves service availability using a "Primary–Secondary DHCP Failover configuration".

The project also includes a small DHCP address pool to study "lease allocation and pool exhaustion".

Main Concepts Demonstrated

* Multi-subnet DHCP
* DHCP Relay
* DHCP Failover
* DHCP DORA process
* DHCP Lease Management
* Lease Pool Exhaustion
* Packet Capture and Analysis
* Network Troubleshooting
* Containerized Networking


Objectives

* Configure DHCP address pools for multiple subnets.
* Implement DHCP Relay functionality.
* Configure primary and secondary DHCP servers.
* Study DHCP failover and lease synchronization.
* Create a limited DHCP pool for exhaustion analysis.
* Observe the DHCP DORA process.
* Capture DHCP packets using 'tcpdump'.
* Analyze DHCP packets using Wireshark.
* Validate DHCP server configuration and operation.


System Architecture

text
                  ┌─────────────────────┐
                  │   DHCP Primary      │
                  │   DHCP Server       │
                  └──────────┬──────────┘
                             │
                       Failover Link
                             │
                  ┌──────────▼──────────┐
                  │   DHCP Secondary    │
                  │   DHCP Server       │
                  └─────────────────────┘
                             │
                             │
                    DHCP Relay Network
                             │
              ┌──────────────┼──────────────┐
              │              │              │
          ┌───▼───┐      ┌───▼───┐      ┌───▼───┐
          │Relay 1│      │Relay 2│      │Relay 3│
          └───┬───┘      └───┬───┘      └───┬───┘
              │              │              │
        192.168.10.0   192.168.20.0   192.168.30.0
              │              │              │
           Clients        Clients        Clients


Network Configuration

The project uses three DHCP subnet scopes:

| Network         | Purpose                                  |
| --------------- | ---------------------------------------- |
| 192.168.10.0/24 | DHCP client subnet                       |
| 192.168.20.0/24 | DHCP client / exhaustion analysis subnet |
| 192.168.30.0/24 | DHCP client subnet                       |

 DHCP Failover

| Parameter      | Value    |
| -------------- | -------- |
| Primary Server | 10.0.0.1 |
| Secondary/Peer | 10.0.0.2 |
| Failover Port  | 647      |

### Lease Exhaustion Pool

text
192.168.20.100 – 192.168.20.102


A deliberately small pool is used to observe how available DHCP addresses are consumed by clients.

 DHCP DORA Process

The project demonstrates the standard DHCP four-step process:

text
Client                         DHCP Server
  │                                 │
  │──── DHCPDISCOVER ──────────────>│
  │                                 │
  │<──── DHCPOFFER ────────────────│
  │                                 │
  │──── DHCPREQUEST ───────────────>│
  │                                 │
  │<──── DHCPACK ──────────────────│
  │                                 │
  │       IP Address Assigned       │


### DORA

1. "DHCPDISCOVER" – Client searches for a DHCP server.
2. "DHCPOFFER" – Server offers an IP address.
3. "DHCPREQUEST" – Client requests the offered address.
4. "DHCPACK" – Server confirms the lease.

Example addresses observed during testing include:

text
192.168.10.100
192.168.10.109


DHCP Relay

DHCP clients initially send broadcast messages.

Since routers normally do not forward these broadcasts, a "DHCP Relay Agent" is used.

text
DHCP Client
     │
     │ Broadcast
     ▼
DHCP Relay
     │
     │ Forwarded DHCP Request
     ▼
DHCP Server


This allows a centralized DHCP server to provide addresses to clients located in different subnets.


DHCP Failover

Two DHCP servers are configured as a failover pair:

text
       Primary DHCP Server
             10.0.0.1
                 │
          Failover Link
          TCP Port 647
                 │
             10.0.0.2
       Secondary DHCP Server


The failover configuration allows DHCP lease information to be coordinated between the two servers and provides a redundant DHCP service arrangement.


Lease Exhaustion Analysis

A small address pool is configured:

text
192.168.20.100
192.168.20.101
192.168.20.102


As clients request addresses, the available leases are consumed.

text
Available
   ↓
Client 1 → 192.168.20.100
Client 2 → 192.168.20.101
Client 3 → 192.168.20.102
   ↓
Pool Exhaustion


This helps demonstrate the behaviour of DHCP when the available address pool becomes limited.

Technologies Used

| Technology       | Purpose                         |
| ---------------- | ------------------------------- |
| Ubuntu Linux     | Project environment             |
| Docker           | Container execution             |
| Containerlab     | Network topology                |
| ISC DHCP Server  | DHCP service                    |
| DHCP Relay       | Cross-subnet DHCP communication |
| tcpdump          | Packet capture                  |
| Wireshark        | Packet analysis                 |
| Linux Networking | Routing and connectivity        |



Project Structure

text
wipro-dhcp-project/
│
├── topology/
│   └── dhcp-lab.yml
│
├── configs/
│   ├── dhcpd.conf
│   └── ...
│
├── scripts/
│   └── ...
│
├── captures/
│   └── *.pcap
│
├── results/
│   └── ...
│
├── screenshots/
│   └── ...
│
└── docs/
    └── Multi_Subnet_DHCP_Relay_Failover_Project_Report.docx


Testing Performed

The following tests were performed:

1. Container Deployment

Verified the Docker/Containerlab networking environment and DHCP-related containers.

2. DHCP Server Validation

Validated the ISC DHCP server configuration and lease database.

3. Multi-Subnet DHCP

Configured DHCP scopes for:

text
192.168.10.0/24
192.168.20.0/24
192.168.30.0/24

4. DHCP DORA Testing

Observed:

text
DHCPDISCOVER
DHCPOFFER
DHCPREQUEST
DHCPACK


5. DHCP Relay Testing

Verified DHCP communication between clients and the centralized DHCP service through the relay.

6. Failover Configuration

Configured the primary and secondary DHCP servers using the failover relationship.

7. Lease Exhaustion

Prepared the limited pool:

text
192.168.20.100 – 192.168.20.102


for controlled exhaustion analysis.

8. Packet Capture

Captured DHCP traffic using 'tcpdump' and analyzed the packets using Wireshark.


Wireshark Analysis

The DHCP packets were analyzed at the protocol level.

Typical DHCPDISCOVER traffic contains:

text
Source IP       : 0.0.0.0
Destination IP  : 255.255.255.255
Source Port     : UDP 68
Destination Port: UDP 67


This demonstrates the broadcast-based nature of the initial DHCP request.



Results

The project demonstrated:

* Successful containerized DHCP environment.
* ISC DHCP Server operation.
* Multiple DHCP subnet configurations.
* DHCP relay architecture.
* DHCP failover configuration.
* Successful DHCP address allocation.
* DHCP DORA message exchange.
* Controlled lease-exhaustion pool.
* DHCP packet capture and Wireshark analysis.

The experiment provides practical understanding of **DHCP address allocation, relay operation, redundancy, lease management, and packet-level troubleshooting**.


Project Limitations

The supplied experimental evidence does not provide complete numerical measurements for:

* DHCP lease-acquisition time.
* Failover takeover time.
* Server recovery time.
* Complete quantitative exhaustion timing.
* Large-scale client load testing.

These measurements can be added in future experiments.


Future Scope

The project can be extended by:

* Measuring DHCP acquisition and failover times.
* Increasing the number of DHCP clients.
* Generating controlled network load.
* Creating lease-utilization graphs.
* Automating server-failure testing.
* Adding more routed subnets.
* Automating DHCP state monitoring.
* Performing longer Wireshark packet captures.
* Integrating the lab into a larger virtualized networking/5G environment.


References

1. R. Droms, "Dynamic Host Configuration Protocol", RFC 2131, IETF.
2. Internet Systems Consortium – ISC DHCP documentation.
3. Containerlab documentation.
4. Docker documentation.
5. Wireshark documentation.



Author

G. Anand
B.E. Electronics and Communication Engineering
Rajalakshmi Institute of Technology
Academic Year: 2026


Project Summary

"Multi-Subnet DHCP with Relay and Failover" demonstrates how DHCP can provide automated IP address allocation across multiple network subnets while using DHCP relay and failover mechanisms for centralized service and redundancy. The project also analyzes DHCP lease behaviour, pool exhaustion, and packet-level communication using Wireshark.
