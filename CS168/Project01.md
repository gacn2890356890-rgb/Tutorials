# Traceout

## Introducing Traceroute 

### Time-to-Live (TTL)
- extra header
- decrements the TTL before router recieves

### Exploiting TTL

- send error message
- UDP-over-IP (from us)
- ICMP-over-IP packets (from routers)
- The ICMP protocol has a different header structure

| not all routers are guaranteed to send back an error message

### Repeated Probing
- sending multiple probes
-  try to hit all the routers a certain number of hops away

| Note that in real-life, there’s no guarantee that we discover all routers at a given distance from our probes.

### Unreachable Ports
- ICMP-over-IP packet
- Note: There’s no formal standard for this, but many computers are configured to never use port number 33434, so that traceroute can use this port to trigger Port Unreachable messages.

## Header Structure

### IP Header Fields
| **Field**               | **Length (in bits)** | **Description**                                              |
| ----------------------- | -------------------- | ------------------------------------------------------------ |
| **Version**             | 4                    | Version of the IP protocol being used. For this project, we always use **IPv4 (version 4)**. |
| **Header Length**       | 4                    | Length of the IP header. Measured in 4-byte words. (Needed because of variable-length options). |
| **Type of Service**     | 8                    | Used to mark packets as high-priority. **Not needed for this project.** |
| **Total Packet Length** | 16                   | Length of the entire packet (header and payload). Measured in **bytes**. |
| **Identification**      | 16                   | Used for fragmentation. **Not needed for this project.**     |
| **Flags**               | 3                    | Used for fragmentation. **Not needed for this project.**     |
| **Offset**              | 13                   | Used for fragmentation. **Not needed for this project.**     |
| **TTL**                 | 8                    | Number of hops left before this packet expires.              |
| **Protocol**            | 8                    | Which higher-layer protocol should we pass the IP payload to? Relevant codes: **1 = ICMP**, **17 = UDP**. |
| **Checksum**            | 16                   | Checks that the packet wasn't corrupted in transit. **Ignore checksums for this project:** All outgoing packets set to 0; no need to verify incoming. |
| **Source Address**      | 32                   | IP address of the machine sending the packet.                |
| **Destination Address** | 32                   | IP address of the destination machine.                       |
| **Options**             | Variable             | Field to request advanced functionality. **Ignore for this project:** Outgoing packets won't have this; ignore if present in incoming. |

### UDP Header Fields

| **Field**            | **Length (in bits)** | **Description**                                              |
| -------------------- | -------------------- | ------------------------------------------------------------ |
| **Source Port**      | 16                   | Port number of the application sending this packet.          |
| **Destination Port** | 16                   | Port number of the application we're contacting at the destination. |
| **Packet Length**    | 16                   | Length of the entire packet (header and payload). Measured in **bytes**. |
| **Checksum**         | 16                   | Checks that the packet wasn't corrupted in transit. **Ignore checksums for this project.** |

### ICMP Header Fields

| **Field**          | **Length (in bits)** | **Description**                                              |
| ------------------ | -------------------- | ------------------------------------------------------------ |
| **Type**           | 8                    | Error type. Relevant codes: **3 = Destination Unreachable**, **11 = Time Exceeded**. |
| **Code**           | 8                    | Error code. Relevant codes: **3 = Destination Port Unreachable**, **0 = TTL Expired**. |
| **Checksum**       | 16                   | Checks that the packet wasn't corrupted in transit. **Ignore checksums for this project.** |
| **Rest of Header** | 32                   | Contents vary depending on ICMP type/code. For this project, these bits are **always set to 0**. |
