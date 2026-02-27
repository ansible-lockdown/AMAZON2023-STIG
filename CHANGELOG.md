# Changelog - Private-AMAZON2023-STIG

## [v1.2.0] - 2026-02-26

### Based on DISA STIG Amazon Linux 2023 V1R2 - 05 January 2026

#### Rule ID Updates (V1R1 -> V1R2)

- AZLX-23-000130: SV-273999r1119985_rule -> SV-273999r1155171_rule
- AZLX-23-001040: SV-274020r1120048_rule -> SV-274020r1155173_rule
- AZLX-23-001240: SV-274049r1120135_rule -> SV-274049r1120747_rule
- AZLX-23-002290: SV-274121r1120351_rule -> SV-274121r1155161_rule
- AZLX-23-002295: SV-274122r1120354_rule -> SV-274122r1155164_rule
- AZLX-23-002300: SV-274123r1120357_rule -> SV-274123r1155167_rule
- AZLX-23-002500: SV-274164r1120480_rule -> SV-274164r1137695_rule
- AZLX-23-002505: SV-274165r1120483_rule -> SV-274165r1137695_rule
- AZLX-23-002510: SV-274166r1120486_rule -> SV-274166r1155170_rule

#### Content Changes

- AZLX-23-002290: Updated find command to target `*.so*` files with `-perm /022` (was incorrectly checking group ownership). Fixed CCI from CCI-0001499 to CCI-001499.
- AZLX-23-002295: Changed from checking library **directories** to library **files** (`*.so*`) per V1R2 update. Updated title, audit command, and register variable. Fixed CCI from CCI-0001499 to CCI-001499.
- AZLX-23-002300: Updated find command to target `*.so*` files specifically.
- AZLX-23-002510: Changed default `StopIdleSessionSec` from `0` (disabled) to `600` (10 minutes) per V1R2 requirement.

#### Bug Fixes

- Fixed test artifact `az2023stig_001295: TESTTEST` -> `true` in defaults/main.yml
- Fixed typo in defaults/main.yml spelling correction to `dependent`
- Fixed doubled word in README.md
- Fixed extra space in AZLX-23-001030 task name
- Added missing default for `az2023stig_pam_retry` (referenced in AZLX-23-002489 task but undefined)
- Added missing default for `az2023stig_priv_command_excluded_mounts` (referenced in auditd.yml but undefined)
- Renamed handler register `chronyd_stopped` -> `discovered_chronyd_stopped` (naming convention)
- Renamed handler register `sssd_config_stat` -> `discovered_sssd_config_stat` (naming convention)

#### Metadata

- Updated `benchmark_version` from `v1.0.0` to `v1.2.0`
- Updated README STIG reference to Version 1, Rel 2 (05 Jan 2026)

## [v1.0.0] - 2025-07-14

### Initial Release - Based on DISA STIG Amazon Linux 2023 V1R1
