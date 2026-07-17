# DVWA Tests

DVWA tests prove the web log and web detection path.

The intended flow is:

```text
attack_station browser or curl
-> Caddy DVWA port
-> Caddy JSON access log
-> Wazuh custom web rules
-> Web Attack Lab dashboard
```

Suricata can also see network evidence when it is running.

## Requirements

Start:

```bash
cd /home/<HOMELAB_USER>/wazuh-docker/single-node
docker-compose up -d
docker start dvwa caddy-juice
sudo systemctl start suricata
```

Suricata is optional for pure Caddy/Wazuh web-rule testing, but useful when comparing web logs to IDS evidence.

## Normal DVWA Request

From `attack_station`:

```bash
curl -i http://<HOMELAB_TAILSCALE_IP>:8080/login.php
```

Expected:

- Caddy access log entry
- Wazuh general Caddy rule
- dashboard web traffic count increases

## SQL Injection-Like Request

From `attack_station`:

```bash
curl "http://<HOMELAB_TAILSCALE_IP>:8080/vulnerabilities/sqli/?id=%27%20OR%201%3D1--&Submit=Submit"
```

Expected:

| Evidence | Expected |
|---|---|
| Caddy JSON | URI contains SQLi-like pattern |
| Wazuh | SQLi-like web rule |
| Dashboard | Web Attack Lab SQLi panel |
| Suricata | optional SQLi-like signature if enabled rule matches |

## Traversal-Like Request

From `attack_station`:

```bash
curl "http://<HOMELAB_TAILSCALE_IP>:8080/%2e%2e/%2e%2e/etc/passwd"
```

Expected:

- traversal-like Wazuh rule
- top attacked URI panel update
- raw web log row with the encoded path

## Command Injection-Like Request

From `attack_station`:

```bash
curl "http://<HOMELAB_TAILSCALE_IP>:8080/vulnerabilities/exec/?ip=localhost%3Bwhoami&Submit=Submit"
```

Expected:

- command-injection-like Wazuh rule
- Web Attack Lab command injection counter

This test is detection-focused. It is enough that the request pattern is logged and detected.

## Brute Force Candidate Test

Send repeated login/brute-force candidate requests:

```bash
for i in $(seq 1 10); do
  curl -s -o /dev/null "http://<HOMELAB_TAILSCALE_IP>:8080/login.php"
done
```

Expected:

- login/brute-force candidate rule
- possible frequency correlation rule if threshold is reached

## Next Step

Continue to [Juice Shop Tests](./04-juice-shop-tests.md).
