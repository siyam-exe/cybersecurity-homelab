# Metasploitable Tests

Metasploitable tests prove the legacy-server path.

The lab uses two kinds of evidence:

| Evidence Type | Source |
|---|---|
| Network IDS evidence | Suricata on `vboxnet0` |
| Host/service evidence | Metasploitable forwarded syslog |

## Requirements

Start:

```bash
cd /home/<HOMELAB_USER>/wazuh-docker/single-node
docker-compose up -d
sudo systemctl start suricata
VBoxManage startvm metasploitable2 --type headless
```

Confirm:

```bash
VBoxManage list runningvms
ping -c 2 <METASPLOITABLE_IP>
```

## Nmap Scan

From `attack_station`:

```bash
nmap -sT -Pn -p 21,22,23,25,80,139,445,3306 <METASPLOITABLE_IP>
```

Expected:

- Suricata scan or port-sweep alert
- destination port panels update
- source/destination IP panels update
- Wazuh scan correlation if enough scan alerts fire

## SSH Failed Login

From `attack_station`:

```bash
ssh <METASPLOITABLE_USER>@<METASPLOITABLE_IP>
```

Enter an intentionally wrong password for a controlled failed login.

Expected:

- Metasploitable syslog entry
- Wazuh SSH/auth failure rule
- Network IDS & Legacy Server Metasploitable auth panel

Do not document real credentials in screenshots or notes.

## Service Enumeration

From `attack_station`:

```bash
nmap -sV -Pn -p 21,22,23,25,80,139,445,3306 <METASPLOITABLE_IP>
```

Expected:

- Suricata network alerts where signatures match
- service-related syslog where services log activity
- dashboard destination ports and raw IDS table update

## Exploit Attempt Note

Real exploit attempts are optional and should be handled carefully.

This guide does not claim exploit-specific detections unless they were tested and confirmed. If you run a Metasploit module later, record:

- module name
- source
- target
- Suricata signature
- Wazuh rule
- target syslog evidence
- dashboard panel
- whether it worked or only partially worked

## Stop After Testing

Run:

```bash
VBoxManage controlvm metasploitable2 acpipowerbutton
sudo systemctl stop suricata
```

## Next Step

Continue to [Reading The Alerts](./06-reading-the-alerts.md).
