# AMAZON 2023 DISA STIG

## Configure a AMAZON2023 based system to be complaint with Disa STIG

This role is based on AMAZON2023 DISA STIG: [Version 1, Rel 2 released on 05 Jan 2026](https://dl.dod.cyber.mil/wp-content/uploads/stigs/U_Amazon_Linux_2023_V1R2_STIG.zip).

## Initial Release from STIG, still many items that not quite aligned in the documentation

---

![Org Stars](https://img.shields.io/github/stars/ansible-lockdown?label=Org%20Stars&style=social)
![Stars](https://img.shields.io/github/stars/ansible-lockdown/az2023-stig?label=Repo%20Stars&style=social)
![Forks](https://img.shields.io/github/forks/ansible-lockdown/az2023-stig?style=social)
![followers](https://img.shields.io/github/followers/ansible-lockdown?style=social)
[![Twitter URL](https://img.shields.io/twitter/url/https/twitter.com/AnsibleLockdown.svg?style=social&label=Follow%20%40AnsibleLockdown)](https://twitter.com/AnsibleLockdown)

![Ansible Galaxy Quality](https://img.shields.io/ansible/quality/56380?label=Quality&&logo=ansible)
![Discord Badge](https://img.shields.io/discord/925818806838919229?logo=discord)

![Release Branch](https://img.shields.io/badge/Release%20Branch-Main-brightgreen)
![Release Tag](https://img.shields.io/github/v/release/ansible-lockdown/AMZN2023-STIG)
![Release Date](https://img.shields.io/github/release-date/ansible-lockdown/AMZN2023-STIG)



[![Main Pipeline Status](https://github.com/ansible-lockdown/AMZN2023-STIG/actions/workflows/main_pipeline_validation.yml/badge.svg?)](https://github.com/ansible-lockdown/AMZN2023-STIG/actions/workflows/main_pipeline_validation.yml)

[![Devel Pipeline Status](https://github.com/ansible-lockdown/AMZN2023-STIG/actions/workflows/devel_pipeline_validation.yml/badge.svg?)](https://github.com/ansible-lockdown/AMZN2023-STIG/actions/workflows/devel_pipeline_validation.yml)
![Devel Commits](https://img.shields.io/github/commit-activity/m/ansible-lockdown/AMZN2023-STIG/devel?color=dark%20green&label=Devel%20Branch%20Commits)

![Issues Open](https://img.shields.io/github/issues-raw/ansible-lockdown/AMZN2023-STIG?label=Open%20Issues)
![Issues Closed](https://img.shields.io/github/issues-closed-raw/ansible-lockdown/AMZN2023-STIG?label=Closed%20Issues&&color=success)
![Pull Requests](https://img.shields.io/github/issues-pr/ansible-lockdown/AMZN2023-STIG?label=Pull%20Requests)
[![pre-commit](https://img.shields.io/badge/pre--commit-enabled-brightgreen?logo=pre-commit)](https://github.com/pre-commit/pre-commit)

![License](https://img.shields.io/github/license/ansible-lockdown/AMZN2023-STIG?label=License)

---

## Looking for support?

[Lockdown Enterprise](https://www.lockdownenterprise.com#GH_AL_RH9_stig)

[Ansible support](https://www.mindpointgroup.com/cybersecurity-products/ansible-counselor#GH_AL_RH9_stig)

### Community

On our [Discord Server](https://www.lockdownenterprise.com/discord) to ask questions, discuss features, or just chat with other Ansible-Lockdown users

---

Configure a Amazon Linux 2023 systems to be DISA STIG compliant.
Non-disruptive CAT I, CAT II, and CAT III findings will be corrected by default.
Disruptive finding remediation can be enabled by setting `az2023stig_disruption_high` to `true`.

## Updating

Coming from a previous release.

As with all releases and updates, It is suggested to test and align controls.
This contains rewrites and ID reference changes as per STIG documentation. With some controls changing ID number.

## Auditing

This can be turned on or off within the defaults/main.yml file with the variable az2023stig_run_audit. The value is false by default, please refer to the wiki for more details. The defaults file also populates the goss checks to check only the controls that have been enabled in the ansible role.

This is a much quicker, very lightweight, checking (where possible) config compliance and live/running settings.

A new form of auditing has been developed, by using a small (12MB) go binary called [goss](https://github.com/goss-org/goss) along with the relevant configurations to check. Without the need for infrastructure or other tooling.
This audit will not only check the config has the correct setting but aims to capture if it is running with that configuration also trying to remove [false positives](https://www.mindpointgroup.com/blog/is-compliance-scanning-still-relevant/) in the process.

## Documentation

- [Read The Docs](https://ansible-lockdown.readthedocs.io/en/latest/)

## Requirements

- Amazon Linux 2023
- Other OSs can be checked by changing the skip_os_check to true for testing purposes.
- Access to download or add the goss binary and content to the system if using auditing. options are available on how to get the content to the system.

## Dependencies

The following packages must be installed on the controlling host/host where ansible is executed:

- python-lxml
- python-xmltodict

Package 'python-xmltodict' is required if you enable the OpenSCAP tool installation and run a report. These are all required on the controller host that executes Ansible.

## Role Variables

This role is designed that the end user should not have to edit the tasks themselves. All customizing should be done via the defaults/main.yml file or with extra vars within the project, job, workflow, etc.

### Tags

There are many tags available for added control precision. Each control has it's own set of tags noting the control number as well as what parts of the system that control addresses.

Below is an example of the tag section from a control within this role. Using this example if you set your run to skip all controls with the tag ssh, this task will be skipped. The
opposite can also happen where you run only controls tagged with ssh.

```sh
tags:
    - azlx-23-002395
    - CAT3
    - SV-274141r1120411_rule
    - V-274141
    - SRG-OS-000027-GPOS-00008
    - CCI-000054
    - NIST800-53R4_AC-10
    - login
```

### Example Audit Summary

This is based on a vagrant image with selections enabled. e.g. No Gui or firewall.
Note: More tests are run during audit as we check config and running state.

```sh
ok: [testhost] => {
    "msg": [
        "The pre remediation audit results are: Count: 460, Failed: 176, Skipped: 15, Duration: 1.075s",
        "The post remediation audit results are: Count: 460, Failed: 41, Skipped: 3, Duration: 1.868s",
        "Full breakdown can be found in /opt",
        ""
    ]
}

PLAY RECAP ***********************************************************************************************************************************************************************
testhost                   : ok=293  changed=107  unreachable=0    failed=0    skipped=71   rescued=0    ignored=0
```

## Branches

- **devel** - This is the default branch and the working development branch. Community pull requests will pull into this branch
- **main** - This is the release branch
- **reports** - This is a protected branch for our scoring reports, no code should ever go here
- **gh_pages** - github pages
- **all other branches** - Individual community member branches

## Containers - testing

- system_is_container

This is set to false by defaults/main.yml
If discovered it is a container type or ansible_connection == docker it will convert to run with true.
Some controls will skip is this is true as they are not applicable at all. Others runs a subset of controls found in vars/is_container.yml based on a vendor supplied un altered image.

**NON altered vendor image.**

- container_vars_file: is_container.yml

This vars file runs controls are grouped into tags so if the container does later have ssh it could be re-enabled by loading an alternative vars file.

## Community Contribution

We encourage you (the community) to contribute to this role. Please read the rules below.

- Your work is done in your own individual branch. Make sure to Signed-off and GPG sign all commits you intend to merge.
- All community Pull Requests are pulled into the devel branch
- Pull Requests into devel will confirm your commits have a GPG signature, Signed-off, and a functional test before being approved
- Once your changes are merged and a more detailed review is complete, an authorized member will merge your changes into the main branch for a new release.

## Pipeline Testing

uses:

- ansible-core 2.12
- ansible collections - pulls in the latest version based on requirements file
- runs the audit using the devel branch
- This is an automated test that occurs on pull requests into devel

## Known Issues

STIG Control

- Understand the chrony handler (This may not work for your environment, but stops future patches from overridding settings)

## Support

This is a community project at its core and will be managed as such.

If you would are interested in dedicated support to assist or provide bespoke setups

- [Ansible Counselor](https://www.mindpointgroup.com/products/ansible-counselor-on-demand-ansible-services-and-consulting/)
- [Try us out](https://engage.mindpointgroup.com/try-ansible-counselor)

## Credits

This repo originated from work done by [Sam Doran](https://github.com/samdoran/ansible-role-stig)

## Added Extras

- makefile - this is there purely for testing and initial setup purposes.
- [pre-commit](https://pre-commit.com) can be tested and can be run from within the directory

```sh
pre-commit run
```
