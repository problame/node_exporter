# Node exporter

[![Build Status](https://travis-ci.org/prometheus/node_exporter.svg)](https://travis-ci.org/prometheus/node_exporter)

Prometheus exporter for machine metrics, written in Go with pluggable metric
collectors.

## Collectors

There is varying support for collectors on each operating system. The tables
below list all existing collectors and the supported systems.

Which collectors are used is controlled by the `--collectors.enabled` flag.

### Enabled by default

Name     | Description | OS
---------|-------------|----
conntrack | Shows conntrack statistics (does nothing if no `/proc/sys/net/netfilter/` present). | Linux
cpu | Exposes CPU statistics | FreeBSD
diskstats | Exposes disk I/O statistics from `/proc/diskstats`. | Linux
entropy | Exposes available entropy. | Linux
filefd | Exposes file descriptor statistics. | Linux
filesystem | Exposes filesystem statistics, such as disk space used. | FreeBSD, Linux, OpenBSD
loadavg | Exposes load average. | Darwin, Dragonfly, FreeBSD, Linux, NetBSD, OpenBSD, Solaris
mdadm | Exposes statistics about devices in `/proc/mdstat` (does nothing if no `/proc/mdstat` present). | Linux
meminfo | Exposes memory statistics. | FreeBSD, Linux
netdev | Exposes network interface statistics such as bytes transferred. | FreeBSD, Linux, OpenBSD
netstat | Exposes network statistics from `/proc/net/netstat`. This is the same information as `netstat -s`. | Linux
stat | Exposes various statistics from `/proc/stat`. This includes CPU usage, boot time, forks and interrupts. | Linux
textfile | Exposes statistics read from local disk. The `--collector.textfile.directory` flag must be set. | _any_
time | Exposes the current system time. | _any_
vmstat | Exposes statistics from `/proc/vmstat`. | Linux
version | Exposes node\_exporter version. | _any_
zfs | Exposes [ZFS](http://open-zfs.org/) performance statistics.<br/> FreeBSD (ARC, zpool), Linux (ARC) | [FreeBSD](https://www.freebsd.org/doc/handbook/zfs.html), [Linux](http://zfsonlinux.org/)

### Disabled by default

Name     | Description | OS
---------|-------------|----
bonding | Exposes the number of configured and active slaves of Linux bonding interfaces. | Linux
devstat | Exposes device statistics | FreeBSD
gmond | Exposes statistics from Ganglia. | _any_
interrupts | Exposes detailed interrupts statistics. | Linux, OpenBSD
ipvs | Exposes IPVS status from `/proc/net/ip_vs` and stats from `/proc/net/ip_vs_stats`. | Linux
ksmd | Exposes kernel and system statistics from `/sys/kernel/mm/ksm`. | Linux
lastlogin | Exposes the last time there was a login. | _any_
megacli | Exposes RAID statistics from MegaCLI. | Linux
meminfo_numa | Exposes memory statistics from `/proc/meminfo_numa`. | Linux
ntp | Exposes time drift from an NTP server. | _any_
runit | Exposes service status from [runit](http://smarden.org/runit/). | _any_
supervisord | Exposes service status from [supervisord](http://supervisord.org/). | _any_
systemd | Exposes service and system status from [systemd](http://www.freedesktop.org/wiki/Software/systemd/). | Linux
tcpstat | Exposes TCP connection status information from `/proc/net/tcp` and `/proc/net/tcp6`. (Warning: the current version has potential performance issues in high load situations.) | Linux

### Textfile Collector

The textfile collector is similar to the [Pushgateway](https://github.com/prometheus/pushgateway),
in that it allows exporting of statistics from batch jobs. It can also be used
to export static metrics, such as what role a machine has. The Pushgateway
should be used for service-level metrics. The textfile module is for metrics
that are tied to a machine.

To use it, set the `--collector.textfile.directory` flag on the Node exporter. The
collector will parse all files in that directory matching the glob `*.prom`
using the [text
format](http://prometheus.io/docs/instrumenting/exposition_formats/).

To atomically push completion time for a cron job:
```
echo my_batch_job_completion_time $(date +%s) > /path/to/directory/my_batch_job.prom.$$
mv /path/to/directory/my_batch_job.prom.$$ /path/to/directory/my_batch_job.prom
```

To statically set roles for a machine using labels:
```
echo 'role{role="application_server"} 1' > /path/to/directory/role.prom.$$
mv /path/to/directory/role.prom.$$ /path/to/directory/role.prom
```

## Building and running

    make
    ./node_exporter <flags>

## Running tests

    make test


## Using Docker

You can deploy this exporter using the [prom/node-exporter](https://registry.hub.docker.com/u/prom/node-exporter/) Docker image.

For example:

```bash
docker pull prom/node-exporter

docker run -d -p 9100:9100 --net="host" prom/node-exporter
```
