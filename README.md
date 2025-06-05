# Fronius_Telegraf
![Auswahl_047](https://github.com/user-attachments/assets/987d7b6e-1e9f-4979-a560-223da435ead7)

telegraf.conf that transfers Fronius Symo Hybrid Api to influxDB

# Intention
having bought a Fronius Hybrid 5, Sony Battery 6.0 and Smartmeter 63A, used and manufactured in 2016 i wanted to have them monitored.

# Installation
typical TIG Stack needed. Telegraf, InfluxDB and Grafana.
Copy telegraf_fronius.conf into your system. 
Modify the IP-Adress used in the api calls.
Modify name of your influxDB, user and password if needed.

# how it works 
## tell Telegraf to use http as input plugin and call the measurement "smartmeter". 

```sh
# Read formatted metrics from one or more HTTP endpoints
[[inputs.http]]
  # measurement name
  name_override = "smartmeter"
  # override default http query interval
  interval = "1m"
```
## this is one of the api-urls we want to use, please adapt IP-Adress
```sh
  urls = [
    "http://172.16.0.171/solar_api/v1/GetMeterRealtimeData.cgi?Scope=System"
  ]
```
## Data format to consume.
```sh
  # json parsing
  data_format = "json"
  json_time_key = "Body_Data_0_TimeStamp"
  json_time_format = "unix"
```
## we are only interested in some statements, that's filtering

```sh
  # data filtering
  # EnergyReal_WAC_Plus_Absolute ... Import
  # EnergyReal_WAC_Minus_Absolute ... Export
  # Power* individual phases; *_Sum only sum of all phases.
  fieldpass = [
    "Body_Data_0_Energy*",
    "Body_Data_0_Current_AC_*",
    "Body_Data_0_Power*",
    # "Body_Data_0_*_Sum"
    ]

```
## use more api urls like these:
```
"http://172.16.0.171/solar_api/v1/GetPowerFlowRealtimeData.fcgi"
"http://172.16.0.171/solar_api/v1/GetStorageRealtimeData.cgi?Scope=System"
```

## specify output as influxDB, location and name of your database
```sh
urls = ["http://127.0.0.1:8086"]
  ## The target database for metrics; will be created as needed.
  database = "telegraf_fronius"
```


# Dashboard
feel free to use my dashboard. Import the Fronius_...json into Grafana

# Details
![Auswahl_20250508_004](https://github.com/user-attachments/assets/a6f05a01-d6f4-4a81-8e67-a0546c080f37)
Did some math to have Netzbezug, Direktverbrauch and Hausverbrauch as numbers available


![Auswahl_20250508_001](https://github.com/user-attachments/assets/d7c2b626-40d3-403b-80e0-cd03a6a9e434)
Having a small battery main focus is Autarkie. You will see this several times in my dashboard.


![Auswahl_20250508_002](https://github.com/user-attachments/assets/47acc99e-a9a3-4ebf-9441-11c7875e203c)
as min-SoC is set to 10% the gauge shows the usable kWh remaining. You may not have 'Gartenstudio' and 'ökofen' as i use seperate S0-counters for them.


![Auswahl_20250508_003](https://github.com/user-attachments/assets/f1254e63-35a6-448e-aedd-c17d731e27a3)
you may not have solarprediction as i use solarprognose.de to do the forecast.
If your installation uses more or less battery modules you will have to modify the dashboard. 


interested in details about installation of these used components with new solar panels? 
Check https://nc-x.com/diy-pv-anlage-fronius-hybrid-gebr
