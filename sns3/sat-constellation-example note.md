# sat-constellation-example.cc
> refrence : https://github.com/sns3/sns3-satellite/blob/master/examples/sat-constellation-example.cc

# Table of Contents 
- [Table of Contents](#table-of-contents)
- [Step](#step)
- [修改LEO環境](#修改leo環境)

## Step
```
cd ~/workspace/bake/source/ns-3.43
./ns3 run "sat-constellation-example --PrintHelp"
```
### Output


| 參數名稱 | 預設值      | 參數用途說明 |
| ---| ---| ---|
| `packetSize`            | `512`| Size of constant packet (bytes) |
| `--interval`         | `20ms`     | Interval to sent packets in seconds (e.g. (1s))                               |
| `--scenarioFolder`          | `constellation-eutelsat-geo-2-sats-isls`     | Scenario folder (e.g. constellation-eutelsat-geo-2-sats-isls) [constellation-leo-2-satellites]     |
| ` --OutputPath`     |  未指定 | 指定輸出統計檔案的資料夾   |


## 修改LEO環境

*若要使用LEO環境，可以透過程式碼修改，也可以透過參數修改*

[code](https://github.com/sns3/sns3-satellite/blob/master/examples/sat-constellation-example.cc) Line 48 將

```
std::string scenarioFolder = "constellation-eutelsat-geo-2-sats-isls";
```
修改為
```
std::string scenarioFolder = "constellation-leo-2-satellites";
```

> Refrence : https://github.com/sns3/sns3-data/tree/master/scenarios

---

### 執行程式碼
```
mkdir -p results/leocon-exp1                                      
./ns3 run sat-constellation-example -- --OutputPath=results/leocon-exp1
```
### Output

### 1.SATs
```
Satellites                                          # 模擬中的衛星列表
  SAT: ID = 0, at 4.39344,113.854,415387            # 衛星ID = 0。座標 (緯度 ,經度 ,高度)
    Devices to ground stations                      # 衛星上連接地面站的通訊設備
      02-06-00:00:00:00:00:01                       # 該通訊介面的 MAC 位址
        Feeder at 02-06-00:00:00:00:00:03, beam 30  # Feeder Terminal 在位址結尾為 03 ，使用beam 30
        Feeder at 02-06-00:00:00:00:00:06, beam 43  # Feeder Terminal 在位址結尾為 06 ，使用beam 43
      Feeder connected to                           # 連接 Feeder Link 的節點
        00:00:00:00:00:05                           # 地面節點 MAC 位址
        00:00:00:00:00:08
        00:00:00:00:00:11
        00:00:00:00:00:1f
        User at 02-06-00:00:00:00:00:04, beam 30    # User Terminal 在位址結尾為 04，使用 beam 30
        User at 02-06-00:00:00:00:00:07, beam 43    # User Terminal 在位址結尾為 07，使用 beam 43
      User connected to                             # Users linked to downstream devices
    ISLs                                            # Inter-Satellite Links：星際鏈路（衛星與衛星之間的通訊）
      02-06-00:00:00:00:00:2b to SAT 1              # 透過 MAC 結尾為 2b 的介面，與 SAT 1 進行連線
```
```
  SAT: ID = 1, at 17.3625,124.686,413074            # 衛星ID = 0。座標 (緯度 ,經度 ,高度)
    Devices to ground stations                      # 衛星上連接地面站的通訊設備 
      02-06-00:00:00:00:00:02                       # 該通訊介面的 MAC 位址
        Feeder at 02-06-00:00:00:00:00:0f, beam 30  # Feeder Terminal 在位址結尾為 0f ，使用beam 30
        Feeder at 02-06-00:00:00:00:00:1d, beam 43  # Feeder Terminal 在位址結尾為 1d ，使用beam 43
      Feeder connected to                           # 連接 Feeder Link 的節點
        User at 02-06-00:00:00:00:00:10, beam 30    # beam 30 覆蓋的用戶 :10
        User at 02-06-00:00:00:00:00:1e, beam 43    # beam 43 覆蓋的用戶 :1e
      User connected to                             # 連接到 SAT 1 User Link的終端設備 MAC 列表。
        00:00:00:00:00:12                           
        00:00:00:00:00:13
        00:00:00:00:00:20
        00:00:00:00:00:21
        00:00:00:00:00:22
    ISLs                                            # Inter-Satellite Links：星際鏈路（衛星與衛星之間的通訊） 
      02-06-00:00:00:00:00:2c to SAT 0              # 透過 MAC 結尾為 2c 的介面，與 SAT 0 進行連線
```
### 2.GWs
```
GW: ID = 2, at 17.69,101.62,0
  Devices 
    02-06-00:00:00:00:00:08, sat: 0, beam: 43
    02-06-00:00:00:00:00:1f, sat: 1, beam: 43
  GW: ID = 3, at 15.93,96.54,-9.31323e-10
  Devices 
    02-06-00:00:00:00:00:05, sat: 0, beam: 30
    02-06-00:00:00:00:00:11, sat: 1, beam: 30
```

### 3.UTs
```
UT: ID = 4, at 20,110,0
  Devices 
    02-06-00:00:00:00:00:12, sat: 1, beam: 30. Linked to GW 02-06-00:00:00:00:00:05
  UT: ID = 5, at 21,111,0
  Devices 
    02-06-00:00:00:00:00:13, sat: 1, beam: 30. Linked to GW 02-06-00:00:00:00:00:05
  UT: ID = 10, at 18,115,0
  Devices 
    02-06-00:00:00:00:00:20, sat: 1, beam: 43. Linked to GW 02-06-00:00:00:00:00:08
  UT: ID = 11, at 19,110,9.31323e-10
  Devices 
    02-06-00:00:00:00:00:21, sat: 1, beam: 43. Linked to GW 02-06-00:00:00:00:00:08
  UT: ID = 12, at 15,120,0
  Devices 
    02-06-00:00:00:00:00:22, sat: 1, beam: 43. Linked to GW 02-06-00:00:00:00:00:08
```

### 4.GW users & UT users
```
GW users
  GW user: ID = 20
  GW user: ID = 21
  GW user: ID = 22
UT users
  GW user: ID = 6
  GW user: ID = 7
  GW user: ID = 8
  GW user: ID = 9
  GW user: ID = 13
  GW user: ID = 14
  GW user: ID = 15
  GW user: ID = 16
  GW user: ID = 17
  GW user: ID = 18
```


### 察看結果檔
```
cd ~/workspace/bake/source/ns-3.43/results/leocon-exp1
ls
```
<img width="1464" height="820" alt="image" src="https://github.com/user-attachments/assets/8db817e0-88de-47d0-9882-c3187e3decad" />


<img width="647" height="569" alt="image" src="https://github.com/user-attachments/assets/0a105803-2885-4fcd-96d2-e6efe8abe221" />

