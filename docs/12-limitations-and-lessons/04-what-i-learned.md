# What I Learned

This project is more than installing tools.

The main learning came from connecting pieces into a working detection flow.

## Architecture Lessons

The lab became cleaner when each component had a specific role:

| Component | Role |
|---|---|
| Tailscale | private remote access boundary |
| UFW | host firewall layer |
| Docker | vulnerable web app runtime |
| Caddy | logged web entry point |
| Wazuh | SIEM/XDR platform |
| Suricata | network IDS |
| rsyslog | Metasploitable syslog receiver |
| dashboards | investigation views |

## Security Lessons

Important lessons:

- vulnerable targets should not auto-start
- Docker port bindings matter as much as firewall rules
- dashboards should be based on detection logic, not loose filters
- raw logs are necessary for investigation
- a vulnerability inventory is not the same as attack detection
- network IDS and web logs answer different questions

## Documentation Lessons

Good documentation should show:

- what command to run
- why it is being run
- what result to expect
- how to verify it
- what to do if it fails

Failed attempts should go in troubleshooting, not the main path.

## SIEM Lessons

A useful SIEM workflow looks like:

```text
event source
-> parsed fields
-> rule or signature
-> dashboard signal
-> raw evidence
-> explanation
```

If any part is missing, the detection becomes harder to trust.

## Next Step

Continue to [Future Roadmap](./05-future-roadmap.md).
