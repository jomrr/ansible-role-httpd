# Ansible role httpd

![GitHub](https://img.shields.io/github/license/jomrr/ansible-role-httpd) ![GitHub last commit](https://img.shields.io/github/last-commit/jomrr/ansible-role-httpd) ![GitHub issues](https://img.shields.io/github/issues-raw/jomrr/ansible-role-httpd)

**Ansible role for setting up apache httpd.**

## Description

Install apache httpd from os repositories and configure
  - default server
  - modules
  - reverse proxy
  - TLS
  - virtual hosts
for version 2.4 or greater.

## Prerequisites

This role has no special prerequisites.

### System packages (Fedora)

- `python3` (>= 3.9)

### Python (requirements.txt)

- ansible >= 2.17

## Dependencies (requirements.yml)

This role has no dependencies.

## Supported Platforms

| OS Family | Distribution | Version | Container Image |
|-----------|--------------|---------|-----------------|
| Debian | Debian | latest | [jomrr/molecule-debian:latest]( https://hub.docker.com/r/jomrr/molecule-debian ) |
| RedHat | Fedora | latest | [jomrr/molecule-fedora:latest]( https://hub.docker.com/r/jomrr/molecule-fedora ) |

## Role Variables

No role default variables specified, see [defaults/main.yml](defaults/main.yml).

## Example Playbook

Example playbooks(s) that show how to use this role.

## Simple example playbook

A simple default example playbook for using jomrr.httpd.
```yaml
---
# name: "jomrr.httpd"
# file: "playbook_httpd.yml"

- name: "PLAYBOOK | httpd"
  hosts: "httpd_hosts"
  gather_facts: true
  roles:
    - role: "jomrr.httpd"
```

## Author(s) and License

- :octocat:                 Author::    [jomrr](https://github.com/jomrr)
- :triangular_flag_on_post: Copyright:: 2024, Jonas Mauer
- :page_with_curl:          License::   [MIT](LICENSE)

## References

- [Apache HTTP Server Documentation](https://httpd.apache.org/docs/)
- [ArchWiki - Apache HTTP Server](https://wiki.archlinux.org/title/Apache_HTTP_Server)
- [Fedora Quick Docs - Getting started with Apache HTTP Server](https://docs.fedoraproject.org/en-US/quick-docs/getting-started-with-apache-http-server/)

---
