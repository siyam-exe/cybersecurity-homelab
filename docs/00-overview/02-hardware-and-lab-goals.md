# Hardware And Lab Goals

This homelab is intentionally small. The design is shaped by the hardware, which means the lab has to be selective about what runs and when it runs.

The goal is not to build the biggest possible lab. The goal is to build a lab that can be understood, operated, tested, and explained.

## Hardware Summary

| Device | Role |
|---|---|
| Dell Inspiron N4030 | Main server |
| Acer Nitro V15 | Admin, analyst, testing, and documentation workstation |

## Dell Inspiron N4030

The Dell is the main server for this lab.

It runs:

- Ubuntu Server 22.04.5 LTS
- Docker
- Wazuh central components
- Wazuh agent
- vulnerable web containers
- Caddy reverse proxy
- Suricata IDS
- rsyslog receiver
- VirtualBox for Metasploitable 2

The Dell is managed headlessly over SSH. There is no need for a desktop environment on the server.

### Observed System Profile

The Wazuh dashboard reported the Dell as:

| Item | Value |
|---|---|
| Operating system | Ubuntu 22.04.5 LTS |
| CPU | Intel Core i5 M 480 @ 2.67 GHz |
| Cores shown in Wazuh | 4 |
| Memory shown in Wazuh | about 7.6 GB usable |
| Hostname | homelab |

This is enough for the lab, but it is not enough to treat every tool like a permanent enterprise service.

## Acer Nitro V15

The Acer is the workstation.

It is used for:

- writing documentation
- opening the Wazuh dashboard
- SSH administration
- running Nmap tests
- future Kali VM testing
- future Windows/Sysmon/Active Directory lab phases

The Acer is intentionally not the main server. It is the stronger machine, so it can host heavier future VMs, but the current Wazuh homelab is centered around the Dell.

## Why The Lab Uses Manual Start

Several parts of the lab are intentionally stopped by default:

| Component | Default State | Reason |
|---|---|---|
| Wazuh stack | Stopped unless needed | Saves RAM and CPU on the Dell |
| Suricata | Stopped unless testing | Avoids unnecessary packet capture overhead |
| Juice Shop | Stopped unless testing | Reduces exposed vulnerable services |
| DVWA | Stopped unless testing | Reduces exposed vulnerable services |
| Caddy web proxy | Stopped unless testing | Only needed when web targets are active |
| Metasploitable 2 | Powered off unless testing | Reduces attack surface and resource use |

This is a deliberate design choice.

For a real company, monitoring should run all the time. For this personal homelab, the priority is controlled learning on limited hardware. Starting the stack only when needed keeps the system cleaner and easier to reason about.

## Main Lab Goals

The hardware supports a few focused goals:

### 1. Build a realistic small-network security lab

The lab is modeled like a small company network:

- one monitored Linux server
- vulnerable internal applications
- a legacy vulnerable server
- remote admin access
- a SIEM dashboard
- network IDS visibility

### 2. Learn detection from evidence

The lab should show where evidence comes from:

- Caddy logs for web requests
- Ubuntu auth logs for SSH and sudo
- Docker events for container activity
- Metasploitable syslog for legacy server events
- Suricata alerts for network activity
- Wazuh FIM for file changes

### 3. Keep the setup understandable

Every major part should answer:

- what does it do?
- why is it here?
- what logs does it create?
- how does Wazuh see it?
- how do I verify it works?

### 4. Avoid unnecessary always-on exposure

Vulnerable targets are useful for testing, but they should not sit exposed all day. The lab is designed so those services can be started for a session and stopped afterward.

## Hardware Constraints

The Dell can run this lab, but the constraints matter:

| Constraint | Design Impact |
|---|---|
| Older CPU | Avoid heavy always-on workloads |
| Limited usable RAM | Run Wazuh and targets intentionally |
| Older storage | Keep log retention and dashboards reasonable |
| Single small server | Use Docker for most services instead of many VMs |

This is why the lab uses one lightweight Metasploitable VM and Docker containers for the rest.

## Section Summary

The hardware design is intentionally practical. The Dell is powerful enough to run the lab, but not powerful enough to treat every service as always-on infrastructure. The Acer stays as the admin, analyst, and testing workstation.

This is why the rest of the guide uses an on-demand model for Wazuh, Suricata, vulnerable targets, and Metasploitable.

## Next Step

Continue to:

[03 - Final Architecture](./03-final-architecture.md)
