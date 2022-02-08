
# Ansible Role:  `mysql-exporter` 

Ansible role to install and configure [mysqld_exporter](https://github.com/prometheus/mysqld_exporter).

[![GitHub Workflow Status](https://img.shields.io/github/workflow/status/bodsch/ansible-mysql-exporter/CI)][ci]
[![GitHub issues](https://img.shields.io/github/issues/bodsch/ansible-mysql-exporter)][issues]
[![GitHub release (latest by date)](https://img.shields.io/github/v/release/bodsch/ansible-mysql-exporter)][releases]

[ci]: https://github.com/bodsch/ansible-mysql-exporter/actions
[issues]: https://github.com/bodsch/ansible-mysql-exporter/issues?q=is%3Aopen+is%3Aissue
[releases]: https://github.com/bodsch/ansible-mysql-exporter/releases


## Operating systems

Tested on

* Debian based
    - Debian 10 / 11
    - Ubuntu 20.04
* RedHat based
    - CentOS 8 (**not longer supported**)
    - Alma Linux 8
    - Rocky Linux 8
    - OracleLinux 8
* ArchLinux

## Contribution

Please read [Contribution](CONTRIBUTING.md)

## Development,  Branches (Git Tags)

The `master` Branch is my *Working Horse* includes the "latest, hot shit" and can be complete broken!

If you want to use something stable, please use a [Tagged Version](https://github.com/bodsch/ansible-mysql-exporter/tags)!

## Configuration

```yaml
mysql_exporter_version: "0.13.0"

mysql_exporter_release_download_url: https://github.com/prometheus/mysqld_exporter/releases

mysql_exporter_system_user: mysql_exporter
mysql_exporter_system_group: mysql_exporter

mysql_exporter_service_name: "mysql-exporter"

mysql_exporter_listen_address: 127.0.0.1
mysql_exporter_listen_port: 9104
mysql_exporter_telemetry_path: /metrics

mysql_exporter_config:
  data_source_name: "" 

mysql_exporter_logging:
  # debug, info, warn, error, fatal
  level: info
  # Declaration is unclear.
  # Although specified in usage, it leads to an unbootable service when used.
  # "logger:syslog?appname=bob&local=7" or "logger:stdout?json=true"
  # format: logger:stderr

mysql_exporter_enabled_collectors: []
```

### data source name

Using UNIX domain sockets and authentication:
```yaml
mysql_exporter_config:
  data_source_name: "prometheus:nopassword@unix(/run/mysqld/mysqld.sock)/"
```

or using a TCP connection and password authentication:

```yaml
mysql_exporter_config:
  data_source_name: "prometheus:nopassword@hostname:port/dbname"
```


### collectors

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
mysql_exporter_enabled_collectors:
  - global_variables
  - engine_innodb_status
```


---

## Author

- Bodo Schulz

## License

This project is licensed under Apache License. See [LICENSE](/LICENSE) for more details.

**FREE SOFTWARE, HELL YEAH!**
