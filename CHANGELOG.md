# Changelog - AMAZON2023-STIG

## 2026 August - Contributing guide and README refresh

- replaced `CONTRIBUTING.rst` with `CONTRIBUTING.md`, carrying the current Ansible-Lockdown
  contributing guide. It adds the approved-contributor model for pull requests, keeps issues open to
  everyone, and retains the DCO 1.1 text and the GPG plus Signed-off-by requirements
- `README.md`: added a Contributing section pointing at `CONTRIBUTING.md`, and refreshed the
  Community Contribution section, which still described the previous open pull request workflow and
  so contradicted the new guide
- `README.md`: removed the decorative emoji from headings, and switched the social badge from
  `twitter.com` to `x.com`

## [v1.4.0]

### Based on DISA STIG Amazon Linux 2023 V1R4 (01 July 2026) - Branch benchmark_v1.4.0

Benchmark V1R3 -> V1R4 (187 -> 187; +1 / -1; PDF-verified). Version strings: defaults/main.yml v1.4.0, README banner + DISA zip URL V1R4/01 Jul 2026.

#### Rule delta
- AZLX-23-001041 (SV-284944, CAT II) ADDED: rsyslog service running + persistent (split out of 001040) - new task + az2023stig_001041 toggle (defaults, is_container N/A, bridge template).
- AZLX-23-001280 (FIPS-mode duplicate, CAT I) REMOVED per V1R4; the FIPS requirement remains covered by AZLX-23-000050 (which independently sets fips_required consumed by post.yml). Removed the task, toggle, is_container entry and bridge-template entry.
- AZLX-23-001040: dropped the service enabled/started sub-task (now 001041); keeps the rsyslog package install. SV-rev -> r1208263.
- AZLX-23-002075: gtls -> OpenSSL TLS (ossl); the role now sets $DefaultNetstreamDriver ossl (plus $ActionSendStreamDriverMode 1) in the rsyslog offload conf; title updated. SV-rev -> r1210985.
- AZLX-23-002240: audit.rules/auditd.conf mode 0640 -> 0600 (g-wx -> g-rwx). SV-rev -> r1210984.
- AZLX-23-002350: added /usr/libexec to the system-command group-owner discovery. SV-rev -> r1208253.
- AZLX-23-002396: title 15 -> 10 minutes (content already TMOUT 600s). SV-rev -> r1208254.
- AZLX-23-002595: pcscd service -> pcscd.socket (systemd_service, name pcscd.socket); added CCI-004046. SV-rev -> r1208251.

#### New Alignment Strategy (full reference parity)
- Renamed goss bridge template templates/ansible_vars_goss.yml.j2 -> templates/lockdown_audit.yml.j2; updated the src reference in tasks/pre_remediation_audit.yml.
- Consolidated audit config vars into vars/audit.yml (get_audit_binary_method, audit_bin_validate_certs, audit_bin_copy_location, audit_content, audit_conf_source, audit_conf_dest, audit_log_dir); added audit_max_concurrent.
- Goss binary source goss-org -> github.com/krameff/goss; audit_bin_version v0.4.8 -> v0.5.0 with krameff checksums; README goss link -> krameff.
- Converted dotted ansible_facts.* to ansible_facts['*'] bracket notation (15 sites) and the bare ansible_hostname / ansible_distribution to ansible_facts[...].
- Converted ansible.builtin.systemd -> ansible.builtin.systemd_service (15 sites).
- Added set -o pipefail + args: executable "{{ az2023stig_shell_executable }}" to 31 shell tasks; added az2023stig_shell_executable to vars/main.yml.
- meta/main.yml author "Ansible Lockdown Team" -> "Ansible-Lockdown Team".

