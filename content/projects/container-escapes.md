---
title: "Container Escapes: from script kiddie to production reality"
description: "Breaking out of Docker and Kubernetes containers in a controlled lab, from one-line misconfigurations to runtime CVEs, and what actually stops them in production."
date: 2026-11-04
status: active
stack: [Docker, Kubernetes, Linux, runc, Falco]
draft: true
---

# Container Escapes: from script kiddie to production reality

A container is not a virtual machine. It is just a Linux process with a restricted view of the system, and it shares the host's kernel. When one of those restrictions is missing or broken, the process can reach the host. This project shows, in a <ins>**controlled lab**</ins>, how that happens: first the trivial escapes caused by misconfiguration, then real runtime and kernel vulnerabilities, and finally what a hardened production cluster does to stop them.

> Everything here runs in an isolated VM that I own. Don't try any of it on systems you are not authorised to test.

## Lab setup

- Host: disposable VM (distro, kernel version)
- Docker version / runc version
- Kubernetes: kind / k3s (version)
- Snapshot before each test so the host can be reset quickly

## What a container really is

- **Namespaces**: what the process can *see* (pid, mnt, net, user, ...)
- **cgroups**: what it can *use*
- **Capabilities**: which root privileges it keeps
- **seccomp / AppArmor / SELinux**: which syscalls and actions it is denied

Each escape below removes or bypasses one of these layers.

## Level 1: script kiddie (misconfigurations)

No exploit needed, only a bad `docker run` flag or YAML field.

### `--privileged`

### Mounted Docker socket (`/var/run/docker.sock`)

### `hostPath: /` and sensitive mounts

### `hostPID` / `hostNetwork`

### `CAP_SYS_ADMIN` and the cgroup v1 `release_agent` trick

## Level 2: real vulnerabilities

The container is configured correctly, but the runtime or the kernel is not patched.

### runc: CVE-2019-5736 (overwriting the host runc binary)

### runc "Leaky Vessels": CVE-2024-21626 (leaked file descriptor)

### Kernel: Dirty Pipe, CVE-2022-0847

## Level 3: production reality (Kubernetes)

- Service account tokens and RBAC: going from a pod to the cluster
- Pod Security Admission (`restricted` profile)
- Rootless containers and user namespaces
- Sandboxed runtimes: gVisor, Kata Containers

## Detection

- Falco rules that fire on each escape above
- What the logs look like (auditd, Kubernetes audit logs)

## Hardening checklist

| Escape | Mitigation | Blocked? |
|---|---|---|
| `--privileged` | | |
| Docker socket | | |
| hostPath | | |
| `release_agent` | | |
| CVE-2019-5736 | | |
| CVE-2024-21626 | | |
| Dirty Pipe | | |

## Results

## Takeaways
