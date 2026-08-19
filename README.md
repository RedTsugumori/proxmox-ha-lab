# High-Availability Infrastructure Lab

A home lab for designing, testing, and documenting resilient infrastructure. A Minecraft server stack is used as the initial workload because it provides a practical way to test service health, state consistency, failover, and recovery.

## Project Goals

- Explore high-availability concepts in a hands-on environment
- Detect service or node failures
- Redirect clients to a healthy service instance
- Minimize interruption during failover
- Limit data loss to a target of 10 seconds or less
- Document testing, limitations, and design decisions

## Current Status

This project is in the design and early experimentation stage. The first test environment uses Linux virtual machines, a Velocity proxy, and primary and secondary Paper servers.

The current design is a learning environment, not a production-ready HA system. Components will be tested individually before automatic failover is considered complete.

## Initial Workload

Minecraft is the first application used to exercise the HA design. It is a workload within the lab rather than the primary subject of the project.

The workload provides several useful failure cases:

- Stateful world data that must remain consistent
- Long-running client connections
- A proxy layer that can route clients
- Observable service interruptions
- Measurable recovery time and potential data loss

## Documentation

- [Initial architecture](docs/architecture.md)
- [Architecture tracking issue](../../issues/1)

## Technologies

- Proxmox VE or a virtualized test environment
- Ubuntu Server
- Velocity proxy
- Paper server
- Java
- GitHub for change tracking and documentation

## Project Principles

- Clearly separate implemented features from planned features
- Record failed tests as well as successful tests
- Keep credentials, private keys, and sensitive network details out of Git
- Prefer repeatable configurations and documented recovery procedures
