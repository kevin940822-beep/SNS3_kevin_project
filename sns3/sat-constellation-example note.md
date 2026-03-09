# sat-constellation-example.cc
> refrence : https://github.com/sns3/sns3-satellite/blob/master/examples/sat-constellation-example.cc

# Table of Contents 
- [Step](#step)
- [修改LEO環境](#修改leo環境)
- [LEO vs GEO](#leo-vs-geo)
- [flowchart](#flowchart)

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
        User at 02-06-00:00:00:00:00:10, beam 30    # User Terminal 在位址結尾為 10，使用 beam 30
        User at 02-06-00:00:00:00:00:1e, beam 43    # User Terminal 在位址結尾為 1e，使用 beam 43
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
GWs                                                 # Ground stations (Gateways) 資訊
  GW: ID = 2, at 17.69,101.62,0                     # Gateway with ID=2，座標 (緯度, 經度 ,高度)
  Devices 
    02-06-00:00:00:00:00:08, sat: 0, beam: 43       # 介面 MAC 位址為 08。目前正與 SAT0 透過 beam 43 連接。
    02-06-00:00:00:00:00:1f, sat: 1, beam: 43       # 介面 MAC 位址為 1f。目前正與 SAT1 透過 beam 43 連接。
  GW: ID = 3, at 15.93,96.54,-9.31323e-10           # Gateway with ID=3，座標 (緯度, 經度 ,高度)
  Devices 
    02-06-00:00:00:00:00:05, sat: 0, beam: 30       # 介面 MAC 位址為 05。目前正與 SAT0 透過 beam 30 連接。
    02-06-00:00:00:00:00:11, sat: 1, beam: 30       # 介面 MAC 位址為 11。目前正與 SAT1 透過 beam 30 連接。
```

### 3.UTs
```
UTs                                                    # User Terminals (UT) 資訊
  UT: ID = 4, at 20,110,0                              # User Terminal with ID=4，座標 (緯度, 經度 ,高度)
  Devices 
    02-06-00:00:00:00:00:12, sat: 1, beam: 30. Linked to GW 02-06-00:00:00:00:00:05    # UT MAC 位址為 :12，透過beam 30 連線至 SAT 1 。連線到 GW 3 (MAC :05)
  UT: ID = 5, at 21,111,0                              # User Terminal with ID=5，座標 (緯度, 經度 ,高度)
  Devices 
    02-06-00:00:00:00:00:13, sat: 1, beam: 30. Linked to GW 02-06-00:00:00:00:00:05    # UT MAC 位址為 :13，透過beam 30 連線至 SAT 1 。連線到 GW 3 (MAC :05)
  UT: ID = 10, at 18,115,0                             # User Terminal with ID=10，座標 (緯度, 經度 ,高度)
  Devices 
    02-06-00:00:00:00:00:20, sat: 1, beam: 43. Linked to GW 02-06-00:00:00:00:00:08    # UT MAC 位址為 :20，透過beam 43 連線至 SAT 1 。連線到 GW 2 (MAC :08)
  UT: ID = 11, at 19,110,9.31323e-10                   # User Terminal with ID=11，座標 (緯度, 經度 ,高度)
  Devices 
    02-06-00:00:00:00:00:21, sat: 1, beam: 43. Linked to GW 02-06-00:00:00:00:00:08    # UT MAC 位址為 :21，透過beam 43 連線至 SAT 1 。連線到 GW 2 (MAC :08)
  UT: ID = 12, at 15,120,0                             # User Terminal with ID=12，座標 (緯度, 經度 ,高度)
  Devices 
    02-06-00:00:00:00:00:22, sat: 1, beam: 43. Linked to GW 02-06-00:00:00:00:00:08    # UT MAC 位址為 :22，透過beam 43 連線至 SAT 1 。連線到 GW 2 (MAC :08)
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
- GW users (IDs 20-22) → 代表GW的終端主機應用程式。
- UT users (IDs 6-18) →  代表UT的終端主機應用程式。
- 這些 ID 並非實體設備，而是**邏輯應用程式節點(logical application nodes)**。

### SatIdMapper GW/UT MAC to satellite ID map
```
00:00:00:00:00:05 0
00:00:00:00:00:08 0
00:00:00:00:00:11 0
00:00:00:00:00:12 1
00:00:00:00:00:13 1
00:00:00:00:00:1f 0
00:00:00:00:00:20 1
00:00:00:00:00:21 1
00:00:00:00:00:22 1
```

<img width="1623" height="756" alt="image" src="https://github.com/user-attachments/assets/70d48b54-7873-4759-bb03-5a578a015b70" />






### 察看結果檔
```
cd ~/workspace/bake/source/ns-3.43/results/leocon-exp1
ls
```

## LEO vs GEO
|item|LEO|GEO|
|---|---|---|
|fwd throughput|<img width="648" height="569" alt="image" src="https://github.com/user-attachments/assets/ebcb9a0e-057f-4626-a206-c134a87ac18f" />|<img width="646" height="568" alt="image" src="https://github.com/user-attachments/assets/f5fb3c01-e9bc-4844-bcbc-e2b6c3cb65b7" />|
|rtn throughput|<img width="644" height="568" alt="image" src="https://github.com/user-attachments/assets/3f1bd68d-d35a-4833-9721-8c216740e34f" />|<img width="639" height="563" alt="image" src="https://github.com/user-attachments/assets/90cc48d4-aae5-484b-8c5c-fbd5c7648fb5" />|


- fwd 與 rtn throughput 不一樣？
  - 衛星為 **Regeneration Mode** 
  - 兩者的 **編碼調變（MODCOD）** 不同
  - 開啟 **ACM**，會獨立調整各自的傳輸速率。
- 受到**ISL固定頻寬**影響。(此模擬為100Mb/s)
- **LEO fwd**在75秒時連線中斷。(衛星飛出通訊範圍)


## ACM off ISL 500Mb/s
測試ACM對模擬的影響

以及將ISL改為500Mb/s

看是否可以得到三角形輸出曲線
- ISL 500Mb/s
- ACM disable
- 模擬時間120s

|item|LEO|GEO|
|---|---|---|
|fwd throughput||<img width="640" height="567" alt="image" src="https://github.com/user-attachments/assets/2b72c2c8-2ba1-4ba0-a491-4143c2b63d01" />|
|rtn throughput||<img width="641" height="563" alt="image" src="https://github.com/user-attachments/assets/7a57fdfb-73c7-4758-8e58-6497c4ba6ccc" />|



## flowchart
<img width="566" height="741" alt="image" src="https://github.com/user-attachments/assets/977e9fdd-e90f-4ffc-b2d0-c50a87307858" />


# 試著將LEO環境換成constellation-telesat-351-sats
