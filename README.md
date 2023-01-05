# influxDB Quality Automation
InfluxDB Playground with a QA Data Structure

InfluxDB stores data in shard groups. Shard groups are organized by retention policy (RP) and store data with timestamps that fall within a specific time interval. The length of that time interval is called the shard group duration.

**Encode meta data in Tags**  
Tags are indexed and fields are not indexed. This means that queries on tags are more performant than those on fields.

```
Data encoded in tags
-------------
weather_sensor,crop=blueberries,plot=1,region=north temp=50.1 1472515200000000000
weather_sensor,crop=blueberries,plot=2,region=midwest temp=49.8 1472515200000000000
```
**Key Concepts** [link](https://archive.docs.influxdata.com/influxdb/v1.2/concepts/key_concepts/)

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
Line Protocal
Syntax:

measurementName,tagKey=tagValue fieldKey="fieldValue" 1465839830100400200
--------------- --------------- --------------------- -------------------
       |               |                  |                    |
  Measurement       Tag set           Field set            Timestamp
Example:

myMeasurement,tag1=value1,tag2=value2 fieldKey="fieldValue"  



XRay Example/JUnit Results

Data Stored in Fields
Time Interval #1 
- Measurement=register  
- Three different test-run's  
- Filter || Tag by "testrun"

register,testrun=001 executed=10,pass=10,fail=0  
register,testrun=002 executed=10,pass=10,fail=0  
register,testrun=003 executed=10,pass=10,fail=0  

Returns the Sum of all testrun's
from(bucket: "data")
  |> range(start: v.timeRangeStart, stop: v.timeRangeStop)
  |> filter(fn: (r) => r["_measurement"] == "register")
  |> group(columns: ["testrun"])
  |> aggregateWindow(every: v.windowPeriod, fn: sum, createEmpty: false)
  |> yield(name: "mean")
  
Return ths sume of testrun=001
rom(bucket: "ms-jwt-auth")
  |> range(start: v.timeRangeStart, stop: v.timeRangeStop)
  |> filter(fn: (r) => r["_measurement"] == "register")
  |> filter(fn: (r) => r["testrun"] == "001")
  |> group(columns: ["_field"])
  |> aggregateWindow(every: v.windowPeriod, fn: sum, createEmpty: false)
  |> yield(name: "mean")

```

Executed 365 days

<img width="341" alt="image" src="https://user-images.githubusercontent.com/993459/210431517-69e87edc-30b3-491c-adcd-b6b667cdd44b.png">

<img width="341" alt="image" src="https://user-images.githubusercontent.com/993459/210431916-20df4bfa-1231-47ab-b906-e167d06beebc.png">

<img width="341" alt="image" src="https://user-images.githubusercontent.com/993459/210431988-3121fa97-2dfb-4671-9619-1128600dfc40.png">






### InfluxDB Downsampling ad Tasks
[https://docs.influxdata.com/influxdb/v2.1/process-data/common-tasks/downsample-data/](https://docs.influxdata.com/influxdb/v2.1/process-data/common-tasks/downsample-data/)

## Graphana Install
```
# Run Graphana
sudo service grafana-server status
```
