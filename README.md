# Nmap Scan Techniques — Penetration Testing Lab

A hands-on penetration testing lab comparing four Nmap scan techniques against a deliberately vulnerable target (Metasploitable 2) in an isolated virtual network.

## Objective

Demonstrate how different Nmap scans interact with a target, what information each returns, and when to use each technique during the reconnaissance phase of a penetration test.

## Lab Environment

| Role | System | IP Address |
|------|--------|------------|
| Attacker | Kali Linux | 192.168.56.1 |
| Target | Metasploitable 2 | 192.168.56.101 |
| Network | Host-Only (isolated) | — |

**Tools used:** Nmap 7.95, Oracle VirtualBox

## Pre-Scan: Connectivity Test

Before scanning, connectivity between Kali and Metasploitable 2 was verified using `ping` to confirm both VMs were on the same network and reachable.


ping 192.168.56.101
Result: 0% packet loss — target reachable, scans can proceed.

1

Scan 1: Stealth Scan (SYN Scan)
Command:

bash
nmap -sS 192.168.56.101
How it works: Sends a SYN packet to each port. If the port is open, the target replies with SYN-ACK. The scan never completes the three-way handshake — making it faster and harder to detect than a full connect scan.

Key findings:

977 closed TCP ports

23 open ports identified, including:

21/tcp — ftp

22/tcp — ssh

23/tcp — telnet

25/tcp — smtp

80/tcp — http

139/tcp — netbios-ssn

445/tcp — microsoft-ds

3306/tcp — mysql

5432/tcp — postgresql

5900/tcp — vnc

6667/tcp — irc

8180/tcp — unknown

Result: Scan completed in 0.98 seconds — fast and rich with service information.

2

Scan 2: TCP Connect Scan
Command:

bash
nmap -sT 192.168.56.101
How it works: Completes the full TCP three-way handshake with each target port. More reliable than a SYN scan, but noisier — connection attempts are logged by the target.

Result: Similar port/service output to the stealth scan but more detectable on the target's logs.

https://images/tcp-connect-scan.png

Scan 3: UDP Scan
Command:

bash
nmap -sU 192.168.56.101
How it works: Sends UDP packets instead of TCP. Since UDP is connectionless (no handshake), results are less clear — a lack of response may mean open or filtered.

Key findings:

952 closed UDP ports

44 open|filtered UDP ports

Open services identified:

53/udp — domain

111/udp — rpcbind

137/udp — netbios-ns

2049/udp — nfs

Result: Scan took 1027 seconds (~17 minutes) — significantly slower and less reliable than TCP scans.

3

Scan 4: ACK Scan
Command:

bash
nmap -sA 192.168.56.101
How it works: Sends ACK packets to map firewall rules. It does not identify open/closed ports — instead it classifies ports as filtered or unfiltered.

Key findings:

1000 scanned ports reported as unfiltered (reset)

No ports marked as filtered

Result: No firewall filtering observed — traffic is passing freely between the VMs.

4
