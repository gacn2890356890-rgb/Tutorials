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
