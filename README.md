# influxDB Quality Automation
InfluxDB Playground with a QA Data Structure

```
curl /etc/*-release 
```
## Install InfluxDB

[https://portal.influxdata.com/downloads/](https://portal.influxdata.com/downloads/)

```
wget -qO- https://repos.influxdata.com/influxdb.key | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/influxdb.gpg > /dev/null

export DISTRIB_ID=$(lsb_release -si); export DISTRIB_CODENAME=$(lsb_release -sc)

echo "deb [signed-by=/etc/apt/trusted.gpg.d/influxdb.gpg] https://repos.influxdata.com/${DISTRIB_ID,,} ${DISTRIB_CODENAME} stable" | sudo tee /etc/apt/sources.list.d/influxdb.list > /dev/null

sudo apt-get update && sudo apt-get install influxdb2

```

## Install Telegraf
```
wget https://dl.influxdata.com/telegraf/releases/telegraf_1.21.1-1_amd64.deb

sudo dpkg -i telegraf_1.21.1-1_amd64.deb
```



## InfluxDB Commands
```
show databases
exit
```
