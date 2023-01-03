# influxDB Quality Automation
InfluxDB Playground with a QA Data Structure

```
# Linux release version
cat /etc/*-release 

output:
DISTRIB_ID=Ubuntu
DISTRIB_RELEASE=20.04
DISTRIB_CODENAME=focal
DISTRIB_DESCRIPTION="Ubuntu 20.04.3 LTS"
...
```
```
# Verify influxdb is running
curl -sL -I localhost:8086/ping

output:
HTTP/1.1 204 No Content
X-Influxdb-Build: OSS
X-Influxdb-Build: oss2
X-Influxdb-Version: 2.1.1
X-Influxdb-Version: 2.1.1
Date: Wed, 29 Dec 2021 22:24:43 GMT

```
## Install InfluxDB and Run

[https://portal.influxdata.com/downloads/](https://portal.influxdata.com/downloads/)

```
wget -qO- https://repos.influxdata.com/influxdb.key | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/influxdb.gpg > /dev/null

export DISTRIB_ID=$(lsb_release -si); export DISTRIB_CODENAME=$(lsb_release -sc)

echo "deb [signed-by=/etc/apt/trusted.gpg.d/influxdb.gpg] https://repos.influxdata.com/${DISTRIB_ID,,} ${DISTRIB_CODENAME} stable" | sudo tee /etc/apt/sources.list.d/influxdb.list > /dev/null

sudo apt-get update && sudo apt-get install influxdb2

```
```
influxd # Run via command line
```

## Install Telegraf
```
wget https://dl.influxdata.com/telegraf/releases/telegraf_1.21.1-1_amd64.deb

sudo dpkg -i telegraf_1.21.1-1_amd64.deb
```

```
# The influxdb portal will have a telgraf setup wizard, that will crate:1) telegraph.conf and the below executable command line. You will have to insure the ports are open for the telgraph service.

telegraf --config https://mallond-bizrez-influxdbqualityautomation-jrwvr7x2j7x-8086.githubpreview.dev/api/v2/telegrafs/08ae1cc4cce14000
```


## InfluxDB Commands
```
show databases
exit
```

### InfluxDB API Write
[https://docs.influxdata.com/influxdb/v2.1/write-data/developer-tools/api/](https://docs.influxdata.com/influxdb/v2.1/write-data/developer-tools/api/)
```
curl --request POST \
"http://localhost:8086/api/v2/write?org=YOUR_ORG&bucket=YOUR_BUCKET&precision=ns" \
  --header "Authorization: Token YOUR_API_TOKEN" \
  --header "Content-Type: text/plain; charset=utf-8" \
  --header "Accept: application/json" \
  --data-binary '
    airSensors,sensor_id=TLM0201 temperature=73.97038159354763,humidity=35.23103248356096,co=0.48445310567793615 1630424257000000000
    airSensors,sensor_id=TLM0202 temperature=75.30007505999716,humidity=35.651929918691714,co=0.5141876544505826 1630424257000000000
    '
```
### Data Concept
```
<measurement>,<tag>[,<tags>] <field>[,<field>] <timestamp>
weather,location=us-midwest temperature=82 1465839830100400200

XRay Example
test-name, type=automation, definition=api-test01, test-executed=24 test-pass=20, test-fail=4


```
### InfluxDB Downsampling ad Tasks
[https://docs.influxdata.com/influxdb/v2.1/process-data/common-tasks/downsample-data/](https://docs.influxdata.com/influxdb/v2.1/process-data/common-tasks/downsample-data/)

## Graphana Install
```
# Run Graphana
sudo service grafana-server status
```
