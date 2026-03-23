# Changelog - Private-AMAZON2023-STIG

## [v1.2.0] - 2026-02-27

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

- AZLX-23-001085: Update logic with terany, systemd state and enabled status.
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
- Fixed undefined variable `az2023_profile_timeout_seconds` in AZLX-23-002396 (runtime failure) — added default of 600 to defaults/main.yml
- Removed duplicate task block for AZLX-23-002100 from az2023stig-0020xx.yml (already exists in az2023stig-0021xx.yml)
- Removed duplicate task block for AZLX-23-002150 in az2023stig-0021xx.yml
- Removed duplicate task block for AZLX-23-002445 in az2023stig-0024xx.yml
- Fixed trailing quote in AZLX-23-000110 task name
- Fixed missing space in AZLX-23-001235 task name (`001235|` -> `001235 |`)
- Added missing CAT1 tag to AZLX-23-000100, 000115, 000120, 000125, 000130
- Added missing CAT2 tag to AZLX-23-001050
- Moved `vars: warn_control_id` from block-level to task-level on 16 `import_tasks: warning_facts.yml` tasks
- Converted 8 single-item `when:` lists to inline format
- Added blank line after `---` in warning_facts.yml
- Removed unused `Restart journald` handler from handlers/main.yml
- Set `az2023stig_001295` to false (task commented out pending implementation)
- Set orphaned toggles `az2023stig_002310`, `az2023stig_002625`, `az2023stig_002630` to false (no tasks implemented)
- Added secret file patterns to .gitignore (*.vault, *.key, *.pem, *.p12, *.pfx, etc.)
- AZLX-23-000135: Fixed Rule_ID SV-273996r1119976_rule -> SV-274000r1119991_rule and Vul_ID V-273996 -> V-274000
- AZLX-23-002475: Fixed Rule_ID SV-274158r1120462_rule -> SV-274158r1120727_rule
- AZLX-23-002020: Fixed typo in Rule_ID tag `SV-274068r1120192_rul` -> `SV-274068r1120192_rule`
- AZLX-23-001090: Fixed when condition from `az2023stig_001050` to `az2023stig_001090` and tag from `azlx-23-001050` to `azlx-23-001090`
- AZLX-23-002105: Fixed when condition from `az2023stig_002100` to `az2023stig_002105` and tag from `azlx-23-002100` to `azlx-23-002105`
- Fixed variable name mismatch `az2023stig_audit_conf_log_group` -> `az2023stig_auditd_log_group` in defaults/main.yml to match task references
- AZLX-23-002270: Renamed duplicate register `discovered_auditd_log_file_dir_owner` -> `discovered_auditd_log_file_dir_owner_002270`
- AZLX-23-002490: Renamed duplicate register `discovered_pwquality_password_auth_status` -> `discovered_pam_unix_password_auth_status`
- AZLX-23-002520: Renamed duplicate register `discovered_auditd_grub_cmdline_linux` -> `discovered_auditd_grub_backlog_limit`
- Renamed duplicate register `post_audit_summary` -> `post_audit_json_summary` / `post_audit_doc_summary` in post_remediation_audit.yml
- Renamed duplicate register `pre_audit_summary` -> `pre_audit_json_summary` / `pre_audit_doc_summary` in pre_remediation_audit.yml
- Updated `post_audit_summary` references in tasks/main.yml to use new split variable names
- Removed orphaned toggles `az2023stig_002310`, `az2023stig_002625`, `az2023stig_002630` (no tasks implemented)

#### Content Changes (cont.)

- AZLX-23-001295: Implemented as manual/audit warning task for PKI-based authentication certificate mapping

#### Metadata

- Updated `benchmark_version` from `v1.0.0` to `v1.2.0`
- Updated README STIG reference to Version 1, Rel 2 (05 Jan 2026)

## [v1.0.0] - 2025-07-14

### Initial Release - Based on DISA STIG Amazon Linux 2023 V1R1
