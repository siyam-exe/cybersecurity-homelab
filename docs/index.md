# Beginner Roadmap

This is the recommended path for building the homelab from a blank server to a working Wazuh detection lab.

The goal is not only to run tools. The goal is to understand what each piece does, how logs move through the system, and how Wazuh turns those logs into useful security evidence.

## How To Use This Guide

Follow the sections in order the first time. Later, use the playbooks when you only need to start, stop, test, or troubleshoot the lab.

Each section should leave you with:

- a working component
- a reason for why that component exists
- commands you can repeat
- a verification step before moving on

## Setup Order

| Step | Section | What You Build | Before Moving On |
|---:|---|---|---|
| 1 | [Overview](./00-overview/01-project-introduction.md) | Understand the hardware, lab goal, architecture, network plan, and safety scope. | You can explain what the homelab, attack_station, Docker, Wazuh, Suricata, Caddy, and Metasploitable each do. |
| 2 | [OS Installation](./01-os-installation/01-ubuntu-server-choice.md) | Install Ubuntu Server on the homelab and complete first login. | The homelab boots into Ubuntu Server and can run basic update commands. |
| 3 | [Remote Access And Firewall](./02-remote-access-and-firewall/01-tailscale-setup.md) | Configure SSH, Tailscale, UFW, and the remote access model. | You can SSH to the homelab through Tailscale and understand which ports should be reachable. |
| 4 | [Docker Platform](./03-docker-platform/01-why-docker.md) | Install Docker and create the container foundation for lab services. | `docker ps` works and the lab Docker network exists. |
| 5 | [Vulnerable Web Apps](./04-vulnerable-web-apps/01-web-lab-overview.md) | Deploy DVWA, OWASP Juice Shop, and Caddy as the logged web access path. | Juice Shop and DVWA can be started only when needed, and Caddy can write JSON access logs. |
| 6 | [Metasploitable](./05-metasploitable/01-why-metasploitable.md) | Add Metasploitable 2 as a legacy vulnerable server in VirtualBox. | The VM can start, has a host-only IP, and can forward syslog to the homelab. |
| 7 | [Wazuh](./06-wazuh/01-wazuh-overview.md) | Install Wazuh, enroll the homelab agent, and collect host/web/VM logs. | The Wazuh dashboard opens through Tailscale and the homelab agent appears active. |
| 8 | [Suricata](./07-suricata/01-why-suricata.md) | Add network IDS visibility for scans and suspicious traffic. | Suricata creates EVE alerts and Wazuh receives them. |
| 9 | [Detection Engineering](./08-detection-engineering/01-detection-strategy.md) | Add and explain rules for web attacks, scans, host events, Docker events, and Metasploitable activity. | Controlled test events create expected Wazuh alerts. |
| 10 | [Dashboards](./09-dashboards/01-dashboard-design.md) | Build or import the four final Wazuh dashboards. | Dashboards show data and raw investigation panels work for recent alerts. |
| 11 | [Operations](./10-operations/01-start-lab.md) | Learn how to start, stop, verify, rotate logs, back up, and recover after reboot. | You can bring the lab up and return it to idle state safely. |
| 12 | [Attack Simulation](./11-attack-simulation/01-testing-rules.md) | Run safe tests and read the resulting alerts. | You can map a test action to a log source, Wazuh rule, and dashboard panel. |
| 13 | [Limitations And Lessons](./12-limitations-and-lessons/01-hardware-limitations.md) | Understand hardware limits, Wazuh limits, detection limits, and future improvements. | You know what this lab proves and what it does not prove. |
| 14 | [Playbooks](./13-playbooks/01-start-wazuh.md) | Use copy-paste operational command guides. | You can run common tasks without rereading the full tutorial. |

## Section Details

### 00 - Overview

What this teaches:

- why the homelab exists
- the hardware roles
- the final architecture
- the network and port plan
- safety rules for vulnerable services

Complete this section when:

- the architecture makes sense at a high level
- you know the default state is to keep vulnerable targets stopped unless testing
- you know remote access is through Tailscale, not the public internet

### 01 - OS Installation

What this teaches:

- why Ubuntu Server was selected
- how to create the bootable USB
- how to install Ubuntu Server
- how to complete first login, updates, SSH, timezone, and hostname setup

Complete this section when:

- the homelab boots into Ubuntu Server
- the server has a working user account
- updates can run successfully
- the hostname and timezone are correct enough for log correlation

### 02 - Remote Access And Firewall

What this teaches:

- why Tailscale is the remote access layer
- how the attack_station reaches the homelab from anywhere
- how UFW supports the access model
- why Docker port bindings matter before firewall rules

Complete this section when:

- SSH works through `<HOMELAB_TAILSCALE_IP>`
- broad public exposure is avoided
- the intended ports are documented
- the firewall strategy is clear

### 03 - Docker Platform

What this teaches:

- why Docker is used
- how containers are managed
- how the lab network is created
- why restart policies are set manually

