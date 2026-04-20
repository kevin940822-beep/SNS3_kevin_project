# Table of contents
- [參數](#參數)
- [環境修改](#將環境改為)
- [Output](#output)
    - [Per Beam App Throughput](#per-beam-app-throughput)
    - [Per UT App Throughput](#per-ut-app-throughput)
    - [User link SINR](#user-link-sinr)

## 參數
- UT : 3
- GW : 1
- beam ID : 1, 2, 41
- 摸擬時間 : 60s
- packetSize = 512 Bytes
- interval = 20ms



### 將環境改為

```
geo-33E-beam-hopping
```

設定beamID與beamhopping [(line 108)](https://github.com/sns3/sns3-satellite/blob/master/examples/sat-constellation-example.cc)
```
// Set beam ID
    if (scenarioFolder == "constellation-telesat-351-sats")
    {
        simulationHelper->SetBeamSet(beamSetTelesat);
    }
    else if (scenarioFolder == "geo-33E-beam-hopping") 
    {
    // 針對跳躍場景使用 GW-1 的波束 
    std::set<uint32_t> beamSetHopping = {1, 2, 41};
    simulationHelper->SetBeamSet(beamSetHopping);
    }
    else
    {
        simulationHelper->SetBeamSet(beamSet); 
    }
    
    simulationHelper->SetUserCountPerUt(2);

    //將UT改一半方便跑
    std::map<uint32_t, uint32_t> utsInBeam = {
    {41, 1},{2, 1},{2, 1}

    };

    for (const auto& it : utsInBeam)
    {
        simulationHelper->SetUtCountPerBeam(it.first, it.second);
    }


   

    LogComponentEnable("sat-constellation-example", LOG_LEVEL_INFO);
    simulationHelper->ConfigureFwdLinkBeamHopping();
    simulationHelper->CreateSatScenario();
```

## Output

### Per Beam App Throughput
|Beam|FWD|RTN|
|---|---|---|
|Beam1|<img width="1719" height="1131" alt="image" src="https://github.com/user-attachments/assets/df4e54d9-2e83-41e2-b83b-e89b197f1e75" />|<img width="1719" height="1131" alt="image" src="https://github.com/user-attachments/assets/9d281136-9ae5-470b-93b7-a0124057da04" />|
|Beam2|<img width="1719" height="1131" alt="image" src="https://github.com/user-attachments/assets/49f772f6-bf0e-433f-96d7-d9d162e0408e" />|<img width="1719" height="1131" alt="image" src="https://github.com/user-attachments/assets/bc022ae8-dca1-4923-b891-ad425a4d60a5" />|
|Beam41|<img width="1719" height="1131" alt="image" src="https://github.com/user-attachments/assets/9e4e138a-1042-422a-b9ba-11af00957e67" />|<img width="1719" height="1131" alt="image" src="https://github.com/user-attachments/assets/87684c41-180f-435d-848f-9f31a524d454" />|

### Per UT App Throughput
|UT|FWD|RTN|
|---|---|---|
|UT1|<img width="1719" height="1131" alt="image" src="https://github.com/user-attachments/assets/acbd1fe9-ce84-4a49-9eaf-c7bc1be1b62f" />|<img width="1719" height="1131" alt="image" src="https://github.com/user-attachments/assets/cae38297-6ec0-4c37-8797-d96d0b721ea6" />|
|UT2|<img width="1719" height="1131" alt="image" src="https://github.com/user-attachments/assets/21193328-d134-451a-a483-606ada2bbf03" />|<img width="1719" height="1131" alt="image" src="https://github.com/user-attachments/assets/05da3b01-d43d-4679-b81c-6613ee661269" />|
|UT3|<img width="1719" height="1131" alt="image" src="https://github.com/user-attachments/assets/982b24b7-fb44-44b6-914a-6902d483fa5d" />|<img width="1719" height="1131" alt="image" src="https://github.com/user-attachments/assets/cae69628-1788-4fc8-9cc2-024589517918" />|

### User link SINR 

|UT|FWD|RTN|
|---|---|---|
|UT1|<img width="1711" height="1132" alt="image" src="https://github.com/user-attachments/assets/792f607c-4537-445f-82ae-af9d8145d548" />|<img width="1747" height="1132" alt="image" src="https://github.com/user-attachments/assets/7d1220f2-33c8-46b8-9028-2eac8b2c505a" />|
|UT2|<img width="1711" height="1132" alt="image" src="https://github.com/user-attachments/assets/d26f90b8-fca7-4775-89b4-b17ba91309b7" />|<img width="1770" height="1132" alt="image" src="https://github.com/user-attachments/assets/abdf1819-04dc-4de3-a835-b102e29a256e" />|
|UT3|<img width="1711" height="1132" alt="image" src="https://github.com/user-attachments/assets/d336d59e-fd9e-4be7-ad26-9f17b46d7141" />|<img width="1747" height="1132" alt="image" src="https://github.com/user-attachments/assets/0ad09207-1f6d-4f3d-83d5-dad9b6e31ce5" />|