#### Bug Fixes
- AZLX-23-002520 (audit_backlog_limit of sufficient size): the task only edited `GRUB_CMDLINE_LINUX` when the line was absent, which it never is, so the limit was never set. It now checks every kernel with `grubby --info=ALL` and runs `grubby --update-kernel=ALL --args=audit_backlog_limit=...` when one lacks it. Thank you @alevatic1 for the pull request (ansible-lockdown/AMAZON2023-STIG#2).
- AZLX-23-002225 (audit logs group-owned by root or a restricted group): `grep log_file` also matched `max_log_file` and `max_log_file_action`, so the task created a directory named `audit.log\n8\nROTATE` under /var/log/audit and never set the group on the log. It now reads the anchored `log_file` and `log_group` settings and sets the group on the audit log file. AZLX-23-002230 and AZLX-23-002270 use the same anchored match.
- AZLX-23-002240 (only the ISSM selects audited events): the find pattern `*.(conf|rules)` is a glob and matched nothing, and `failed_when` read the AZLX-23-002235 register. It now sets 0600 on `/etc/audit/rules.d/*.rules` and `/etc/audit/audit.rules`, leaving `audit-stop.rules` alone. The stig.rules template mode moves to 0600 so the two tasks agree.
- AZLX-23-002490 and AZLX-23-002495 (sufficient hashing rounds): `az2023stig_pam_rounds` 10000 -> 100000, the floor the check text sets. A line that already carries a `rounds=` value is now updated to the variable; before, only a line with no value was changed.
- Reboot step in tasks/post.yml now runs only when a handler set `reboot_required`, defaulted to false in vars/main.yml. Before, it rebooted on every run when `skip_reboot` was false, and reported a change on every run when it was true.
- `Restart auditd` handler skipped in containers.
- The facts file condition in tasks/main.yml tested `post_audit_json_summary` and `post_audit_doc_summary`, which nothing sets; it now tests `post_audit_results`.
- AZLX-23-001286 (bind package encryption): uses package facts instead of `rpm -q bind`, clearing the ansible-lint `command-instead-of-module` failure.
- Task names on 16 controls now carry the V1R4 rule title, including AZLX-23-002110 (named for the `/etc/opasswd` rule) and AZLX-23-002125 (named for the chown rule). Tag CCI typos `CCI-0001314` and `CCI-0002617` corrected.

#### Other Changes
- Audit binary: syver v0.13.0 (github.com/krameff/syver) replaces goss v0.5.0, with the published amd64 and arm64 checksums. The paired audit content requires syver 0.13.0 or later.
- defaults/main.yml split into defaults/main/main.yml and defaults/main/audit.yml; vars/audit.yml merged into defaults/main/audit.yml and its include removed from prelim.yml. Variable names and values are unchanged apart from the audit binary. Audit task files run with `umask 0022` and pass `-m {{ audit_max_concurrent }}`.
- Workflows: added molecule.yml (create, converge, idempotence, verify) and repo_qa.yml (Repo QA Checker 2.8.4 against .qa_baseline.json), both on GitHub-hosted runners, plus dependabot.yml for GitHub Actions. The devel and main pipelines skip fork pull requests and move from actions/checkout v6.0.2 to v7; the devel pipeline's welcome job now carries its own issues and pull-requests write permissions.
- Molecule: `platform:` key (`MOLECULE_DOCKER_PLATFORM`, default linux/arm64), collections/requirements.yml as the dependency file, audit output fetched to `_temp_fetched_audits/`, and the audit runs, audit fetch and facts file tagged `molecule-idempotence-notest`. Added molecule/README_Molecule_QuickStart.md.
- pre-commit: gitleaks v8.30.0 -> v8.30.1; .yamllint enables comments-indentation; .gitignore refreshed.
- README: badges point at AMAZON2023-STIG (the AMZN2023-STIG and az2023-stig URLs returned 404) and follow the current layout with public, lint, release and subscriber sections; audit section describes syver; "complaint" -> "compliant".
- LICENSE, meta/main.yml and `company_title`: MindPoint Group - A Quantum Sky Company. Added galaxy tags.

## [v1.3.0]

### Based on DISA STIG Amazon Linux 2023 V1R3 (01 April 2026) - Branch benchmark_v1.3.0

#### New Rules (4 added)

- AZLX-23-000050: Enable FIPS mode (CAT I)
- AZLX-23-001206: Configure SSH Client ciphers for defense-in-depth (CAT I)
- AZLX-23-001211: Configure SSH Client MACs for defense-in-depth (CAT I)
- AZLX-23-001286: Implement DOD-approved encryption in bind package (CAT I)

#### Removed Rules (9 duplicate/redundant controls)

- AZLX-23-001085: Removed duplicate control
- AZLX-23-001200: Removed redundant requirement
- AZLX-23-001260: Removed redundant requirement
- AZLX-23-001265: Removed redundant requirement
- AZLX-23-001275: Removed duplicate control
- AZLX-23-002215: Removed duplicate control
- AZLX-23-002275: Removed duplicate control
- AZLX-23-002560: Removed duplicate control
- AZLX-23-002570: Removed duplicate control

#### Severity Upgrades (CAT II -> CAT I)

- AZLX-23-001195: Upgraded to CAT I (crypto-policies package)
- AZLX-23-001205: Upgraded to CAT I (SSH server ciphers)
- AZLX-23-001210: Upgraded to CAT I (SSH server MACs)
- AZLX-23-001285: Upgraded to CAT I (crypto policy override)
- AZLX-23-001270: Upgraded to CAT I (FIPS cryptographic policy)

#### Updated Rules

- AZLX-23-000320: Updated Check for NFS systems
- AZLX-23-001040: Rule number updated (SV-274020r1193303_rule)
- AZLX-23-001060: Added instruction verbiage to Fix
- AZLX-23-001065: Updated requirement to use systemd timers
- AZLX-23-001080: Corrected typo in Title (SV-274028r1190806_rule)
- AZLX-23-001105: Added NA note to libreswan
- AZLX-23-001270: Updated Severity and Parent SRGID
- AZLX-23-002000: Updated Standard Banner
- AZLX-23-002035: Updated Fix typo (added "%")
- AZLX-23-002420: Updated Check for /etc/pam.d/system-auth
- AZLX-23-002465: Removed NA note
- AZLX-23-002475: Added CCI and Fix example
- AZLX-23-002565: Added CCI, updated Check and Fix
- AZLX-23-002590: Corrected nosuid finding statement

#### Bug Fixes

- Fixed AZLX-23-001060 installing chrony instead of aide
- Fixed AZLX-23-001305 missing leading / in /etc/sssd/sssd.conf path
- Fixed register order in AZLX-23-001305 (register after failed_when)
- Updated audit template (ansible_vars_goss.yml.j2) to match V1R3 rules
- Fixed meta/main.yml indentation (4-space -> 2-space)
- Removed duplicate 'stig' galaxy tag from meta/main.yml
- Updated min_ansible_version to 2.16.1 in meta/main.yml and vars/main.yml
- Fixed vars/main.yml comment typo (missing space)
- Fixed LICENSE casing: MindPoint (capital P)
- Fixed container detection: added community.docker.docker connection type
- Added molecule test scenarios (default, localhost, wsl)

#### QA Improvements

- Created vars/is_container.yml with 75 container-incompatible rules disabled
- Updated 29 SV-*/V-* task tags to match V1R3 XCCDF revision numbers
- Renamed az2023_firewalld_service to az2023stig_firewalld_service (prefix consistency)
- Renamed az2023_profile_timeout_seconds to az2023stig_profile_timeout_seconds
- Added sshd -t config validation to SSH restart handler
- Added loop_control/label to all loop tasks for better debugging output
- Converted absolute symbolic file modes to relative notation
- Fixed comment typos in defaults/main.yml (002055, 002425 references)
- Fixed post.yml FIPS task to skip in containers
- Fixed molecule prepare.yml: sudo install, sysctl.d directory, python3-libselinux
- Updated README benchmark version V1R2 to V1R3, Ansible requirement to 2.16.1
- Fixed AZLX-23-002510 logind StopIdleSessionSec not applied (backrefs/regexp bug)
- Fixed AZLX-23-002396 TMOUT not created on fresh systems (missing create-if-absent)
- Fixed AZLX-23-002490 PAM password-auth rounds not added (inverted condition)
- Fixed AZLX-23-002495 PAM system-auth rounds not added (inverted condition)
- Fixed AZLX-23-002600 autofs not masked (added systemd mask in addition to stop/disable)
- Molecule prepare: added FIPS crypto policy, firewalld/chronyd start, autofs, crypto-policies-scripts

---

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

- AZLX-23-001085: Update logic with ternary, systemd state and enabled status
- AZLX-23-002290: Updated find command to target `*.so*` files with `-perm /022`; fixed CCI from CCI-0001499 to CCI-001499
- AZLX-23-002295: Changed from checking library directories to library files (`*.so*`) per V1R2 update; fixed CCI
- AZLX-23-002300: Updated find command to target `*.so*` files specifically
- AZLX-23-002510: Changed default `StopIdleSessionSec` from `0` (disabled) to `600` (10 minutes) per V1R2 requirement
- AZLX-23-001295: Implemented as manual/audit warning task for PKI-based authentication certificate mapping

#### Bug Fixes

- Fixed test artifact `az2023stig_001295: TESTTEST` -> `true` in defaults/main.yml
- Fixed typo: `dependant` -> `dependent` in defaults/main.yml
- Added missing default for `az2023stig_pam_retry` (002489 task referenced undefined variable)
- Added missing default for `az2023stig_priv_command_excluded_mounts` (auditd.yml referenced undefined variable)
- Added missing default for `az2023_profile_timeout_seconds` (002396 runtime failure)
- Renamed handler registers to `discovered_` prefix convention (chronyd_stopped, sssd_config_stat)
- Removed duplicate task blocks: AZLX-23-002100, 002150, 002445
- Fixed trailing quote in AZLX-23-000110 task name
- Fixed missing space in AZLX-23-001235 task name
- Added missing CAT1 tag to AZLX-23-000100, 000115, 000120, 000125, 000130
- Added missing CAT2 tag to AZLX-23-001050
- Moved `vars: warn_control_id` from block-level to task-level on 16 warning_facts.yml imports
- Converted 8 single-item `when:` lists to inline format
- Fixed Rule_ID mismatches: AZLX-23-000135, 002475, 002020 (typo)
- Fixed when/tag mismatches: AZLX-23-001090, 002105
- Fixed variable name mismatch `az2023stig_audit_conf_log_group` -> `az2023stig_auditd_log_group`
- Renamed 5 duplicate register variables to unique names (002270, 002490, 002520, post/pre audit)
- Removed orphaned toggles: az2023stig_002310, 002625, 002630 (no tasks implemented)
- Removed unused `Restart journald` handler
- Added secret file patterns to .gitignore
- Removed `dhcp` from default firewalld service list

#### Metadata

- Updated `benchmark_version` from `v1.1.0` to `v1.2.0`
- Updated README STIG reference to Version 1, Rel 2 (05 Jan 2026)

---

## [v1.0.0] - 2025-07-14

### Initial Release - Based on DISA STIG Amazon Linux 2023 V1R1
