# Introduction #

Nginn-messagebus includes simple performance monitoring tools that can compute various perf statistics and make them available through http.


# Details #

**This page is not complete yet, more details will be added**

## Basic information ##

The input for performance statistics is taken from application logs. NGinn-messagebus uses NLog library for logging and can use everything that goes through NLog to calculate various statistics. In order to be able to intercept log entries nginn-messagebus implements its own log target (NGPerfCounters) that must be configured in NLog config file in order for the perf stats to work.
The statistics are based on detecting particular log entries by pattern matching and extracting information from them. Currently nginn-messagebus implements the following perf statistics:
  * frequency calculation (frequency of specified log entry)
  * percentile calculation based on last NNN events
  * average (based on last NNN events)
  * count (counts the number of occurrences of a specific log entry)

Percentile and average perf counters work on numeric values, these values are extracted from log entries using a regular expression. Count and frequency perf counters work by counting all matching log entries.

## Configuration ##

Configuration of performance counters is quite complicated because it's done in several places:

> ### 1. NLog configuration ###

NLog must be configured to route performance-related entries to nginn-messagebus perf counters.

> ### 2. Perf counter configuration ###

Performance counters are configured in a dedicated xml config file. This file specifies what performance statistics will be calculated and which log entries will provide the input for calculations.

> ### 3. Http interface configuration ###

In order to retrieve the collected performance statistics nginn-messagebus must be configured to handle http requests. You can do that when configuring the message bus:

```
MessageBusConfigurator cfg = MessageBusConfigurator.Begin()
   .SetConnectionStrings(dbConnectionStrings)
   .SetEndpoint("sql://mydb/Queue1")
   .ConfigureHttpReceiver("http://+:9013")
   .AutoStartMessageBus(true)
   .FinishConfiguration();
```

Here we configure nginn-messagebus to listen on port 9013 on all IP addresses

## Retrieving perf stats ##

Performance statistics are accessible through http interface. You can see them in a browser by opening this url: http://localhost:9013/perf/
You should see a list of all defined performance counters. Clicking on any of them will show its current value (you can retrieve the value directly by requesting the following url: http://localhost:9013/perf/MessageHandling95 - where MessageHandling95 is the name of the perf counter)