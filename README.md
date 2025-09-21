# opnsense-notes

Firewall Logging via Influxdb


from https://forum.opnsense.org/index.php?topic=40141.0


I do very minimal filter logging with my OPNsense implementation for home use but I always wanted slightly better filter log views. I have been using Telegraf, Influxdb, Packetbeat & Grafana for general system and DNS monitoring. I finally decided to spend a little time to work on adding in the filter log and it turned out be relatively straight forward for the limited fields I needed (source, destination, destination port, rule number and label) for IPV4, tcp/udp, and blocks only. Here is the snippet that I added for Telegraf to /usr/local/etc/telegraf.d/custom.conf:

```
[[inputs.tail]]
  files = ["/var/log/filter/latest.log"]
  from_beginning = false
  pipe = false
  watch_method = "poll"
  name_override = "filterlog"
  data_format = "grok"
  grok_patterns=["<%{NONNEGINT}>%{NONNEGINT} %{TIMESTAMP_ISO8601:timestamp:ts} %{HOSTNAME} %{WORD} %{NONNEGINT} \\- \\[%{GREEDYDATA}\\] %{INT:rulenum},%{DATA},%{DATA},%{DATA:label:tag},%{DATA:interface:tag},%{DATA},%{DATA},%{DATA},%{INT},%{BASE16NUM},%{DATA},%{INT},%{INT},%{INT},%{DATA},%{INT},%{DATA:proto:tag},%{INT},%{IPV4:src:tag},%{IPV4:dst:tag},%{INT},%{INT:dst_port:tag}"]
```
