# influxDB Quality Automation
InfluxDB Playground with a QA Data Structure

```
curl /etc/*-release 
```
## Install InfluxDB
```
sudo curl -sL https://repos.influxdata.com/influxdb.key | sudo apt-key add -
```
```
sudo echo "deb https://repos.influxdata.com/ubuntu bionic stable" | sudo tee /etc/apt/sources.list.d/influxdb.list
```
```
sudo apt update
```
```
sudo apt install influxdb
```
```
sudo influxd #start influxdb
```
```
influx #command line prompt
```

## InfluxDB Commands
```
show databases
exit
```