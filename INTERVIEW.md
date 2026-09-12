# Interview / ticket language

### Opening (when the user says Wi-Fi/LAN is connected but web fails)

I don’t assume “internet is down.” I check **addressing first**, then **gateway**, then **DNS**.

### DHCP

APIPA (`169.254.x.x`) means the client couldn’t get a lease. I verify VLAN, DHCP service/pool, and whether a relay is needed if the server isn’t on the same subnet.

### DNS

If the client can ping an IP (gateway or `8.8.8.8`) but not a hostname, **Layer 3 is up and name resolution is the fault**. I check the DNS server field and reachability to that resolver.

### Gateway

If the host has a correct IP/mask but can’t ping its default gateway, it won’t leave the subnet. Same-VLAN peer pings can still work — that pattern points to gateway, not DHCP.

### One-liner that hiring managers like

> I separate DHCP failure, gateway failure, and DNS failure with `ipconfig` and targeted pings before I change random settings.
