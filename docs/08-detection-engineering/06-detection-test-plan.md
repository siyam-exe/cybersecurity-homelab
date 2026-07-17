# Detection Test Plan

This plan is the repeatable checklist for proving that detections work.

It is written so the same plan can be used before and after dashboard changes.

![Attack to detection workflow](../../assets/architecture/attack-to-detection-flow.png)

## Test Rules

Use these rules for every test:

- only test systems you own
- start only the services required for the test
- generate one controlled test type at a time
- write down expected log source before running the test
- confirm the raw log before trusting the dashboard
- stop vulnerable targets and Suricata afterward

## Startup Order

For a full detection test session:

```bash
cd /home/<HOMELAB_USER>/wazuh-docker/single-node
docker-compose up -d
sudo systemctl start wazuh-agent
sudo systemctl start suricata
docker start juice-shop dvwa caddy-juice
VBoxManage startvm <METASPLOITABLE_VM_NAME> --type headless
```

Only start the pieces required for the specific test.

Command breakdown:

| Command | Why |
|---|---|
| `docker-compose up -d` | Starts Wazuh in the background. |
| `systemctl start wazuh-agent` | Ensures local log collection is active. |
| `systemctl start suricata` | Enables network IDS only for the test session. |
| `docker start ...` | Starts vulnerable web targets only when needed. |
| `VBoxManage startvm ... --type headless` | Starts Metasploitable without a desktop window. |

## Test Matrix

| Test ID | Test | Source | Target | Expected Log Source | Expected Rule/Signature | Dashboard |
|---|---|---|---|---|---|---|
| WEB-01 | Normal Juice Shop request | `attack_station` | Juice Shop via Caddy | Caddy JSON | `110100` | Web Attack Lab |
| WEB-02 | SQLi-like request | `attack_station` | Juice Shop/DVWA | Caddy JSON, Suricata if matched | `110101`, Suricata SID `1000004` | Web Attack Lab |
| WEB-03 | Traversal-like request | `attack_station` | Juice Shop/DVWA | Caddy JSON, Suricata if matched | `110102`, Suricata SID `1000005` or `1000006` | Web Attack Lab |
| WEB-04 | XSS-like request | `attack_station` | Juice Shop/DVWA | Caddy JSON | `110103` | Web Attack Lab |
| WEB-05 | Command injection-like request | `attack_station` | DVWA | Caddy JSON | `110104` | Web Attack Lab |
| WEB-06 | Recon/admin path | `attack_station` | Caddy web apps | Caddy JSON | `110105` | Web Attack Lab |
| WEB-07 | Suspicious upload path | `attack_station` | DVWA | Caddy JSON | `110106` | Web Attack Lab |
| WEB-08 | Repeated login/brute-force path | `attack_station` | DVWA/login path | Caddy JSON | `110107`, then `110108` | Web Attack Lab |
| IDS-01 | Nmap scan of homelab lab ports | `attack_station` | homelab Tailscale services | Suricata EVE JSON | SID `1000007`, Wazuh `110120`, `110121` | Network IDS & Legacy Server |
| IDS-02 | Nmap scan of Metasploitable | `attack_station` or Kali | Metasploitable | Suricata EVE JSON | SID `1000002`, Wazuh `110120`, `110121` | Network IDS & Legacy Server |
| META-01 | Metasploitable SSH/auth activity | `attack_station` or Kali | Metasploitable | forwarded syslog | SSH/auth Wazuh rules | Network IDS & Legacy Server |
| HOST-01 | SSH login to homelab | `attack_station` | homelab | Ubuntu auth log | built-in SSH/auth rules | Host & Infrastructure Security |
| HOST-02 | sudo command | homelab shell | homelab | Ubuntu auth log | built-in sudo rules | Host & Infrastructure Security |
| DOCKER-01 | Start/stop vulnerable containers | homelab shell | Docker | Docker listener | `87903`, `87904` | Host & Infrastructure Security |
| DOCKER-02 | Docker exec test | homelab shell | test container | Docker listener | `87907` | Host & Infrastructure Security |
| FIM-01 | Modify a watched config test file | homelab shell | watched path | Wazuh FIM | `550`, `553`, `554` | Host & Infrastructure Security |

## Verification Order

For web tests:

```bash
sudo tail -f /var/log/homelab/caddy-juice/juice-shop-access.log
sudo tail -f /var/log/homelab/caddy-juice/dvwa-access.log
```

For Suricata tests:

```bash
sudo tail -f /var/log/suricata/eve.json | jq 'select(.event_type=="alert")'
```

For Wazuh agent collection:

```bash
sudo grep -Ei 'caddy|suricata|metasploitable' /var/ossec/logs/ossec.log
```

For Wazuh dashboard:

```text
Threat Hunting
-> filter by rule.id or rule.groups
```

Useful filters:

```text
rule.id:(110103 OR 110104 OR 110105 OR 110106 OR 110108)
rule.id:(110120 OR 110121 OR 110122)
rule.groups:suricata
data.homelab.source:caddy
data.homelab.source:suricata
```

## Shutdown Order

After testing:

```bash
docker stop caddy-juice juice-shop dvwa
sudo systemctl stop suricata
VBoxManage controlvm <METASPLOITABLE_VM_NAME> acpipowerbutton
cd /home/<HOMELAB_USER>/wazuh-docker/single-node
docker-compose stop
```

If the VM does not shut down cleanly after a reasonable wait, use the VirtualBox stop playbook.

## Next Step

Continue to [Detection Results](./07-detection-results.md).
