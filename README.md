# Ansible Role: httpd

![GitHub](https://img.shields.io/github/license/jomrr/ansible-role-httpd) ![GitHub last commit](https://img.shields.io/github/last-commit/jomrr/ansible-role-httpd) ![GitHub issues](https://img.shields.io/github/issues-raw/jomrr/ansible-role-httpd) [![dev](https://img.shields.io/github/actions/workflow/status/jomrr/ansible-role-httpd/dev-push-smoke.yml?branch=dev&event=push&label=dev)](https://github.com/jomrr/ansible-role-httpd/actions/workflows/dev-push-smoke.yml?query=branch%3Adev) [![main](https://img.shields.io/github/actions/workflow/status/jomrr/ansible-role-httpd/main-full-gate.yml?branch=main&event=push&label=main)](https://github.com/jomrr/ansible-role-httpd/actions/workflows/main-full-gate.yml?query=branch%3Amain)

Ansible role for managing Apache HTTP Server baseline configuration.

## Purpose

This role installs Apache HTTP Server, manages the global Apache configuration surface,
renders a security-only default-deny vhost, optionally renders Apache-native application vhost files,
validates the effective configuration, and enables and starts the Apache service.

## Scope

### Managed

- Apache HTTP Server packages
- Additional platform packages explicitly requested through `httpd_extra_packages`
- Global Apache main configuration
- Explicit module loading
- Global listener configuration
- Global TLS context
- Security-only default-deny virtual host
- Invalid local self-signed certificate for the default-deny HTTPS vhost when enabled
- Apache-native application vhost files declared in `httpd_vhost_files`
- Apache service enablement and runtime state
- Effective Apache configuration validation

### Not Managed

- Firewall policy
- Public or internally trusted certificate issuance
- ACME, CA integration, renewal, or OCSP lifecycle
- PHP-FPM package, pool, or service management
- Backend application deployment
- Backend reverse-proxy services
- Application data or document-root content
- Unmanaged distribution include directories
- Purging unmanaged vhost files

## Requirements

- Target hosts need platform repositories that provide Apache HTTP Server and `python3-cryptography` when HTTPS listeners are configured.

## Dependencies

```yaml
collections:
  - name: community.crypto
    version: '>=2.0.0'
```

## Role Variables

The following variables are part of the public role interface.

| Name | Type | Required | Default | Description |
| ---- | ---- | -------- | ------- | ----------- |
| `httpd_extra_packages` | `list` | `false` | [] | Additional platform packages required by explicitly enabled modules or local policy. |
| `httpd_default_vhost_server_name` | `str` | `false` | `default.invalid` | ServerName used by the default-deny virtual host and its invalid self-signed certificate. |
| `httpd_strict_host_check` | `bool` | `false` | `True` | Whether strict Host and SNI matching is enabled. |
| `httpd_mpm` | `str` | `false` | `event` | Apache Multi-Processing Module policy. |
| `httpd_server_admin` | `str` | `false` | `root@localhost` | Global Apache ServerAdmin directive. |
| `httpd_server_name` | `str` | `false` | `localhost` | Global Apache ServerName directive. |
| `httpd_proxy_requests` | `str` | `false` | `Off` | Global ProxyRequests directive when proxy_module is explicitly enabled. |
| `httpd_server_tokens` | `str` | `false` | `Prod` | Controls how much version information Apache exposes. |
| `httpd_server_signature` | `str` | `false` | `Off` | Controls whether Apache adds a server signature to generated pages. |
| `httpd_trace_enable` | `str` | `false` | `Off` | Controls whether the HTTP TRACE method is enabled. |
| `httpd_file_etag` | `str` | `false` | `None` | Global FileETag directive. |
| `httpd_add_default_charset` | `str` | `false` | `UTF-8` | Global AddDefaultCharset value. |
| `httpd_timeout` | `int` | `false` | `30` | Global request timeout in seconds. |
| `httpd_keep_alive` | `str` | `false` | `On` | Controls HTTP keep-alive connections. |
| `httpd_keep_alive_timeout` | `int` | `false` | `5` | Keep-alive timeout in seconds. |
| `httpd_max_keep_alive_requests` | `int` | `false` | `100` | Maximum requests allowed on a single keep-alive connection. |
| `httpd_enable_sendfile` | `str` | `false` | `Off` | Global EnableSendfile directive. |
| `httpd_enable_mmap` | `str` | `false` | `Off` | Global EnableMMAP directive. |
| `httpd_log_level` | `str` | `false` | `warn` | Global Apache LogLevel directive. |
| `httpd_log_formats` | `list` | `false` | - name: common<br />  format: '%h %l %u %t "%r" %&gt;s %b'<br />- name: combined<br />  format: '%h %l %u %t "%r" %&gt;s %b "%{Referer}i" "%{User-Agent}i"' | Global Apache LogFormat entries. |
| `httpd_custom_logs` | `list` | `false` | [] | Additional global CustomLog entries. |
| `httpd_directory_blocks` | `list` | `false` | - path: /<br />  allow_override: None<br />  options: None<br />  require:<br />    - all denied | Global Apache Directory blocks. |
| `httpd_files_blocks` | `list` | `false` | - pattern: .ht*<br />  require:<br />    - all denied | Global Apache Files blocks. |
| `httpd_mime_enabled` | `bool` | `false` | `True` | Whether to render the global MIME basics block. |
| `httpd_mime_types` | `list` | `false` | - content_type: text/css<br />  extensions:<br />    - .css<br />- content_type: application/javascript<br />  extensions:<br />    - .js | Global Apache AddType definitions. |
| `httpd_listen` | `list` | `false` | - port: 80<br />- port: 443<br />  protocol: https | Global Apache Listen directives. |
| `httpd_tls_session_cache_timeout` | `int` | `false` | `300` | Global SSLSessionCacheTimeout in seconds. |
| `httpd_tls_use_stapling` | `str` | `false` | `Off` | Global SSLUseStapling directive. |
| `httpd_tls_random_seeds` | `list` | `false` | - context: startup<br />  source: file:/dev/urandom<br />  bytes: 512<br />- context: connect<br />  source: builtin | Global SSLRandomSeed directives. The bytes key is optional and must be omitted for source=builtin. |
| `httpd_extra_modules` | `list` | `false` | [] | Additional Apache modules rendered after the role-required module set. |
| `httpd_disabled_modules` | `list` | `false` | [] | Explicit extra module names to suppress from the rendered LoadModule list. |
| `httpd_vhost_files` | `list` | `false` | [] | Complete Apache-native virtual host files written into the managed vhost directory. |

## Managed Files

- `/etc/httpd/conf/httpd.conf`
- `/etc/httpd/managed/modules.d/00-modules.conf`
- `/etc/httpd/managed/conf.d/00-listen.conf`
- `/etc/httpd/managed/conf.d/00-tls.conf`
- `/etc/httpd/managed/vhost.d/000-default-deny.conf`
- `/etc/httpd/managed/vhost.d/*.conf for declared `httpd_vhost_files``
- `/etc/httpd/managed/tls/certs/httpd-default-deny.crt when HTTPS listeners are configured`
- `/etc/httpd/managed/tls/private/httpd-default-deny.key when HTTPS listeners are configured`
- `/etc/httpd/managed/tls/csr/httpd-default-deny.csr when HTTPS listeners are configured`
- `/etc/apache2/apache2.conf or /etc/apache2/httpd.conf`
- `/etc/apache2/managed/modules.d/00-modules.conf`
- `/etc/apache2/managed/conf.d/00-listen.conf`
- `/etc/apache2/managed/conf.d/00-tls.conf`
- `/etc/apache2/managed/vhost.d/000-default-deny.conf`
- `/etc/apache2/managed/vhost.d/*.conf for declared `httpd_vhost_files``
- `/etc/apache2/managed/tls/certs/httpd-default-deny.crt when HTTPS listeners are configured`
- `/etc/apache2/managed/tls/private/httpd-default-deny.key when HTTPS listeners are configured`
- `/etc/apache2/managed/tls/csr/httpd-default-deny.csr when HTTPS listeners are configured`

## Check Mode

Package, directory, template, line, copy, file, certificate, and service tasks support check mode where the underlying module supports it.
Effective `httpd -t -f` validation is skipped in check mode because first-run systems may not have the Apache binary or include files yet.

## Service Behavior

The role always ensures the Apache service is enabled and started.
Managed configuration and default-deny certificate changes notify the `httpd_reload` handler, which reloads the service outside check mode.

### Handlers

- httpd_reload

## Security Notes

- Distribution package include wildcards are intentionally not included.
- The default-deny vhost is rendered as `000-default-deny.conf` so it sorts before normal vhost files.
- HTTPS listeners use a local invalid self-signed certificate for the default-deny vhost and do not consume a public or trusted certificate.
- Unknown or unmatched hostnames are denied by strict host/SNI checks or by the default-deny vhost.
- Proxy, FastCGI proxy, WebSocket proxy, compatibility, headers, and HTTP/2 modules are not part of the default minimal module set.
- Application vhost files are Apache-native content; the role does not provide an application vhost DSL.
- TLS certificates for application vhosts are not issued, renewed, or deployed by this role.
- Logs are written below `/var/log`, not below `/etc`.

## Operational Notes

- Present application vhost files in `httpd_vhost_files` must use names sorting after `000-default-deny.conf`.
- `httpd_listen` defaults to HTTP and HTTPS; mark additional HTTPS listeners with `protocol: https`.
- Listener address and port endpoints in `httpd_listen` must be unique.
- `httpd_custom_logs` entries require exactly one target (`file` or `pipe`), exactly one format (`format_name` or `format_string`), and optionally one condition (`env` or `expr`).
- Add optional modules through `httpd_extra_modules`; required packages belong in `httpd_extra_packages`.
- On SUSE-family systems, the MPM is selected through `/etc/sysconfig/apache2`.

## Supported Platforms

| OS Family | Distribution | Version | Container Image |
| --------- | ------------ | ------- | --------------- |
| RedHat | AlmaLinux | latest | [jomrr/molecule-almalinux:latest](https://hub.docker.com/r/jomrr/molecule-almalinux) |
| Debian | Debian | latest | [jomrr/molecule-debian:latest](https://hub.docker.com/r/jomrr/molecule-debian) |
| RedHat | Fedora | latest | [jomrr/molecule-fedora:latest](https://hub.docker.com/r/jomrr/molecule-fedora) |
| Suse | OpenSuse Leap | latest | [jomrr/molecule-opensuse-leap:latest](https://hub.docker.com/r/jomrr/molecule-opensuse-leap) |
| Suse | OpenSuse Tumbleweed | latest | [jomrr/molecule-opensuse-tumbleweed:latest](https://hub.docker.com/r/jomrr/molecule-opensuse-tumbleweed) |
| Debian | Ubuntu | latest | [jomrr/molecule-ubuntu:latest](https://hub.docker.com/r/jomrr/molecule-ubuntu) |

## Example Playbook

### Simple example playbook

Minimal example for applying this role.

```yaml
---
- name: Configure Apache HTTP Server baseline
  hosts: httpd
  gather_facts: true
  roles:
    - role: jomrr.httpd
```
### Reverse proxy vhost file

Apache-native vhost file for HTTP-to-HTTPS redirect and reverse proxying.

```yaml
---
- name: Configure Apache HTTP Server with a reverse proxy vhost
  hosts: httpd
  gather_facts: true
  roles:
    - role: jomrr.httpd
      vars:
        httpd_extra_modules:
          - name: proxy_module
            file: mod_proxy.so
          - name: proxy_http_module
            file: mod_proxy_http.so
        httpd_listen:
          - port: 80
          - port: 443
            protocol: https
        httpd_vhost_files:
          - name: 100-app.conf
            content: |-
              <VirtualHost *:80>
                  ServerName app.example.org
                  Redirect permanent / https://app.example.org/
              </VirtualHost>

              <VirtualHost *:443>
                  ServerName app.example.org

                  SSLEngine on
                  SSLCertificateFile /etc/pki/tls/certs/app.example.org.crt
                  SSLCertificateKeyFile /etc/pki/tls/private/app.example.org.key

                  ProxyPreserveHost On
                  ProxyPass / http://127.0.0.1:3000/
                  ProxyPassReverse / http://127.0.0.1:3000/
              </VirtualHost>
```
### CustomLog examples

Global Apache CustomLog entries with explicit targets, formats, and conditions.

```yaml
---
- name: Configure Apache HTTP Server with additional access logs
  hosts: httpd
  gather_facts: true
  roles:
    - role: jomrr.httpd
      vars:
        httpd_custom_logs:
          - file: /var/log/httpd/example-access.log
            format_name: combined
          - pipe: /usr/bin/rotatelogs /var/log/httpd/example-access.%Y%m%d 86400
            format_string: '%h %l %u %t "%r" %>s %b'
          - file: /var/log/httpd/example-non-local-referer.log
            format_name: combined
            condition:
              env: localreferer
              negate: true
          - file: /var/log/httpd/example-errors.log
            format_name: combined
            condition:
              expr: "%{REQUEST_STATUS} >= 400"
```

## Author

[Jonas Mauer](https://github.com/jomrr)

## License

This project is licensed under the MIT License.
See [LICENSE](LICENSE) for the full license text.

Copyright (c) 2024-2026 Jonas Mauer.