Complete this section when:

- Docker is installed
- the lab network exists
- you understand why vulnerable containers should not auto-start

### 04 - Vulnerable Web Apps

What this teaches:

- why DVWA and Juice Shop are used
- why Caddy sits in front of them
- why Caddy JSON logs are useful for Wazuh
- how to start and stop web targets safely

Complete this section when:

- Juice Shop can run behind Caddy
- DVWA can run behind Caddy
- direct access paths do not bypass the intended logging path
- Caddy writes request logs for both apps

### 05 - Metasploitable

What this teaches:

- why Metasploitable 2 is used
- how VirtualBox host-only networking works
- how Tailscale subnet routing reaches the VM
- how Metasploitable syslog reaches the homelab

Complete this section when:

- Metasploitable starts headless
- the homelab can reach the VM on the host-only network
- the attack_station/Kali route works if remote testing is needed
- forwarded syslog appears on the homelab

### 06 - Wazuh

What this teaches:

- why Wazuh is the SIEM for the lab
- how the Docker single-node stack is placed
- how the homelab host agent is enrolled
- how host, Caddy, Metasploitable, Suricata, Docker, FIM, and vulnerability data enter Wazuh

Complete this section when:

- Wazuh manager, indexer, and dashboard can start
- the dashboard is reachable through Tailscale
- the homelab agent is active
- key log files are collected
- FIM and vulnerability detection are verified

### 07 - Suricata

What this teaches:

- why Wazuh web logs and Suricata IDS alerts are different
- how Suricata monitors `vboxnet0` and `tailscale0`
- how EVE JSON alerts enter Wazuh
- what scan/network evidence looks like

Complete this section when:

- Suricata starts on demand
- Suricata produces alerts
- Wazuh receives Suricata alerts
- scan tests create expected IDS evidence

### 08 - Detection Engineering

What this teaches:

- how detection logic is organized
- which detections are built-in Wazuh behavior
- which detections are custom Wazuh rules
- which detections come from Suricata signatures
- how tests prove detections

Complete this section when:

- web attack rules trigger for controlled requests
- scan correlation rules trigger for port scan activity
- host, Docker, FIM, and Metasploitable detections are understood
- test results are recorded honestly

### 09 - Dashboards

What this teaches:

- why the lab uses four dashboards
- what each dashboard is supposed to answer
- which panels are overview panels
- which panels are raw investigation panels
- how to export/import dashboard objects

Complete this section when:

- all four dashboards exist
- panels use meaningful rule/source fields
- raw log tables support investigation
- screenshots are added to `assets/dashboards/`

### 10 - Operations

What this teaches:

- how to start the lab
- how to stop the lab
- how to run health checks
- how log rotation works
- what to back up
- what to check after reboot

Complete this section when:

- you can start only the services you need
- you can stop Wazuh, web targets, Suricata, and Metasploitable safely
- you can verify the lab is in idle state

### 11 - Attack Simulation

What this teaches:

- safe testing boundaries
- Nmap scan tests
- DVWA tests
- Juice Shop tests
- Metasploitable tests
- how to read Wazuh alerts after each test

Complete this section when:

- each test maps to an expected log source
- each test maps to an expected Wazuh rule or Suricata signature
- dashboard evidence can be explained without exaggerating exploit success

### 12 - Limitations And Lessons

What this teaches:

- hardware constraints
- Wazuh limitations
- detection limitations
- what worked well
- what could be improved later

Complete this section when:

- the reader understands this is a controlled lab
- the reader understands what Wazuh can and cannot prove
- future improvements are clearly separated from the current build

### 13 - Playbooks

What this teaches:

- fast operational commands for common tasks
- how to start and stop individual lab components
- how to run health checks
- how to export dashboards
- how to return the whole lab to idle state

Complete this section when:

- the playbooks can be copied and used without reading the full explanation pages
- every risky command has a purpose and verification step

## Final Build State

The finished lab should have:

- Ubuntu Server running on the homelab
- Tailscale private access
- UFW rules aligned to the port plan
- Docker installed
- DVWA and Juice Shop behind Caddy
- Metasploitable 2 available through VirtualBox when needed
- Wazuh installed and stopped unless needed
- homelab Wazuh agent enrolled
- Suricata installed and stopped unless testing
- Wazuh detections for host, Docker, web, IDS, FIM, vulnerability, and Metasploitable activity
- four Wazuh dashboards for SOC overview, web attacks, network IDS, and host infrastructure

## Default Runtime Policy

Keep the lab quiet by default:

| Component | Default State |
|---|---|
| SSH | Running |
| Tailscale | Running |
| Wazuh | Stopped unless reviewing alerts or testing |
| Suricata | Stopped unless testing network detections |
| Juice Shop | Stopped unless testing |
| DVWA | Stopped unless testing |
| Caddy web proxy | Stopped unless testing |
| Metasploitable | Powered off unless testing |
