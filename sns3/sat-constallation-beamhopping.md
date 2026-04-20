將環境改為

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
    {1, 15},
                                              {2, 4},
                                              {3, 7},
                                              {4, 15},
                                              {11, 7},
                                              {12, 15},
                                              {13, 4},
                                              {14, 9},
                                              {25, 4},
                                              {26, 7},
                                              {27, 9},
                                              {28, 15},
                                              {40, 4},
                                              {41, 7}
    };

    for (const auto& it : utsInBeam)
    {
        simulationHelper->SetUtCountPerBeam(it.first, it.second);
    }


   

    LogComponentEnable("sat-constellation-example", LOG_LEVEL_INFO);
    simulationHelper->ConfigureFwdLinkBeamHopping();
    simulationHelper->CreateSatScenario();
```


