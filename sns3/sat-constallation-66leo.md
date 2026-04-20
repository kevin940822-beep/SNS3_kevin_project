# Table of Content
- [參數](#參數)
- [Step](#step)



## 參數介紹 
- 由 6 個軌道面，每個平面有 11 顆衛星 = 66顆衛星
- 5 GWs
- 100 UTs
- simulation time : 30 sec


## Step

### 1️⃣ Edit tles.txt

go to `scenarios/constellation-iridium-next-66-sats/positions/tles.txt`

Change 6 11 to 66 in the first line.

```
6 11 --> 66
iridium-75 0
1 00001U 00000ABC 00001.00000000  .00000000  00000-0  00000+0 0    04
2 00001  86.4000   0.0000 0000001   0.0000   0.0000 14.80000000    05
iridium-75 1
```

### 2️⃣ Change scenario

[sat-constellation-example.cc Line 48](https://github.com/sns3/sns3-satellite/blob/0fc2b8c74f0d9c2b0c3ee4ed132064a40ad2daf1/examples/sat-constellation-example.cc#L48)
```
    std::string scenarioFolder = "constellation-eutelsat-geo-2-sats-isls";

--> std::string scenarioFolder = "constellation-iridium-next-66-sats";
```
