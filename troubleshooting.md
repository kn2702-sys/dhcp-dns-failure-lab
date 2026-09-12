# Scenarios A / B / C — break, investigate, fix

Always restore the baseline between scenarios.

---

## Scenario A: DHCP failure

### Break (pick one)

**A1 — Disable DHCP service on R1**

```text
R1(config)# no service dhcp
```

**A2 — Remove / break the HR pool**

```text
R1(config)# no ip dhcp pool HR
```

**A3 — Wrong access VLAN on PC-USER port** (looks like DHCP failure to the user)

```text
SW1(config)# interface FastEthernet0/1
SW1(config-if)# switchport access vlan 20
```

### Expected symptom

- Client has **no valid IP**, or **`169.254.x.x` (APIPA)**
- Websites fail; often peers unreachable

### Investigate

```text
ipconfig
   ↓
169.254.x.x ?
   ↓
DHCP server reachable?   (ping 192.168.10.1 from a static test IP if needed)
   ↓
VLAN correct?            (show vlan brief / show interfaces status)
   ↓
DHCP pool available?     (show ip dhcp pool / show ip dhcp binding)
   ↓
DHCP relay?              (only if server is off-subnet — ip helper-address)
```

Packet Tracer: Desktop → Command Prompt → `ipconfig` / `ipconfig /renew`.

### Fix

Re-enable DHCP / restore pool / restore VLAN 10; then `ipconfig /renew`.

### Ticket note (example)

> User had APIPA addressing. Verified access VLAN 10 and restored DHCP pool on gateway. Lease obtained; gateway ping OK.

---

## Scenario B: DNS failure

### Prep

Ensure PC-USER has a **valid** IP + gateway (DHCP working). Network layer must be healthy.

### Break (pick one)

**B1 — Point DNS to a dead address**

PC-USER → IP Configuration → Static (or DHCP with wrong DNS if you set `dns-server` on the pool to a blackhole):

- IP: `192.168.10.25`
- Mask: `255.255.255.0`
- Gateway: `192.168.10.1`
- DNS: `192.168.10.99` (nothing listens)

**B2 — Empty DNS field** while using names.

**B3 — Contrast fix:** temporarily set DNS to `8.8.8.8` (or working `DNS-SRV`) after showing failure — interview demo of “DNS was the only broken piece.”

### Expected symptom

```text
ping 8.8.8.8        → success   (or ping 192.168.10.1)
ping google.com     → fail      (name resolution)
```

In PT, if external ICMP is limited, use:

```text
ping 192.168.10.1           → success
ping dns.lab.local          → fail when DNS broken
```

(with A record on DNS-SRV — see `configs/DNS-SRV-notes.md`)

### Investigate

```text
Valid IP + gateway ping OK?
        ↓
ping by IP OK, ping by name FAIL?  → DNS
        ↓
Check DNS server field on client
        ↓
DNS server reachable? (ping DNS IP)
        ↓
Record exists? (server DNS service on?)
```

### Interview sentence

> “The network layer is working, but name resolution is failing.”

### Fix

Restore correct DNS (`192.168.10.10` or `8.8.8.8` / DHCP `dns-server`).

---

## Scenario C: Gateway failure

### Break

Give PC-USER a correct host IP but **wrong gateway**:

- IP: `192.168.10.25`
- Mask: `255.255.255.0`
- Gateway: `192.168.10.99` (or `192.168.20.1`)
- DNS: anything

### Expected symptom

```text
ping 192.168.10.12   → may succeed (same subnet, no gateway needed)
ping 192.168.10.1    → fail
Websites / other VLANs → fail
```

### Investigate

```text
ipconfig → note gateway
   ↓
ping gateway IP
   ↓
ARP / Simulation: where does the frame go?
   ↓
Compare to documented gateway 192.168.10.1
```

### Fix

Set gateway to `192.168.10.1` (or return to DHCP).

### Ticket note (example)

> Host had valid addressing in 192.168.10.0/24 but default gateway was incorrect. Corrected to 192.168.10.1; off-subnet connectivity restored.

---

## Side-by-side cheat sheet

| Test | DHCP fail | DNS fail | Gateway fail |
| --- | --- | --- | --- |
| `ipconfig` IP | APIPA / none | Valid | Valid |
| Ping gateway | Fail / N/A | OK | **Fail** |
| Ping same-subnet peer | Fail / flaky | OK | OK |
| Ping public IP | Fail | **OK** | Fail |
| Ping by name | Fail | **Fail** | Fail |

---

## Restore checklist

- [ ] `service dhcp` on R1  
- [ ] Pool HR: `192.168.10.0/24`, default-router `192.168.10.1`  
- [ ] PC-USER port VLAN 10  
- [ ] Gateway `192.168.10.1`  
- [ ] DNS valid  
- [ ] Baseline pings pass again  
