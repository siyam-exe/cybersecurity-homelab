# Syslog Forwarding

Metasploitable forwards syslog to the homelab. The homelab writes those logs to a dedicated file, and Wazuh collects that file later.

## Final Log Flow

```text
Metasploitable
-> UDP 514
-> homelab rsyslog on vboxnet0
-> /var/log/metasploitable/metasploitable.log
-> Wazuh agent later
```

## Prepare The Homelab Log File

Run on the homelab:

```bash
sudo apt install -y rsyslog
sudo install -d -o syslog -g adm -m 2750 /var/log/metasploitable
sudo touch /var/log/metasploitable/metasploitable.log
sudo chown syslog:adm /var/log/metasploitable/metasploitable.log
sudo chmod 0640 /var/log/metasploitable/metasploitable.log
```

Command breakdown:

| Command | Why it is used |
|---|---|
| `install -d -o syslog -g adm -m 2750 ...` | Creates the log directory with the owner, group, and permissions set in one clean command. |
| `touch ...` | Creates the log file if it does not already exist. |
| `chown syslog:adm ...` | Lets rsyslog write the file and lets the `adm` group read it. |
| `chmod 0640 ...` | Allows owner read/write, group read, and no access for everyone else. |

## Configure Homelab rsyslog Listener

Create the config:

```bash
sudo nano /etc/rsyslog.d/20-metasploitable.conf
```

Add:

```conf
module(load="imudp")
input(type="imudp" port="514" address="<VBOXNET_HOST_IP>")

if $fromhost-ip == '<METASPLOITABLE_IP>' then /var/log/metasploitable/metasploitable.log
& stop
```

Config breakdown:

| Line | Meaning |
|---|---|
| `module(load="imudp")` | Enables rsyslog's UDP input module. |
| `input(... port="514" address="<VBOXNET_HOST_IP>")` | Listens for syslog only on the VirtualBox host-only address. |
| `if $fromhost-ip == '<METASPLOITABLE_IP>' then ...` | Writes only Metasploitable messages to the dedicated file. |
| `& stop` | Stops processing after the message is written, preventing duplicate handling. |

Why this is tightened:

- rsyslog listens only on the VirtualBox host-only address
- only the Metasploitable source address is written to the dedicated file
- random devices are not invited to send logs to this listener

## Validate And Restart rsyslog

Run:

```bash
sudo rsyslogd -N1
sudo systemctl restart rsyslog
sudo systemctl status rsyslog
```

## Configure Metasploitable Forwarding

Inside the Metasploitable VM, add a remote syslog destination:

```bash
sudo nano /etc/rsyslog.conf
```

Add:

```conf
*.* @<VBOXNET_HOST_IP>
```

Restart syslog inside the VM:

```bash
sudo service rsyslog restart
```

## Test Forwarding

Inside Metasploitable:

```bash
logger "metasploitable syslog test"
```

On the homelab:

```bash
sudo tail -n 10 /var/log/metasploitable/metasploitable.log
```

Expected result:

- the test message appears in the homelab log file
- the file is readable by root and the `adm` group
- Wazuh can ingest it later as a local file

## Next Step

Continue to [Start Stop Metasploitable](./07-start-stop-metasploitable.md).
