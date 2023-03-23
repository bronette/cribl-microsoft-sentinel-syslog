# Cribl Syslog to Sentinel Syslog
----

This pack is designed to work with Syslog sources within Cribl Edge/Stream and convert Syslog events into the Sentinel Syslog table (https://learn.microsoft.com/en-us/azure/azure-monitor/reference/tables/syslog).


## Requirements Section

Before you begin, ensure that you have met the following requirements:

* Setup a Microsoft Sentinel webhook destination to write to the Syslog native table configuring a webhook outlined in guide here: https://docs.cribl.io/stream/usecase-azure-webhook/
* Deploy a Syslog collector.
* Apply the Syslog pre-processor pack https://github.com/criblpacks/cribl-syslog-input. 

## How it works
* Ingests the Syslog and split
* Convert the original fields from the Syslog payload into the Pascal Casing fields.


## Sample outputs

Original Security Event payload from Cribl Edge Windows Event forwarder: 

```
{
  "_raw": "dhcpd3: DHCPACK on 192.168.2.129 to 50:1e:2d:3e:8d:7e (cinema800-501e2d3e8d7e) via eth2",
  "host": "mdonnelly-router",
  "severityName": "info",
  "facilityName": "local2",
  "_time": 1637281980,
  "index": "firewall",
  "sourcetype": "ubiquiti",
  "source": "syslog:in_syslog:udp",
  "cribl_pipe": "CriblSyslogPreProcessing",
  "cribl_breaker": [
    "noBreak1MB",
    "noBreak1MB"
  ]
}
```

Converted Syslog into Sentinel Syslog schema.
```
{
  "index": "firewall",
  "cribl_pipe": "microsoft-sentinel-syslog",
  "SyslogMessage": "dhcpd3: DHCPACK on 192.168.2.129 to 50:1e:2d:3e:8d:7e (cinema800-501e2d3e8d7e) via eth2",
  "HostName": "mdonnelly-router",
  "SeverityLevel": "info",
  "Facility": "local2",
  "ProcessName": "ubiquiti",
  "SourceSystem": "Linux",
  "TimeGenerated": "2023-03-22T17:28:01.027Z",
  "EventTime": "2021-11-19T00:33:00.000Z",
  "Type": "Syslog",
  "HostIP": "192.168.2.129"
}
```

## Using The Pack

Here's the instructions to use the pack: 

1. Create a Syslog source and point your Syslog sources to the Cribl edge source listener. 
2. Apply the Syslog pre-processor pack here: https://github.com/criblpacks/cribl-syslog-input within the source to the "pre-processing" settings within the Syslog source you created in step1.
3. Configure both a Syslog destination outlined in the guide here: https://docs.cribl.io/stream/usecase-azure-webhook/.
3. Create a routes to handle syslog events with these settings:
    * Filter: true.
    * Source: Syslog source listener from step 1.
    * Pack: microsoft-sentinel-syslog.
    * Destination: Cribl Syslog Sentinel destination webhook. 

## TODO
* More testing and validation of events. 

## Release Notes

### Version 0.0.1 - 2023-03-22
This is the first release. Don't laugh.


## Contact
To contact us please email kbrunette@cribl.io.


## License
TThis Pack uses the following license: [`Apache 2.0`](https://www.apache.org/licenses/LICENSE-2.0).
