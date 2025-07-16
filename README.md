# Sudo Patching with Ansible

This repository contains Ansible playbooks to patch the `sudo` package on managed Linux systems in a controlled and repeatable way.

## Purpose

The goal is to address recent security vulnerabilities in `sudo`, specifically:

- **CVE-2025-32462**
- **CVE-2025-32463**

These playbooks apply patched versions of `sudo` that include Red Hat–provided security fixes (via backports), without requiring a full version upgrade. This ensures systems remain secure while maintaining package stability.
