# Ansible Role: httpd

![GitHub](https://img.shields.io/github/license/jomrr/ansible-role-httpd)
![GitHub last commit](https://img.shields.io/github/last-commit/jomrr/ansible-role-httpd)
![GitHub issues](https://img.shields.io/github/issues-raw/jomrr/ansible-role-httpd)
[![dev](https://img.shields.io/github/actions/workflow/status/jomrr/ansible-role-httpd/dev.yml?branch=dev&label=dev)](https://github.com/jomrr/ansible-role-httpd/actions/workflows/dev.yml?query=branch%3Adev)
[![main](https://img.shields.io/github/actions/workflow/status/jomrr/ansible-role-httpd/main.yml?branch=main&label=main)](https://github.com/jomrr/ansible-role-httpd/actions/workflows/main.yml?query=branch%3Amain)

Ansible role for managing Apache HTTP Server baseline configuration.

## Purpose

This role installs Apache HTTP Server, manages the global Apache configuration
surface,
renders a security-only default-deny vhost, optionally renders Apache-native
application vhost files,
validates the effective configuration, and enables and starts the Apache
service.

## Scope

### Managed

- Apache HTTP Server packages
- Additional platform packages explicitly requested through
  `httpd_extra_packages`
- Global Apache main configuration
- Explicit module loading
- Global listener configuration
- Global TLS context
- Security-only default-deny virtual host
- Invalid local self-signed certificate for the default-deny HTTPS vhost when
  enabled
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

- Target hosts need platform repositories that provide Apache HTTP Server and
  `python3-cryptography` when HTTPS listeners are configured.

## Dependencies

```yaml
collections:
  - name: community.general
    version: '>=12.0.0'
  - name: community.crypto
    version: '>=2.0.0'
```

## Role Variables

### `httpd_extra_packages`

Type: `list`. Required: `false`.

Additional platform packages required by explicitly enabled modules or local
policy.

Default:

```yaml
httpd_extra_packages: []
```

### `httpd_default_vhost_server_name`

Type: `str`. Required: `false`.

ServerName used by the default-deny virtual host and its invalid self-signed
certificate.

Default:

```yaml
httpd_default_vhost_server_name: default.invalid
```

### `httpd_strict_host_check`

Type: `bool`. Required: `false`.

Whether strict Host and SNI matching is enabled.

Default:

```yaml
httpd_strict_host_check: true
```

### `httpd_mpm`

Type: `str`. Required: `false`.

Apache Multi-Processing Module policy.

Default:

```yaml
httpd_mpm: event
```

### `httpd_server_admin`

Type: `str`. Required: `false`.

Global Apache ServerAdmin directive.

Default:

```yaml
httpd_server_admin: root@localhost
```

### `httpd_server_name`

Type: `str`. Required: `false`.

Global Apache ServerName directive.

Default:

```yaml
httpd_server_name: localhost
```

### `httpd_proxy_requests`

Type: `str`. Required: `false`.

Global ProxyRequests directive when proxy_module is explicitly enabled.

Default:

```yaml
httpd_proxy_requests: 'Off'
```

### `httpd_server_tokens`

Type: `str`. Required: `false`.

Controls how much version information Apache exposes.

Default:

```yaml
httpd_server_tokens: Prod
```

### `httpd_server_signature`

Type: `str`. Required: `false`.

Controls whether Apache adds a server signature to generated pages.

Default:

```yaml
httpd_server_signature: 'Off'
```

### `httpd_trace_enable`

Type: `str`. Required: `false`.

Controls whether the HTTP TRACE method is enabled.

Default:

```yaml
httpd_trace_enable: 'Off'
```

### `httpd_file_etag`

Type: `str`. Required: `false`.

Global FileETag directive.

Default:

```yaml
httpd_file_etag: None
```

### `httpd_add_default_charset`

Type: `str`. Required: `false`.

Global AddDefaultCharset value.

Default:

```yaml
httpd_add_default_charset: UTF-8
```

### `httpd_timeout`

Type: `int`. Required: `false`.

Global request timeout in seconds.

Default:

```yaml
httpd_timeout: 30
```

### `httpd_keep_alive`

Type: `str`. Required: `false`.

Controls HTTP keep-alive connections.

Default:

```yaml
httpd_keep_alive: 'On'
```

### `httpd_keep_alive_timeout`

Type: `int`. Required: `false`.

Keep-alive timeout in seconds.

Default:

```yaml
httpd_keep_alive_timeout: 5
```

### `httpd_max_keep_alive_requests`

Type: `int`. Required: `false`.

Maximum requests allowed on a single keep-alive connection.

Default:

```yaml
httpd_max_keep_alive_requests: 100
```

### `httpd_enable_sendfile`

Type: `str`. Required: `false`.

Global EnableSendfile directive.

Default:

```yaml
httpd_enable_sendfile: 'Off'
```

### `httpd_enable_mmap`

Type: `str`. Required: `false`.

Global EnableMMAP directive.

Default:

```yaml
httpd_enable_mmap: 'Off'
```

### `httpd_log_level`

Type: `str`. Required: `false`.

Global Apache LogLevel directive.

Default:

```yaml
httpd_log_level: warn
```

### `httpd_log_formats`

Type: `list`. Required: `false`.

Global Apache LogFormat entries; combined is always provided and can be
redefined here.

Default:

```yaml
httpd_log_formats:
  - name: common
    format: '%h %l %u %t "%r" %>s %b'
  - name: combined
    format: '%h %l %u %t "%r" %>s %b "%{Referer}i" "%{User-Agent}i"'
```

### `httpd_custom_logs`

Type: `list`. Required: `false`.

Additional global CustomLog entries.

Default:

```yaml
httpd_custom_logs: []
```

### `httpd_directory_blocks`

Type: `list`. Required: `false`.

Global Apache Directory blocks.

Default:

```yaml
httpd_directory_blocks:
  - path: /
    allow_override: None
    options: None
    require:
      - all denied
```

### `httpd_files_blocks`

Type: `list`. Required: `false`.

Global Apache Files blocks.

Default:

```yaml
httpd_files_blocks:
  - pattern: .ht*
    require:
      - all denied
```

### `httpd_mime_enabled`

Type: `bool`. Required: `false`.

Whether to render the global MIME basics block.

Default:

```yaml
httpd_mime_enabled: true
```

### `httpd_mime_types`

Type: `list`. Required: `false`.

Global Apache AddType definitions.

Default:

```yaml
httpd_mime_types:
  - content_type: text/css
    extensions:
      - .css
  - content_type: application/javascript
    extensions:
      - .js
```

### `httpd_listen`

Type: `list`. Required: `false`.

Global Apache Listen directives.

Default:

```yaml
httpd_listen:
  - port: 80
  - port: 443
    protocol: https
```

### `httpd_tls_session_cache_timeout`

Type: `int`. Required: `false`.

Global SSLSessionCacheTimeout in seconds.

Default:

```yaml
httpd_tls_session_cache_timeout: 300
```

### `httpd_tls_use_stapling`

Type: `str`. Required: `false`.

Global SSLUseStapling directive.

Default:

```yaml
httpd_tls_use_stapling: 'Off'
```

### `httpd_tls_random_seeds`

Type: `list`. Required: `false`.

Global SSLRandomSeed directives. The bytes key is optional and must be omitted
for source=builtin.

Default:

```yaml
httpd_tls_random_seeds:
  - context: startup
    source: file:/dev/urandom
    bytes: 512
  - context: connect
    source: builtin
```

### `httpd_extra_modules`

Type: `list`. Required: `false`.

Additional Apache modules rendered after the role-required module set.

Default:

```yaml
httpd_extra_modules: []
```

### `httpd_vhost_files`

Type: `list`. Required: `false`.

Complete Apache-native virtual host files written into the managed vhost
directory.

Default:

```yaml
httpd_vhost_files: []
```

## Managed Files

- `/etc/httpd/conf/httpd.conf`
- `/etc/httpd/managed/modules.d/00-modules.conf`
- `/etc/httpd/managed/conf.d/00-listen.conf`
- `/etc/httpd/managed/conf.d/00-tls.conf`
- `/etc/httpd/managed/vhost.d/000-default-deny.conf`
- `/etc/httpd/managed/vhost.d/*.conf` for declared `httpd_vhost_files`
- `/etc/httpd/managed/tls/certs/httpd-default-deny.crt` when HTTPS listeners are
  configured
- `/etc/httpd/managed/tls/private/httpd-default-deny.key` when HTTPS listeners
  are configured
- `/etc/httpd/managed/tls/csr/httpd-default-deny.csr` when HTTPS listeners are
  configured
- `/etc/apache2/apache2.conf` or `/etc/apache2/httpd.conf`
- `/etc/apache2/managed/modules.d/00-modules.conf`
- `/etc/apache2/managed/conf.d/00-listen.conf`
- `/etc/apache2/managed/conf.d/00-tls.conf`
- `/etc/apache2/managed/vhost.d/000-default-deny.conf`
- `/etc/apache2/managed/vhost.d/*.conf` for declared `httpd_vhost_files`
- `/etc/apache2/managed/tls/certs/httpd-default-deny.crt` when HTTPS listeners
  are configured
- `/etc/apache2/managed/tls/private/httpd-default-deny.key` when HTTPS listeners
  are configured
- `/etc/apache2/managed/tls/csr/httpd-default-deny.csr` when HTTPS listeners are
  configured

## Check Mode

Check mode predicts package, directory, template, copy, and file changes,
including before the first installation.
Private key, CSR, and certificate generation, SUSE MPM selection, service tasks,
and handlers are skipped in check mode because their prerequisites may not exist
until an actual run.
These skipped steps do not report predicted changes, including on already
configured hosts.
Effective `httpd -t -f` validation is also skipped because first-run systems may
not have the Apache binary or include files yet.

## Service Behavior

The role always ensures the Apache service is enabled and started.
Module configuration and SUSE MPM selection changes notify `restart`, handled by
`HTTPD | Restart service`,
so MPM changes replace the Apache parent process.
Other managed configuration and default-deny certificate changes notify
`reload`, handled by `HTTPD | Reload service`.
When both handlers are notified, the restart runs before the reload.

### Handlers

- HTTPD | Restart service
- HTTPD | Reload service

## Security Notes

- Distribution package include wildcards are intentionally not included.
- The default-deny vhost is rendered as `000-default-deny.conf` so it sorts
  before normal vhost files.
- HTTPS listeners use a local invalid self-signed certificate for the
  default-deny vhost and do not consume a public or trusted certificate.
- Unknown or unmatched hostnames are denied by strict host/SNI checks or by the
  default-deny vhost.
- Proxy, FastCGI proxy, WebSocket proxy, compatibility, headers, and HTTP/2
  modules are not part of the default minimal module set.
- Application vhost files are Apache-native content; the role does not provide
  an application vhost DSL.
- TLS certificates for application vhosts are not issued, renewed, or deployed
  by this role.
- Logs are written below `/var/log`, not below `/etc`.

## Operational Notes

- Idempotency: applying the same inputs to an already converged host makes no
  changes and does not restart or reload the service.
- The role writes module, listener, TLS, and vhost changes before validating the
  main and complete configuration. A validation failure stops the role before
  service start or handler execution; files already written remain on disk, with
  module-provided backups for replacements.
- Present application vhost files in `httpd_vhost_files` must use names sorting
  after `000-default-deny.conf`.
- `httpd_listen` defaults to HTTP and HTTPS; mark additional HTTPS listeners
  with `protocol: https`.
- Listener address and port endpoints in `httpd_listen` must be unique.
- The main and default-deny access logs use `combined`. The role always defines
  this format before `httpd_log_formats`, so an empty list or a list without
  `combined` retains standard combined logging. An explicit `combined` entry
  overrides the built-in format.
- `httpd_custom_logs` entries require exactly one target (`file` or `pipe`),
  exactly one format (`format_name` or `format_string`), and optionally one
  condition (`env` or `expr`).
- Add optional modules through `httpd_extra_modules`; required packages belong
  in `httpd_extra_packages`.
- Module entries are deduplicated by name. The first entry wins; role-required
  modules take precedence over `httpd_extra_modules`.
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
