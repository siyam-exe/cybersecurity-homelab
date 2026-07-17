# Web Attack Lab

The Web Attack Lab dashboard focuses on Juice Shop and DVWA.

It answers:

```text
What web attack behavior did Wazuh detect against the vulnerable apps?
```

![Web Attack Lab dashboard](../../assets/dashboards/web-attack-lab.png)

## Dashboard Purpose

| Purpose | Explanation |
|---|---|
| Web attack investigation | Show SQLi, XSS, command injection, traversal, upload, recon, and brute-force detections. |
| App comparison | Separate Juice Shop and DVWA evidence. |
| Raw log review | Keep the exact URI and status code available for writeups. |
| Caddy vs Suricata comparison | Show HTTP-layer evidence next to IDS evidence when available. |

## Panel Layout

| Section | Panels |
|---|---|
| KPI row | Total web attacks, SQLi, XSS, command injection, traversal, web brute force, suspicious upload, recon/admin discovery |
| Charts | Web attacks over time, web attack type breakdown, Juice Shop vs DVWA, top attacked URLs, HTTP status codes, web source IPs, Caddy vs Suricata evidence |
| Bottom table | Raw web attack logs |

## Detection Coverage

| Detection | Wazuh Rule / Source |
|---|---|
| General Caddy request | `110100` |
| SQLi-like request | `110101` |
| Traversal-like request | `110102` |
| XSS-like request | `110103` |
| Command injection-like request | `110104` |
| Recon/admin path discovery | `110105` |
| Suspicious upload/dangerous file request | `110106` |
| Login/brute-force candidate | `110107` |
| Web brute-force correlation | `110108` |
| Suricata web connection/signature evidence | SIDs `1000003` to `1000006`, Wazuh `86601` |

## Important Fields

| Panel | Fields |
|---|---|
| Web attack count | `rule.id`, `rule.description` |
| Web attacks over time | `timestamp`, `rule.description` |
| App split | `data.homelab.app` |
| Source IPs | `data.request.client_ip`, `data.src_ip` |
| Top URLs | `data.request.uri` |
| Status codes | `data.status` |
| Caddy vs Suricata | `data.homelab.source`, `rule.groups` |
| Raw logs | `timestamp`, `data.request.uri`, `rule.description`, `data.status`, `rule.id` |

## Why `rule.description` Matters

The first version of the web attack timeline showed raw rule IDs.

That worked technically, but it was not friendly to read:

```text
110103
110104
110105
110106
110108
```

The final version uses `rule.description`, so the chart legend shows readable detection names.

## How To Use It

For a web attack test:

1. Start Juice Shop, DVWA, and Caddy.
2. Generate one controlled request from `attack_station`.
3. Open Web Attack Lab.
4. Check the matching KPI.
5. Review the timeline and app split.
6. Open raw web logs and confirm the URI.

Example flow:

```text
XSS-like test
-> KPI "XSS" increases
-> Web attacks over time shows XSS-like request
-> Raw web logs show the URI
```

## Honest Limits

The web rules are intentionally readable lab detections.

They are useful for learning:

- common attack categories
- Wazuh custom rules
- dashboards driven by detection logic
- application-layer evidence

They are not a complete production WAF rule set.

## Next Step

Continue to [Network IDS & Legacy Server](./04-network-ids-legacy-server.md).
