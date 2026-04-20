# Table of Contnet
- [calculate throughput](#calculate-throughput)
- [calculate SINR](#calculate-sinr)

## calculate throughput

## Step

**主要透過 `SatStatsThroughputHelper` 函式計算**

[satellite-stats-helper-container.cc](https://github.com/sns3/sns3-satellite/blob/master/stats/satellite-stats-helper-container.cc)


### 1️⃣Trace Source Connection

For application-level throughput, it connects to the **"Rx"** **trace source**(追蹤來源) of applications

[Line 1338](https://github.com/sns3/sns3-satellite/blob/0fc2b8c7/stats/satellite-stats-throughput-helper.cc#L1338)
```
if (app->TraceConnectWithoutContext("Rx", callback))  
{  
    NS_LOG_INFO(this << " successfully connected with node ID " << (*it)->GetId()  
                     << " application #" << i);  
}
```
### 2️⃣Unit Conversion

`UnitConversionCollector` with `FROM_BYTES_TO_KBIT` conversion type (將原始位元組計數轉換為資料速率)

[Line 144](https://github.com/sns3/sns3-satellite/blob/0fc2b8c7/stats/satellite-stats-throughput-helper.cc#L144)
```
m_conversionCollectors.SetType("ns3::UnitConversionCollector");  
m_conversionCollectors.SetAttribute("ConversionType",  
                                    EnumValue(UnitConversionCollector::FROM_BYTES_TO_KBIT));
```

### 3️⃣Rate Calculation

- For **scalar output** : Uses `ScalarCollector` with `OUTPUT_TYPE_AVERAGE_PER_SECOND` to calculate average throughput in kbps

[Line 132](https://github.com/sns3/sns3-satellite/blob/0fc2b8c7/stats/satellite-stats-throughput-helper.cc#L132)
```
m_terminalCollectors.SetType("ns3::ScalarCollector");  
m_terminalCollectors.SetAttribute("InputDataType",  
                                  EnumValue(ScalarCollector::INPUT_DATA_TYPE_DOUBLE));  
m_terminalCollectors.SetAttribute(  
    "OutputType",  
    EnumValue(ScalarCollector::OUTPUT_TYPE_AVERAGE_PER_SECOND));
```

- For **scatter output** : Uses `IntervalRateCollector` for time-series throughput data

[Line 163](https://github.com/sns3/sns3-satellite/blob/0fc2b8c7/stats/satellite-stats-throughput-helper.cc#L163)
```
 m_terminalCollectors.SetType("ns3::IntervalRateCollector");
        m_terminalCollectors.SetAttribute("InputDataType",
                                          EnumValue(IntervalRateCollector::INPUT_DATA_TYPE_DOUBLE));
        CreateCollectorPerIdentifier(m_terminalCollectors);
```


## **calculate SINR**
[satellite-phy-rx-carrier.cc](https://github.com/sns3/sns3-satellite/blob/0fc2b8c7/model/satellite-phy-rx-carrier.cc#L631-L655)

### SINR 的基本計算公式為：

```
double sinr = rxPowerW / (ifPowerW + rxNoisePowerW + rxAciIfPowerW + rxExtNoisePowerW);
```

[Line 631](https://github.com/sns3/sns3-satellite/blob/0fc2b8c7/model/satellite-phy-rx-carrier.cc#L631)

`rxPowerW` : 接收到的訊號功率（單位：瓦特）
`ifPowerW` : 同頻幹擾功率（單位：瓦特）
`rxNoisePowerW` : 熱雜訊功率（單位：瓦特）
`rxAciIfPowerW` : 鄰道幹擾功率（單位：瓦特）
`rxExtNoisePowerW` : 外部噪音功率（單位：瓦特）

### Composite SINR Calculation (複合SINR)

For transparent satellite links, SNS3 computes a composite SINR that combines uplink and downlink SINR values

[Line 652](https://github.com/sns3/sns3-satellite/blob/0fc2b8c7/model/satellite-phy-rx-carrier.cc#L652)

```
double finalSinr = (1 / (1 / sinr + 1 / additionalInterference));
```

### Per-Slot Reception
[satellite-phy-rx-carrier-per-slot.cc](https://github.com/sns3/sns3-satellite/blob/0fc2b8c7/model/satellite-phy-rx-carrier-per-slot.cc)

In `SatPhyRxCarrierPerSlot`, SINR is calculated for each received slot(依照時槽):

[Line 274](https://github.com/sns3/sns3-satellite/blob/0fc2b8c7/model/satellite-phy-rx-carrier-per-slot.cc#L274)
```
double sinr = CalculateSinr(packetRxParams.rxParams->m_rxPower_W,  
                            packetRxParams.rxParams->GetInterferencePower(),  
                            m_rxNoisePowerW,  
                            m_rxAciIfPowerW,  
                            m_rxExtNoisePowerW,  
                            m_additionalInterferenceCallback());
```


**TRANSPARENT Mode**: Uses composite SINR from both uplink and downlink
**REGENERATION_PHY/LINK/NETWORK** : Uses only the current link's SINR

The system also provides methods for **calculating composite SINR between two links** and **finding the worst SINR for link budget analysis**

[satellite-phy-rx-carrier.h  Line 495](https://github.com/sns3/sns3-satellite/blob/0fc2b8c7/model/satellite-phy-rx-carrier.h#L495)

```
     * \brief Function for calculating the composite SINR
     * \param sinr1 SINR 1
     * \param sinr2 SINR 2
     * \return Composite SINR
     */
    double CalculateCompositeSinr(double sinr1, double sinr2);
    /**
     * \brief Function for calculating the worst sinr between uplink and downlink
     * \param sinr1 SINR 1
     * \param sinr2 SINR 2
     * \return Worst SINR
     */
    double GetWorstSinr(double sinr1, double sinr2);
```
