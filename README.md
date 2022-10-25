prometheus_windows
=========

A role to install the Prometheus [agent](https://prometheus.io/blog/2021/11/16/agent/) and windows_exporter on a Windows system

Requirements
------------

As with all Windows roles, the `pywinrm` Python package is required on the machine executing the playbook.

Role Variables
--------------

In most cases, the only two required variables for this role are the [remote write URL](https://docs.sysdig.com/en/docs/installation/prometheus-remote-write/#endpoints-and-regions) for your Sysdig backend and a valid Sysdig Monitor API token.
```yaml
prom_remote_write_url: https://us2.app.sysdig.com/prometheus/remote/write
prom_remote_write_token: 00000000-1111-2222-3333-444444444444
```

The log level and scrape interval for the Prometheus agent can be customized:
```yaml
prom_scrape_interval: 10s
prom_server_log_level: warn
```

The Windows exporters is scraped by default, but additional scrape targets can be configured using the [Prometheus scrape config](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config) format:
```yaml
prom_extra_scrape_configs:
- job_name: my_exporter
  static_configs:
    - targets: ["localhost:9100"]
```

Additional labels can be added to each metric:
```yaml
prom_global_labels:
  env: lab
  region: us-east-1
```

Artifacts are downloaded from the internet. Files can be internally mirrored if needed, or updated to new versions by changing these vars:
```yaml
prom_windows_exporter_installer: https://github.com/prometheus-community/windows_exporter/releases/download/v0.20.0/windows_exporter-0.20.0-amd64.msi

prom_server_package: https://github.com/prometheus/prometheus/releases/download/v2.39.1/prometheus-2.39.1.windows-amd64.zip
prom_server_package_checksum: 7e2524bb498e29d607be949d070a95e6da283973f363ad87c40a8de29c219220
prom_server_version: "2.39.1"
prom_server_arch: amd64

prom_nssm_package: https://nssm.cc/ci/nssm-2.24-101-g897c7ad.zip
prom_nssm_package_checksum: 99f5045fffbffb745d67fe3a065a953c4a3d9c253b868892d9b685b0ee7d07b8
prom_nssm_version: 2.24-101-g897c7ad
prom_nssm_arch: win64
```

This role also allows customization of the program and data directories used by Prometheus: (the temp directory is only used during installation to download artifacts)
```yaml
prom_temp_dir: "%TEMP%"
prom_app_dir: C:\Program Files\prometheus
prom_data_dir: "%ProgramData%\\prometheus"
prom_config_file: "{{ prom_data_dir }}\\prometheus.yml"
```

Example Playbook
----------------

Below is an example playbook using the bare minimal remote write credentials and some additional labels.

```yaml
- name: install and configure Prometheus for Sysdig Monitor
  hosts: win
  vars:
    prom_remote_write_url: https://us2.app.sysdig.com/prometheus/remote/write
    prom_remote_write_token: 00000000-1111-2222-3333-444444444444
    prom_global_labels:
      env: lab
      region: us-east-1
  roles:
    - prometheus_windows
```

License
-------

Apache 2.0
