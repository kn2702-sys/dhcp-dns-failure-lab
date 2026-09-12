# Optional DNS-SRV (Packet Tracer Server)

| Field | Value |
| --- | --- |
| IP | `192.168.10.10` |
| Mask | `255.255.255.0` |
| Gateway | `192.168.10.1` |
| DNS | `192.168.10.10` |

Services → DNS → On. Add A record:

| Name | Address |
| --- | --- |
| `dns.lab.local` | `192.168.10.10` |
| `gw.lab.local` | `192.168.10.1` |

Scenario B: turn DNS service **Off** or point the client DNS to `.99` so `ping dns.lab.local` fails while `ping 192.168.10.1` succeeds.
