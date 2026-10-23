# Molecule Quick Start - Amazon Linux 2023 STIG

## What this is

This `molecule/` directory ships one CI scenario: `default/`. It starts a throwaway Amazon Linux 2023 Docker container, applies the **whole** Ansible Lockdown Amazon Linux 2023 STIG role to it, then runs the paired audit with syver (content from the `AMAZON2023-STIG-Audit` repo) to show how many controls pass and how many still fail. It is the gating test to run before merging or pushing benchmark changes.

The `localhost/` and `wsl/` scenarios are for running against the local machine and are not used by CI.

## Prerequisites

| Need | Why | Check |
|---|---|---|
| Docker (Docker Desktop, Colima or OrbStack on macOS) | The scenario runs in a container | `docker info` |
| Python 3.11+ with `molecule`, `molecule-plugins[docker]` and `ansible-core` | Runs the scenario | `molecule --version` |
| `community.docker` and `ansible.posix` collections | The docker driver and the role's mount tasks | `ansible-galaxy collection list` |
| Network access | `prepare.yml` installs packages with dnf, and the role clones the audit content and downloads syver | |

## Run it

```bash
cd <path-to>/AMAZON2023-STIG
molecule create
molecule converge
molecule idempotence
molecule verify
molecule destroy
```

`molecule test` runs the same sequence in one command, then destroys the container.

The whole sequence took under 4 minutes on an Apple Silicon laptop once the image was pulled.

## What each step does

| Step | What happens |
|---|---|
| `molecule create` | Pulls and starts `robertdebock/amazonlinux:latest` with systemd as PID 1. |
| `molecule converge` (first) | (a) runs `prepare.yml` to install supporting packages, set the FIPS crypto policy and a root password, (b) installs syver and clones the audit content, (c) runs the pre-remediation audit, (d) applies the full role, (e) runs the post-remediation audit, (f) fetches both audit JSONs out of the container. |
| `molecule idempotence` | Runs converge again and fails if any task reports a change. The audit runs, the audit fetch and the facts file are tagged `molecule-idempotence-notest`, because each writes a fresh result or timestamp on every run. |
| `molecule verify` | Runs `verify.yml`, a short package and SSH configuration check. The broad picture is in the audit JSONs. |

## Expected results

From a run on 2026-10-22 against `benchmark_v1.4.0` (V1R4) and audit content at `benchmark_v1.4.0`, native arm64:

```text
converge:    amazon2023-stig : ok=187  changed=68  unreachable=0  failed=0  skipped=165
idempotence: amazon2023-stig : ok=162  changed=0   unreachable=0  failed=0  skipped=147
audit:       pre  Count: 251, Failed: 71
             post Count: 251, Failed: 26
```

The post-remediation failures are expected in a container. Controls that need a real kernel, bootloader, mounts or a running auditd are skipped by `vars/is_container.yml` or cannot be remediated inside Docker.

## Platform

`robertdebock/amazonlinux:latest` is a multi-arch image (amd64 and arm64). `molecule.yml` sets `platform: "${MOLECULE_DOCKER_PLATFORM:-linux/arm64}"`, so on Apple Silicon it runs the arm64 variant natively. CI exports `MOLECULE_DOCKER_PLATFORM=linux/amd64` for its amd64 runners.

## Testing an unmerged audit branch

`audit_git_version` defaults to `benchmark_{{ benchmark_version }}` (i.e. `benchmark_v1.4.0`) in `defaults/main/audit.yml`. To test against an audit branch that has not been merged yet, override it:

```bash
molecule converge -- --extra-vars 'audit_git_version=<your-audit-branch>'
```

Both repositories use semver benchmark branches, for example `benchmark_v1.4.0`.

## Troubleshooting

| Symptom | Cause | Fix |
|---|---|---|
| `molecule create` fails with a socket error | Docker is not running | Start Docker and re-run |
| Pre-check fails with "does not meet minimum of 0.13.0" | The audit content needs syver 0.13.0 or later, and an older goss binary is on the path | Leave `audit_bin` at its default so the role installs syver |
| Molecule exits with a non-blocking IO error | stdin or stdout is not a terminal in a wrapper script | Redirect: `molecule converge < /dev/null > converge.log 2>&1` |
