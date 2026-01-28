# cvm_deploy

[![ansible-lint.yml](https://github.com/linux-system-roles/cvm_deploy/actions/workflows/ansible-lint.yml/badge.svg)](https://github.com/linux-system-roles/cvm_deploy/actions/workflows/ansible-lint.yml) [![ansible-test.yml](https://github.com/linux-system-roles/cvm_deploy/actions/workflows/ansible-test.yml/badge.svg)](https://github.com/linux-system-roles/cvm_deploy/actions/workflows/ansible-test.yml) [![codespell.yml](https://github.com/linux-system-roles/cvm_deploy/actions/workflows/codespell.yml/badge.svg)](https://github.com/linux-system-roles/cvm_deploy/actions/workflows/codespell.yml) [![markdownlint.yml](https://github.com/linux-system-roles/cvm_deploy/actions/workflows/markdownlint.yml/badge.svg)](https://github.com/linux-system-roles/cvm_deploy/actions/workflows/markdownlint.yml) [![qemu-kvm-integration-tests.yml](https://github.com/linux-system-roles/cvm_deploy/actions/workflows/qemu-kvm-integration-tests.yml/badge.svg)](https://github.com/linux-system-roles/cvm_deploy/actions/workflows/qemu-kvm-integration-tests.yml) [![shellcheck.yml](https://github.com/linux-system-roles/cvm_deploy/actions/workflows/shellcheck.yml/badge.svg)](https://github.com/linux-system-roles/cvm_deploy/actions/workflows/shellcheck.yml) [![tft.yml](https://github.com/linux-system-roles/cvm_deploy/actions/workflows/tft.yml/badge.svg)](https://github.com/linux-system-roles/cvm_deploy/actions/workflows/tft.yml) [![tft_citest_bad.yml](https://github.com/linux-system-roles/cvm_deploy/actions/workflows/tft_citest_bad.yml/badge.svg)](https://github.com/linux-system-roles/cvm_deploy/actions/workflows/tft_citest_bad.yml) [![woke.yml](https://github.com/linux-system-roles/cvm_deploy/actions/workflows/woke.yml/badge.svg)](https://github.com/linux-system-roles/cvm_deploy/actions/workflows/woke.yml)

![cvm_deploy](https://github.com/linux-system-roles/cvm_deploy/workflows/tox/badge.svg)

Ansible role for deploying Trustee Guest Components using Podman Quadlets for
confidential virtual machine deployments. The role downloads quadlet files and
configuration files from a GitHub repository, installs them, and manages them as
systemd services.

## Requirements

### Managed Node Requirements

- Podman v4.6 or later (installed automatically by the role)
- Git (for downloading Trustee Guest Components quadlet files from GitHub)
- Systemd (for managing Trustee Guest Components services)

### Collection requirements

For instance, if the role depends on some collections and has a
`meta/collection-requirements.yml` file for installing those dependencies, and
in order to manage `rpm-ostree` systems, it should be mentioned here that the
 user should run

```bash
ansible-galaxy collection install -vv -r meta/collection-requirements.yml
```

on the *control node* before using the role.

## Role Variables

A description of all input variables (i.e. variables that are defined in
`defaults/main.yml`) for the role should go here as these form an API of the
role.  Each variable should have its own section e.g.

### cvm_deploy_quadlet_repo_url

The GitHub repository URL containing the Trustee Guest Components quadlet files.
Default value is `https://github.com/litian1992/trustee-gc-quadlet-rhel`.

### cvm_deploy_quadlet_repo_path

The path within the repository where Trustee Guest Components quadlet files are
located. Default value is `quadlet`.

### cvm_deploy_quadlet_repo_branch

The Git branch or tag to use when downloading Trustee Guest Components quadlet
files. Default value is `main`.

### cvm_deploy_quadlet_install_dir

The directory where Trustee Guest Components quadlet files will be installed.
For root users, this should be `/usr/share/containers/systemd/` or
`/etc/containers/systemd/`. Default value is `/etc/containers/systemd`.

### cvm_deploy_quadlet_reload_systemd

Whether to reload systemd daemon after deploying Trustee Guest Components.
Default value is `true`.

### cvm_deploy_quadlet_enable_services

Whether to enable Trustee Guest Components services to start on boot. Default
value is `true`.

### cvm_deploy_quadlet_start_services

Whether to start Trustee Guest Components services immediately after deployment.
Default value is `true`.

### trustee_kbs_url

The KBS (Key Broker Service) URL to use in the Trustee Guest Components
configuration. This value will replace the `KBS_URL` placeholder in
`/etc/trustee-gc/cdh/config.toml`. Default value is empty string (no
replacement will occur if not set).

### trustee_kbs_cert

The KBS certificate to use in the Trustee Guest Components configuration. This
value will replace the `KBS_CERT` placeholder in `/etc/trustee-gc/cdh/config.toml`.
Default value is empty string (no replacement will occur if not set).

Variables that are not intended as input, like variables defined in
`vars/main.yml`, variables that are read from other roles and/or the global
scope (ie. hostvars, group vars, etc.) can be also mentioned here but keep in
mind that as these are probably not part of the role API they may change during
the lifetime.

Example of setting the variables:

```yaml
cvm_deploy_quadlet_repo_url: "https://github.com/litian1992/trustee-gc-quadlet-rhel"
cvm_deploy_quadlet_repo_path: "quadlet"
cvm_deploy_quadlet_repo_branch: "main"
cvm_deploy_quadlet_install_dir: "/etc/containers/systemd"
cvm_deploy_quadlet_reload_systemd: true
cvm_deploy_quadlet_enable_services: true
cvm_deploy_quadlet_start_services: true
trustee_kbs_url: "https://kbs.example.com"
trustee_kbs_cert: "/path/to/cert.pem"
```

## Variables Exported by the Role

This section is optional.  Some roles may export variables for playbooks to
use later.  These are analogous to "return values" in Ansible modules.  For
example, if a role performs some action that will require a system reboot, but
the user wants to defer the reboot, the role might set a variable like
`template_reboot_needed: true` that the playbook can use to reboot at a more
convenient time.

Example:

### cvm_deploy_reboot_needed

Default `false` - if `true`, this means a reboot is needed to apply the changes
made by the role

## Example Playbook

Including an example of how to use your role (for instance, with variables
passed in as parameters) is always nice for users too:

```yaml
- name: Deploy Trustee Guest Components using Podman Quadlets
  hosts: all
  vars:
    cvm_deploy_quadlet_repo_url: "https://github.com/litian1992/trustee-gc-quadlet-rhel"
    cvm_deploy_quadlet_repo_path: "quadlet"
    cvm_deploy_quadlet_repo_branch: "main"
    trustee_kbs_url: "https://kbs.example.com"
    trustee_kbs_cert: "/path/to/kbs-cert.pem"
  roles:
    - linux-system-roles.cvm_deploy
```

The role will:
1. Install Podman and Git if not already present
2. Download Trustee Guest Components quadlet files and config files from the
   specified GitHub repository
3. Copy quadlet files (`.container`, `.volume`, `.network`, `.kube`) to the
   install directory (`/etc/containers/systemd` by default)
4. Copy config files from the repository's `configs` directory to `/etc/trustee-gc/`
5. Replace `KBS_URL` and `KBS_CERT` placeholders in `/etc/trustee-gc/cdh/config.toml`
   with the values from `trustee_kbs_url` and `trustee_kbs_cert` variables (if
   provided)
6. Reload systemd daemon
7. Enable and start the Trustee Guest Components services

More examples can be provided in the [`examples/`](examples) directory. These
can be useful, especially for documentation.

## rpm-ostree

See README-ostree.md

## License

Whenever possible, please prefer MIT.

## Author Information

An optional section for the role authors to include contact information, or a
website (HTML is not allowed).
