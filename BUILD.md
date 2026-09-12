# Build guide

## Option 1 — Extend Lab 1

1. Open your Lab 1 Packet Tracer file (or rebuild from [enterprise-vlan-lab](https://github.com/kn2702-sys/enterprise-vlan-lab)).
2. Use any **VLAN 10 (HR)** PC as `PC-USER`.
3. Keep a second HR PC as `PC-REF` with a working lease (or static `.12`).
4. Optional: add Server-PT on VLAN 10 as `DNS-SRV` (`192.168.10.10`) — see `configs/DNS-SRV-notes.md`.

## Option 2 — Minimal topology (this lab only)

Devices: R1, SW1 (2960), PC-USER, PC-REF, optional DNS-SRV.

Cabling:

- R1 `Gi0/0` ↔ SW1 `Gi0/1` (trunk or access VLAN 10; for minimal lab, access VLAN 10 on both sides of a single VLAN is OK, or use ROAS `.10` trunk)
- SW1 `Fa0/1` ↔ PC-USER
- SW1 `Fa0/2` ↔ PC-REF
- SW1 `Fa0/5` ↔ DNS-SRV (optional)

Paste `configs/R1-dhcp.txt` and `configs/SW1.txt`.

## Baseline verification (before any break)

| Check | Expected |
| --- | --- |
| PC-USER `ipconfig` | `192.168.10.x`, mask `/24`, gateway `192.168.10.1` |
| `ping 192.168.10.1` | Success |
| `ping PC-REF` | Success |
| DNS (if configured) | `ping` by hostname or documented A record works |

Only then start Scenario A/B/C in `troubleshooting.md`.
