# Incident Report: Controlled Nginx Service Outage

**Incident ID:** HOMELAB-INC-2026-001  
**Date:** August 21, 2026  
**Status:** Resolved  
**Environment:** Proxmox HA Lab (single-node phase)  
**Affected system:** VM 100 (`debian-test`)  
**Affected service:** Nginx HTTP service on TCP port 80  
**Classification:** Planned failure exercise  
**Author:** Anthony Brionez ([@RedTsugumori](https://github.com/RedTsugumori))  

## Executive summary

A controlled service outage was performed to practice detecting, diagnosing, and recovering from an application-level failure.

Nginx was intentionally stopped on VM 100 while the virtual machine remained running. External testing confirmed that the VM continued responding to network pings but stopped accepting TCP connections on port 80. This demonstrated the difference between host availability and service availability.

Nginx was manually restarted, and service recovery was verified from an external Windows client. No data was lost, and no production users or systems were affected.

## Incident results

| Event | Time |
|---|---:|
| Nginx stopped | 1:03:35 PM PDT |
| Outage detected externally | 1:06:22 PM PDT |
| Nginx restarted | 1:08:31 PM PDT |
| Recovery confirmed externally | 1:12:03 PM PDT |
| **Confirmed incident duration** | **8 minutes, 28 seconds** |
| **Detection time** | **2 minutes, 48 seconds** |
| **Recovery after detection** | **5 minutes, 40 seconds** |

## Impact

The outage affected only the Nginx test service in the isolated lab environment.

- The webpage became unavailable.
- TCP connections to port 80 failed.
- The VM continued responding to ICMP ping.
- SSH access remained available.
- No data was lost.
- No production users or systems were affected.

## Detection

The outage was detected manually from an external Windows client using:

`Test-NetConnection 10.0.0.45 -Port 80`

The test produced the following results:

- `PingSucceeded: True`
- `TcpTestSucceeded: False`

These results confirmed that the VM and its network connection remained operational while the Nginx service was unavailable.

## Timeline

| Time | Event |
|---|---|
| 1:03:31 PM PDT | Controlled failure test initiated. |
| 1:03:35 PM PDT | Nginx entered the `inactive (dead)` state. |
| 1:06:22 PM PDT | External testing detected that TCP port 80 was unavailable. |
| 1:08:31 PM PDT | Nginx was manually restarted. |
| 1:12:03 PM PDT | External testing confirmed that TCP port 80 was reachable. |

## Root cause

The immediate cause was an intentional administrative stop of the Nginx service using `systemctl stop nginx`.

The exercise simulated an application-level service failure rather than a VM, Proxmox host, or network failure.

## Contributing factors

The following conditions increased the simulated detection and recovery time:

- No continuous service monitoring was configured.
- Detection depended on a manual TCP test.
- No automatic alert was generated.
- Recovery depended on manually restarting Nginx.
- External recovery verification was performed manually.

## Resolution

Nginx was restarted using `systemctl start nginx`.

The service state was verified as `active (running)` inside the VM. An external Windows client then successfully connected to TCP port 80, confirming that the service was available to network clients again.

## What went well

- The failure was performed in an isolated lab environment.
- The VM remained reachable throughout the incident.
- SSH access remained available for recovery.
- TCP testing isolated the failure to the web service.
- Nginx restarted without errors.
- External testing confirmed recovery.
- Exact timestamps and screenshots were collected.

## Improvement opportunities

- Add continuous HTTP or TCP monitoring.
- Generate an alert when the service becomes unavailable.
- Reduce the time between detection and recovery.
- Test recovery from an unexpected process crash.
- Investigate automatic service-restart policies.
- Use VM 101 as an independent monitoring system.

## Corrective actions

| Action | Priority | Status |
|---|---|---|
| Configure continuous monitoring from VM 101 | High | Planned |
| Add an HTTP health check for Nginx | High | Planned |
| Configure outage notifications | Medium | Planned |
| Test recovery from an unexpected Nginx process failure | Medium | Planned |
| Investigate automated service recovery | Medium | Planned |
| Repeat the exercise and compare recovery times | Low | Planned |

## Lessons learned

A running VM does not guarantee that every service inside it is available.

Host-level checks such as ping can confirm that a machine is reachable, but service-level checks are required to determine whether an application is functioning. Reliable monitoring should therefore test the actual service from the perspective of an external client.

This exercise also demonstrated the importance of independently verifying recovery instead of assuming that a successful service-start command restored availability.
