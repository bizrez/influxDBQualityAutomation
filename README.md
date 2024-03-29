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

### REST API
[https://docs.influxdata.com/influxdb/v2.1/write-data/developer-tools/api/](https://docs.influxdata.com/influxdb/v2.1/write-data/developer-tools/api/)

InfluxDB API Write
```
curl --request POST \
"https://us-east-1-1.aws.cloud2.influxdata.com/api/v2/write?org=52dc5555563fd3fb&data&precision=ns" \
  --header "Authorization: Token vvvvvvvvxYP4MJ-zpQYAQE21mQTADV-LjP__Hl3veAwv_jXaFajvXcqR3zHA==" \
  --header "Content-Type: text/plain; charset=utf-8" \
  --header "Accept: application/json" \
  --data-binary 'register,testrun=001 executed=10,pass=10,fail=0'
```
Get Buckets
```
curl --location --request GET 'https://us-east-1-1.aws.cloud2.influxdata.com/api/v2/buckets' \
--header 'Authorization: Token vvvvvvP4MJ-zpQYAQE21mQTADV-LjP__Hl3veAwv_jXaFajvXcqR3zHA==' \
--data-raw ''
```

**BEST BET** So much easier than encoding the JSON string. Returns CSV
Query Content-Type application/vnd.flux
```
curl --location --request POST 'https://us-east-1-1.aws.cloud2.influxdata.com/api/v2/query' \
--header 'Authorization: Token vvvvvvvxYP4MJ-zpQYAQE21mQTADV-LjP__Hl3veAwv_jXaFajvXcqR3zHA==' \
--header 'Content-Type: application/vnd.flux' \
--data-raw 'from(bucket:"data") |> range(start:-30d)
            |> filter(fn: (r) => r["_measurement"] == "register")'

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
Example of Office Script
- Uses Fetch to callout Influxdb
- Content-Type: application/vnd.flux
- Raw data is the Fluxquery
- Parse the CSV results into an Array
- Array is Saved in an Excel sheet

``` 
function main(workbook: ExcelScript.Workbook) {

  var myHeaders = new Headers();
  myHeaders.append("Authorization", "Token QYAQE21mQTADV-LjP__Hl3veAwv_jXaFajvXcqR3zHA==");
  myHeaders.append("Content-Type", "application/vnd.flux");

  var raw = "from(bucket:\"ms-jwt-auth\") |> range(start:-365d)\n|> filter(fn: (r) => r[\"_measurement\"] == \"register\")";

  var requestOptions = {
    method: 'POST',
    headers: myHeaders,
    body: raw,
    redirect: 'follow'
  };


  await fetch("https://us-east-1-1.aws.cloud2.influxdata.com/api/v2/query", requestOptions)
    .then(response => response.text())
    .then(result => {
      
      result = result.split('\n')
      result = result.map((row) => row.split(','));
      result.forEach((element) => {
        console.log(element);
      });
      
      //Declare the worksheet to paste the data to
      let ws = workbook.getWorksheet("data")
      //Clear the existing worksheet data
      ws.getRange().clear(ExcelScript.ClearApplyTo.all)
      //Declare the range to paste the data to
      let rng = ws.getRange("A1").getAbsoluteResizedRange(result.length, result[0].length)

      //Paste the data
      rng.setValues(result);

      
    }
      
    
    )
    .catch(error => console.log('error', error));

  console.log('function end')

}
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
