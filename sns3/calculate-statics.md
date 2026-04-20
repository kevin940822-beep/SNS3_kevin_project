## calculate throughput

## Step

**主要透過 `SatStatsThroughputHelper` 函式計算**

[satellite-stats-helper-container.cc](https://github.com/sns3/sns3-satellite/blob/master/stats/satellite-stats-helper-container.cc)


### 1️⃣Trace Source Connection

For application-level throughput, it connects to the **"Rx"** trace source of applications

Line 1338
```
if (app->TraceConnectWithoutContext("Rx", callback))  
{  
    NS_LOG_INFO(this << " successfully connected with node ID " << (*it)->GetId()  
                     << " application #" << i);  
}
```
### 2️⃣Unit Conversion

`UnitConversionCollector` with `FROM_BYTES_TO_KBIT` conversion type

Line 144
```
m_conversionCollectors.SetType("ns3::UnitConversionCollector");  
m_conversionCollectors.SetAttribute("ConversionType",  
                                    EnumValue(UnitConversionCollector::FROM_BYTES_TO_KBIT));
```

### 3️⃣Rate Calculation

- For scalar output : Uses `ScalarCollector` with `OUTPUT_TYPE_AVERAGE_PER_SECOND` to calculate average throughput in kbps

Line 132
```
m_terminalCollectors.SetType("ns3::ScalarCollector");  
m_terminalCollectors.SetAttribute("InputDataType",  
                                  EnumValue(ScalarCollector::INPUT_DATA_TYPE_DOUBLE));  
m_terminalCollectors.SetAttribute(  
    "OutputType",  
    EnumValue(ScalarCollector::OUTPUT_TYPE_AVERAGE_PER_SECOND));
```

- For scatter output : Uses `IntervalRateCollector` for time-series throughput data

Line 163
```
 m_terminalCollectors.SetType("ns3::IntervalRateCollector");
        m_terminalCollectors.SetAttribute("InputDataType",
                                          EnumValue(IntervalRateCollector::INPUT_DATA_TYPE_DOUBLE));
        CreateCollectorPerIdentifier(m_terminalCollectors);
```
