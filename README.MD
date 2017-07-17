# Influx data


## Setup

```bash
vagrant up
```

## Needed software

* vagrant
* vbox

## Virtual machine

| vm | Desctiption |
| --- | --- |
| Influxdata | influxdb:last, chronograf, kapacitor and telegraf | 
| node1 | Cluster node 1 (influxdb0.9.6) |
| node2 | Cluster node 2 (influxdb0.9.6) |
| node3 | Cluster node 3 (influxdb0.9.6) |

## Services

| Service | Version | Use port | Url to UI |
| --- | --- | --- | --- |
| influxdb | last | 8086 | n/a |
| chronograf | last | 8888 | http://influxdata:8888 OR http://13.13.1.10:8888 |
| kapacitor | last | 9092 | n/a |
| telegraf | last | 8092, 8125 and 8094 | n/a |
| Cluster node 1 | 0.9.6 | 8088, 8086 and 8083 | http://node1:8083 OR http://13.13.1.11:8083 |
| Cluster node 2 | 0.9.6 | 8088, 8086 and 8083 | http://node2:8083 OR http://13.13.1.12:8083 |
| Cluster node 3 | 0.9.6 | 8088, 8086 and 8083 | http://node3:8083 OR http://13.13.1.13:8083 |
