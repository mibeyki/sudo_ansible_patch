# Sudo Patching with Ansible

This Ansible playbook audits and patches the `sudo` package to address security vulnerabilities CVE-2025-32462 and CVE-2025-32463 across multiple Linux distributions.

## Purpose

The `sudo.yaml` playbook checks `sudo` versions, applies patched versions (including backports), and generates reports to ensure systems are secure while maintaining stability.

## Supported Systems

- **Ubuntu**: 20.04, 22.04, 24.04, 25.04
- **Debian**: 11, 12
- **RHEL/CentOS/AlmaLinux/Rocky**: 7, 8, 9

## Features

- Audits `sudo` for CVE-2025-32462 and CVE-2025-32463.
- Compares versions using `dpkg` (Debian/Ubuntu), `rpmdev-vercmp` (RedHat-based), or Ansible’s `version` filter.
- Updates `sudo` to the latest version via `apt` or `yum`.
- Generates reports at `/tmp/sudo_cve_report_<hostname>.txt` with vulnerability status and actions taken.
- Flags hosts for manual verification if backported fixes are detected.

## Prerequisites

- Ansible 2.9+
- Inventory file with target hosts
- `sudo` installed on targets
- RedHat-based systems: `rpmdevtools` (`yum install rpmdevtools`)

## Usage

1. Clone the repository:
   ```bash
   git clone <repository-url>
   cd <repository-directory>
   ```

2. Update inventory (e.g., `inventory.ini`):
3. Run the playbook:
   ```bash
   ansible-playbook playbooks/sudo.yaml -i path/to/inventory-file.yaml -v
   ```

4. Check reports:
   - Example for `192.168.233.152` (Debian 12):
     ```
     Sudo CVE Report for 192.168.233.152
     ---------------------------------------
     Initial sudo version: 1.9.13p3-1+deb12u2
     Final sudo version: 1.9.13p3-1+deb12u2
     CVE-2025-32462: Not Vulnerable
     CVE-2025-32463: Not Vulnerable (backported fix)
     Action taken: No update performed
     Manual verification needed: Yes
     ---------------------------------------
     ```

## Troubleshooting

For errors like `'dict object' has no attribute 'rc'` on hosts (e.g., `192.168.233.152`):

1. **Check Debug Output**:
   - Review `Debug OS details` and `Debug comparison task conditions` in verbose output (`-v`):
     ```
     Host: 192.168.233.152
     ansible_os_family: Debian
     ansible_distribution: Debian
     os_release_short: 12
     final_sudo_version: 1.9.13p3-1+deb12u2
     fixed_version_32462: 1.9.13p3-1+deb12u2
     fixed_version_32463: not affected
     cve_2025_32463_final_compare defined: false
     cve_2025_32463_compare_condition: false
     ```

2. **Verify OS**:
   ```bash
   ssh 192.168.233.152 cat /etc/os-release
   ```
   - Ensure `NAME="Debian GNU/Linux"`, `VERSION_ID="12"`.

3. **Verify Sudo**:
   ```bash
   ssh 192.168.233.152 dpkg -l sudo
   ```
   - Confirm `sudo` is installed.

4. **Common Issues**:
   - **Undefined variables**: Ensure `fixed_versions` matches your distribution/version.
   - **Skipped tasks**: Debian 11/12 are `"not affected"` for CVE-2025-32463, which is normal.
   - **OS detection**: If `ansible_os_family != Debian`, check `/etc/os-release`.

## Notes

- Debian 11/12 and RHEL-based systems are often `"not affected"` for CVE-2025-32463 due to backports.
- Manual verification is recommended for hosts with backported fixes.
