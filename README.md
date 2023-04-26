
# Ansible Role:  `mysql-exporter` 

Ansible role to install and configure [mysqld_exporter](https://github.com/prometheus/mysqld_exporter).


[![GitHub Workflow Status](https://img.shields.io/github/actions/workflow/status/bodsch/ansible-mysql-exporter/main.yml?branch=main)][ci]
[![GitHub issues](https://img.shields.io/github/issues/bodsch/ansible-mysql-exporter)][issues]
[![GitHub release (latest by date)](https://img.shields.io/github/v/release/bodsch/ansible-mysql-exporter)][releases]
[![Ansible Quality Score](https://img.shields.io/ansible/quality/50067?label=role%20quality)][quality]

[ci]: https://github.com/bodsch/ansible-mysql-exporter/actions
[issues]: https://github.com/bodsch/ansible-mysql-exporter/issues?q=is%3Aopen+is%3Aissue
[releases]: https://github.com/bodsch/ansible-mysql-exporter/releases
[quality]: https://galaxy.ansible.com/bodsch/mysql_exporter


If `latest` is set for `mysql_exporter_version`, the role tries to install the latest release version.  
**Please use this with caution, as incompatibilities between releases may occur!**

The binaries are installed below `/usr/local/bin/mysql_exporter/${mysql_exporter_version}` and later linked to `/usr/bin`. 
This should make it possible to downgrade relatively safely.

The downloaded archive is stored on the Ansible controller, unpacked and then the binaries are copied to the target system.
The cache directory can be defined via the environment variable `CUSTOM_LOCAL_TMP_DIRECTORY`. 
By default it is `${HOME}/.cache/ansible/mysql_exporter`.
If this type of installation is not desired, the download can take place directly on the target system. 
However, this must be explicitly activated by setting `mysql_exporter_direct_download` to `true`.

## Requirements & Dependencies

Ansible Collections

- [bodsch.core](https://github.com/bodsch/ansible-collection-core)
- [bodsch.scm](https://github.com/bodsch/ansible-collection-scm)

```bash
ansible-galaxy collection install bodsch.core
ansible-galaxy collection install bodsch.scm
```
or
```bash
ansible-galaxy collection install --requirements-file collections.yml
```

## Operating systems

Tested on

* ArchLinux
* Debian based
    - Debian 10 / 11
    - Ubuntu 20.04
* RedHat based
    - Alma Linux 8
    - Rocky Linux 8
    - OracleLinux 8

## Contribution

Please read [Contribution](CONTRIBUTING.md)

## Development,  Branches (Git Tags)

The `master` Branch is my *Working Horse* includes the "latest, hot shit" and can be complete broken!

If you want to use something stable, please use a [Tagged Version](https://github.com/bodsch/ansible-mysql-exporter/tags)!

## Configuration

```yaml
mysql_exporter_version: "0.14.0"

mysql_exporter_release_download_url: https://github.com/prometheus/mysqld_exporter/releases

mysql_exporter_system_user: mysql_exporter
mysql_exporter_system_group: mysql_exporter

mysql_exporter_direct_download: false

mysql_exporter_service: {}

mysql_exporter_credentials:
  client:
    hostname: ""
    port: ""
    socket: ""
    username: ""
    password: ""

mysql_exporter_collectors: []
```

### service configuration

```yaml
mysql_exporter_service:
  log:
    level: info
    format: ""
  web:
    listen_address: "127.0.0.1:9104"
    telemetry_path: /metrics
  exporter:
    lock_wait_timeout: ""                  # 2
    log_slow_filter: ""                    # true | false
  timeout_offset: ""                       # 0.25
  config:
    my_cnf: "{{ mysql_exporter_config_dir }}/mysql_exporter.cnf"
  raw_flags: {}
```

### data source credentials

Using UNIX domain sockets and authentication:


```yaml
mysql_exporter_credentials:
  client:
    socket: "/run/mysqld/mysqld.sock"
    username: "prometheus"
    password: "nopassword"
```

or using a TCP connection and password authentication:

```yaml
mysql_exporter_credentials:
  client:
    hostname: "hostname"
    port: "3306"
    socket: "dbname"
    username: "prometheus"
    password: "nopassword"
```


### collectors

 [see also](https://github.com/prometheus/mysqld_exporter#collector-flags)

| collector name | description |
| :---           | :----        |
| `heartbeat.database="heartbeat"`               | Database from where to collect heartbeat data |
| `heartbeat.table="heartbeat"`                  | Table from where to collect heartbeat data |
| `info_schema.processlist.min_time=0`           | Minimum time a thread must be in each state to be counted |
| `info_schema.tables.databases="*"`             | The list of databases to collect table stats for, or '*' for all |
| `perf_schema.eventsstatements.limit=250`       | Limit the number of events statements digests by response time |
| `perf_schema.eventsstatements.timelimit=86400` | Limit how old the 'last_seen' events statements can be, in seconds |
| `perf_schema.eventsstatements.digest_text_limit=120`         | Maximum length of the normalized statement text |
| `perf_schema.file_instances.filter=".*"`       | RegEx file_name filter for `performance_schema.file_summary_by_instance` |
| `perf_schema.file_instances.remove_prefix="/var/lib/mysql/"` |  Remove path prefix in `performance_schema.file_summary_by_instance` |
| `global_variables`                             | Collect from `SHOW GLOBAL VARIABLES` |
| `slave_status`                                 | Collect from `SHOW SLAVE STATUS` |
| `info_schema.processlist`                      | Collect current thread state counts from the `information_schema.processlist` |
| `info_schema.tables`                           | Collect metrics from `information_schema.tables` |
| `info_schema.innodb_tablespaces`               | Collect metrics from `information_schema.innodb_sys_tablespaces` |
| `info_schema.innodb_metrics`                   | Collect metrics from `information_schema.innodb_metrics` |
| `auto_increment.columns`                       | Collect auto_increment columns and max values from `information_schema` |
| `global_status`                                | Collect from `SHOW GLOBAL STATUS` |
| `perf_schema.tableiowaits`                     | Collect metrics from `performance_schema.table_io_waits_summary_by_table` |
| `perf_schema.indexiowaits`                     | Collect metrics from `performance_schema.table_io_waits_summary_by_index_usage` |
| `perf_schema.tablelocks`                       | Collect metrics from `performance_schema.table_lock_waits_summary_by_table` |
| `perf_schema.eventsstatements`                 | Collect metrics from `performance_schema.events_statements_summary_by_digest` |
| `perf_schema.eventswaits`                      | Collect metrics from `performance_schema.events_waits_summary_global_by_event_name` |
| `perf_schema.file_events`                      | Collect metrics from `performance_schema.file_summary_by_event_name` |
| `perf_schema.file_instances`                   | Collect metrics from `performance_schema.file_summary_by_instance` |
| `binlog_size`                                  | Collect the current size of all registered binlog files |
| `info_schema.userstats`                        | If running with `userstat=1`, set to `true` to collect user statistics |
| `info_schema.clientstats`                      | If running with `userstat=1`, set to `true` to collect client statistics |
| `info_schema.tablestats`                       | If running with `userstat=1`, set to `true` to collect table statistics |
| `info_schema.innodb_cmp`                       | Collect metrics from `information_schema.innodb_cmp` |
| `info_schema.innodb_cmpmem`                    | Collect metrics from `information_schema.innodb_cmpmem` |
| `info_schema.query_response_time`              | Collect query response time distribution if `query_response_time_stats` is `ON`. |
| `engine_tokudb_status`                         | Collect from `SHOW ENGINE TOKUDB STATUS` |
| `perf_schema.replication_group_member_stats`   | Collect metrics from `performance_schema.replication_group_member_stats` |
| `heartbeat`                                    | Collect from heartbeat |
| `slave_hosts`                                  | Scrape information from `SHOW SLAVE HOSTS` |
| `engine_innodb_status`                         | Collect from `SHOW ENGINE INNODB STATUS` |

#### Example

```yaml
mysql_exporter_collectors:
  - global_variables
  - engine_innodb_status
```


---

## Author

- Bodo Schulz

## License

This project is licensed under Apache License. See [LICENSE](/LICENSE) for more details.

**FREE SOFTWARE, HELL YEAH!**
