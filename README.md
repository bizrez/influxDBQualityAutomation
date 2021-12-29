# influxDB Quality Automation
InfluxDB Playground with a QA Data Structure

```
curl /etc/*-release 
```
## Install InfluxDB

```
wget -qO- https://repos.influxdata.com/influxdb.key | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/influxdb.gpg > /dev/null
export DISTRIB_ID=$(lsb_release -si); export DISTRIB_CODENAME=$(lsb_release -sc)
echo "deb [signed-by=/etc/apt/trusted.gpg.d/influxdb.gpg] https://repos.influxdata.com/${DISTRIB_ID,,} ${DISTRIB_CODENAME} stable" | sudo tee /etc/apt/sources.list.d/influxdb.list > /dev/null

sudo apt-get update && sudo apt-get install influxdb2

```








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
