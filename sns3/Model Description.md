### Refrence
> https://www.sns3.org/doc/satellite-design.html#model-description

# Table of Contents
- [SNS3 Design](#sns3-design)
- [Frame configuration (框架與時槽結構)](#frame-configuration-框架與時槽結構)
- [Architecture (整體架構)](#architecture-整體架構)
- [User terminal SatNetDevice](#user-terminal-satnetdevice)
  - [Additional Note (UT)](#additional-note-ut)
- [Geostationary satellite](#geostationary-satellite)
  - [Additional Note (SAT)](#additional-note-sat)
- [Gateway](#gateway)
  - [Additional Note (GW)](#additional-note-gw)


## SNS3 Design
> Refrence : https://www.sns3.org/doc/satellite-design.html#design

- **Satellite Network Simulator 3 (SNS3)** is a satellite network extension to **Network Simulator 3 (ns-3)** platform.
- **SNS3** models a full interactive multi-spot beam satellite network with a **geostationary satellite (GEO)**.
- **transparent star (bent-pipe)** payload.

- reference satellite system have **72 spot-beams with an  European coverage**.

- serverd by 5 **gateways (GWs)** and using **Ka-band frequencies**  26.5 GHz ~ 40 GHz on the feeder/user link.

- implements the **DVB-RCS2/S2** standards.
  - **DVB-RCS2 (return link) : Digital Video Broadcast - Return Channel via Satellite - 2nd generation**
  - **DVB-S2 (forword link) : Digital Video Broadcasting - Satellite - 2nd generation**

## Frame configuration (框架與時槽結構)
> Refrence : https://www.sns3.org/doc/satellite-design.html#frame-configuration

### Return Link (DVB-RCS2)
- Use **Multi-Frequency Time Division Multiple Access (MF-TDMA)**.
- Composed of:
  1. **Superframe sequences (SFS)**
  2. **Superframes**
  3. **Frames**
  4. **Time slots**
  5. **Bandwidth Time Units (BTUs)**

**Superframe sequence (SFS)** : An ordered time sequence of superframes on a given set of carriers, identifying one **MF-TDMA** resource block.  *(在一組特定頻寬上的連續 superframe 時間序列，代表一整塊 MF-TDMA 資源)*

<div align="center">
<img width="579" height="281" alt="image" src="https://github.com/user-attachments/assets/defa1270-bb8a-443e-a363-518475093956" />
</div>

<p align="center"><strong>Figure 1.</strong> Superframe sequences (SFS) </p>

<div align="center">
<img width="722" height="663" alt="image" src="https://github.com/user-attachments/assets/6f2ab7a8-6721-49d1-88e6-3d5e478d5353" />
</div>

<p align="center"><strong>Figure 2.</strong> Superframe configuration </p>

> Refrence : [EN 301 545-2 - V1.4.1](https://www.etsi.org/deliver/etsi_en/301500_301599/30154502/01.04.01_60/en_30154502v010401p.pdf) 7.5.1.2

- The used frame structures are dynamically configured by the **Network Control Center (NCC)**.
  - **Superframe Composition Table (SCT)**
  - **Frame Composition Table v2 (FCT2)**
  - **Broadcast Composition Table (BCT)**

- The satellite module does not explicitly model **SCT**, **FCT** and **BCT**.
- The frame configurations may be changed via **parametrization**.
- **NCC** models **Terminal Burst Time Plan v2 (TBTPv2)**.
  - Is capable of configuring the time slots dynamically for each **superframe** **(e.g. start times, durations, waveforms)**.

 ### Forward Link (DVB-S2)
 - Using **DVB-S2 Time Division Multiplexing (TDM)**.
 - feeder link 2 GHz bandwidth is:
   - Divided into **16 carriers**, each **125 MHz**. 
   - Each carrier is statically mapped to a user link frequency color.  *(每個 carrier 固定對應到某一個 user-beam 的 125 MHz 顏色)*
   - Only one carrier per beam is supported.    *(每個 beam 只支援 一個 carrier)*
  
## Architecture (整體架構)
<img width="1300" height="537" alt="image" src="https://github.com/user-attachments/assets/5949f5b4-03a9-46ba-9677-3c0bd824779f" />
<p align="center"><strong>Figure 3.</strong> SNS3 Architecture </p>

> Refrence : https://www.sns3.org/doc/satellite-design.html#fig-satellite-general-architecture

### Communication Channels
|Channel Name	|Description|
|---|---|
|CSMA channel | Communication between ground user and UT, simulates LAN/shared media  *(模擬地面有線／LAN 通道)*    |
|SatChannel   |	Simulates satellite uplink/downlink (UT ↔ SAT ↔ GW)                      |
|Ideal channel|	Idealized channel between GW and ground user (no interference or latency) *(無干擾、延遲)*|

- All satellite nodes require a new implementation of a ```SatNetDevice```
- ```SatNetDevice``` implement:
  - **Logical Link Control (LLC)**.
  - **Medium Access Control (MAC)**.
  - **Physical (PHY)**.
- A new implementation of the Channel class ```SatChannel```.
  - To support satellite system received signal power calculations.

- Satellite module implements both spherical and geodetic coordinate systems (**WGS80** and **GRS84**). 
  - In addition to labeling latitude, longitude, altitude of UT, GEO satellite and GW.

*(支援球面/大地座標（WGS80、GRS84），方便標示 UT / SAT / GW 的經緯度與高度)*
### **1️⃣ Left side End user（地面使用者）**
- Connection :
  - Use **CSMA channel** (Carrier Sense Multiple Access) connect to User Terminal(UT).
- Protocol Stack :
  - **Data Link Layer (CSMA)** : Ethernet link to the UT.
  - **Network Layer** : An Ip layer(IPv4 or IPv6), responsible for packet routing by IPv4.
  - **Transport Layer** : Use TCP/UDP end-to-end transport protocols, provides reliability, congestion control, ports.
  - **Application Layer** : Actual applications.
- This end user simply sends and receives IP traffic over a LAN interface. *(這個 End user 只是透過 LAN 介面收送 IP 流量)*
- It does not know that its packets will cross a satellite system.

### **2️⃣ UT (User Terminal)**
- Connection :
  - Use **SatChannel** connect to Satellite node.
- Modules :
  - ```SatNetDevice``` : Special satellite network interface towards the satellite.
  - **Network layer** :
    - An IP routing/forwarding layer that connects the **LAN side (CSMA)** to the **satellite side** (```SatNetDevice```).
    - Looks like a router: one interface to the **local LAN**, one interface to the **satellite network**.
  - ```GeoPosition``` : Record the UT’s orbital position (latitude, longitude, altitude using WGS-84/GRS-84).
    *(儲存UT的軌道位置，經度/緯度/高度)*

- The UT plays the role of a customer premises terminal. *(UT 扮演的是一個 用戶端終端設備／小型路由器 的角色)*
- It routes IP packets between the **local LAN** and the **satellite link**.

### **3️⃣ Satellite node**
- Connection :
  - Use **SatChannel** to build **uplink/downlink** with  **UT** and **GW**.
- Modules :
  - ```SatGeoNetDevice``` :
    - Device representing the **satellite’s radio payload**.    *(代表衛星上的射頻酬載。)*
    - Does not run IP or transport protocols.
    - Receives bursts from UTs/GWs on the ```SatChannel```.
    - Forwards the bursts towards the opposite direction (UT↔GW).    *(將 burst 轉發到另一方向（UT ↔ GW）)*
    - Applies propagation effects and computes received signal quality (e.g., SINR).    *(套用傳播效應，計算收到訊號的品質（例如 SINR）。)*
  - ```GeoPosition``` : Record the satellite’s orbital position (latitude, longitude, altitude using WGS-84/GRS-84).
 
 - The satellite is essentially a transparent relay at the **physical**/**MAC** level. *(做透明轉發的中繼站)*

### **4️⃣ GW (GateWay)**
- Connection :
  - Use **Ideal channel** connect to **End Users** .
- Modules :
  - `SatNetDevice` : Handles data transmission to and from the satellite.
  - `CSMA` : Communicates with end users on the ground via terrestrial networks (e.g., Wi-Fi, wired LAN).
  - `GeoPosition` : Records the gateway’s geographic coordinates.

### **5️⃣ Right side End User**
- Connection :
  - Use **Ideal channel** connect to **End Users** .
  - Representing a simplified model without interference or delay. *(無延遲無干擾的簡易模型)*
 
<!--Internally includes : *(內部包含)*
    - **Logical Link Control (LLC)** : Packs/Unpacks higher-layer packets into **bursts**. *(把上層封包打包成 **burst**、或從 **burst** 還原出封包。)*
    - **Medium Access Control (MAC)** : Obeys the satellite access rules (frames, time slots, TBTP). *(遵守衛星系統的存取規則。)*
    - **Physical (PHY)** : Transmits bursts over the ```SatChannel``` using given waveforms/MODCODs. *(用指定的 waveform / MODCOD在 ```SatChannel``` 上發射 **burst**。)* 
-->

---

## User terminal SatNetDevice

<div align="center">
<img width="465" height="566" alt="image" src="https://github.com/user-attachments/assets/9e0aa6c4-350a-400f-acdc-e68d5112cc3d" />
  <p align="center"><strong>Figure 4.</strong> UT SatNetDevice </p>
</div>

>Refrence : https://www.sns3.org/doc/satellite-design.html#user-terminal

### **UT負責** : 
- DVB-RCS2（RTN）回傳鏈路：burst transmission logic（上行 burst 發送邏輯）
- DVB-S2（FWD）前向鏈路：BB frame reception logic（下行 BBFrame 接收邏輯）

### `SatNetDevice`
- UT 對衛星網路的介面
- 內部實作 DVB-RCS2 / DVB-S2 所需的 LLC、MAC、PHY 功能

### `SatPacketClassifier`
- 封包分類
- 根據封包特徵（QoS、目的地）決定後續走哪條處理路徑 (queue / module)。

### `SatUtLlc` (Logical Link Control - LLC Layer)
- `Tx` : 
  - `SatReturnLinkEncapsulator` : 將上行 IP packets 封裝成 **RLE (Return Link Encapsulation)** 格式，for **return channel transmission**.
  - `SatQueue` : 暫存上行資料（封裝後 / 等待排程）

- `Rx`（FWD 下行）:
   - `SatGenericStreamEncapsulator` : 將前向鏈路（DVB-S2）收到的資料做解封包/重新封包。
   - `SatQueue` : 暫存下行資料(收到 / 解封裝後)。

- `SatRequestManager` 
  - 監控 queue 狀態
  - 必要時送 **CR (Capacity Request)** 給 **NCC (Network Control Center)**
  - 支援 :
    - **RBDC(Rate-Based Dynamic Capacity)** 基於速率的動態容量
    - **VBDC(Volume-Based Dynamic Capacity)** 基於容量的動態容量

queue 有資料 → **SatRequestManager** 依規則產生 CR（RBDC/VBDC）→ NCC 在 TBTP 分配上行 timeslot 給 UT

### `SatUtMac` (Media Access Control - MAC Layer)
- `SatUtScheduler` : 根據 **TBTP(Terminal Burst Time Plan)** 資訊來排程 time slots
- `SatRandomAccess` : 支援 random access mechanisms
- `SatTbtpContainer` : 保存 NCC 傳送的 TBTP
  
*(遵守衛星系統的存取規則。)*

### `SatUtPhy` (PHY Layer)
- `SatPhyTx` : 處理實體層的傳輸邏輯
- `SatPhyRx` : 處理實體層的接收邏輯
  - `SatPhyRxCarrier` : 多個 carrier 的接收處理與干擾檢查
    - `SatLinkResults` : 儲存鏈路結果（例如接收品質/是否成功等結果資料）
    - `SatInterference` : 評估接收時的干擾

### **Additional Note (UT)**

**UT使用流程**
<div align="center">
<img width="233" height="666" alt="image" src="https://github.com/user-attachments/assets/939da820-8071-4a3c-8670-217766edca38" />
    <p align="center"><strong>Figure 5.</strong> UT flowchart </p>
</div>



## Geostationary satellite

<div align="center">
<img width="721" height="395" alt="image" src="https://github.com/user-attachments/assets/27381ff5-c92c-4c55-b410-db6455205ca1" />
    <p align="center"><strong>Figure 6.</strong> SatGeoNetDevice </p>
</div>

>Refrence : https://www.sns3.org/doc/satellite-design.html#geostationary-satellite

### `SatGeoUserPhy`

- 處理 **User Link PHY layer**

- SatChannel "Rtn user" :（UT → SAT）
- SatChannel "Fwd user" :（SAT → UT）
- `Tx` : Transmitter module, 負責向 **UT（User Terminal）** 發送訊號的模組。
- `Rx` : Receiver module, 接收來自**UT（User Terminal）** 的訊號。
  - `RxC` : Receiver Control module, 用於量測接收到的使用者上行品質
  - `I` : Interference Estimator, 計算**SINR (Signal-to-Interference-plus-Noise Ratio)**.
   

### `SatGeoFeederPhy` 

- 處理**Feeder Link PHY layer**

- SatChannel "Fwd feeder" :（GW → SAT）
- SatChannel "Rtn feeder" :（SAT → GW）

- 與**SatGeoUserPhy**相同，包含`Tx`、`Rx`、`RxC`、`I`
- 接收來自**GW**的訊號或向**GW**發送訊號。

### **Additional Note (SAT)**
- **Transparent Forwarding (bent-pipe)** : 衛星不對封包進行處理，只負責放大和轉發訊號。
- **SINR Calculation** : SINR分別在 **User End**和 **Feeder Ends** 進行計算，最後在 **GW** 使用複合公式進行組合。

<div align="center">
<img width="517" height="662" alt="image" src="https://github.com/user-attachments/assets/4f107856-3f0c-4778-8e36-981e0f88c3b3" />
    <p align="center"><strong>Figure 7.</strong> RTN / FWD packet flowchart</p>
</div>

## Gateway

<div align="center">
<img width="465" height="566" alt="image" src="https://github.com/user-attachments/assets/f2492a0c-a293-4d2b-b1cc-a99ebef8a262" />
    <p align="center"><strong>Figure 8.</strong> GW SatNetDevice </p>
</div>

> Refrence : https://www.sns3.org/doc/satellite-design.html#gateway

### `SatNetDevice`
- **GW** 網路設備的容器
- 和**UT**的差別 : **GW**主要負責 FWD 發送 以及 RTN 接收

### `SatPacketClassifier`
- 將上層送下來的封包做分類
- 決定後續的路由路徑和調度策略

*GW 端的入口分流器*

### `SatGwLlc` (Logical Link Control - LLC Layer)


- `Tx` : 
  - `SatGenericStreamEncapsulator` : 把 GW 端要送往 UT 的資料，封裝成 **Generic Stream format (GSE)**.
  - `SatQueue` : 暫存已封裝的資料(Queue)，等待送出。

 - `Rx` :
   - `SatReturnLinkEncapsulator` : 把從衛星收到的 RTN 封包還原成上層封包可用的形式。
   - `SatQueue` : 暫存已解封裝/待上送的 RTN 封包。

*封裝/解封裝 + queue*

### `SatGwMac` (Media Access Control - MAC Layer)

- `SatFwdLinkScheduler` : **Forward link scheduler**, 決定要發送哪些資料以及何時發送。
- `SatBbFrameContainer` : 存放組裝好的 **BBFrame (BaseBand Frame)** 資料
  
*排程和BBFrame組裝*

### `SatGwPhy` (PHY Layer)
- `SatPhyTx` : 處理實體層的傳輸邏輯

- `SatPhyRx` : 處理實體層的接收邏輯
  - `SatPhyRxCarrier` : 多個 carrier 的接收處理與干擾檢查
    - `SatLinkResults` : 儲存鏈路結果（例如接收品質/是否成功等結果資料）
    - `SatInterference` : 評估接收時的干擾


### **Additional Note (GW)**
- `SatNetDevice` 涵蓋從分類到物理傳輸的整個協定
- **LLC 層** 透過獨立的 Tx 和 Rx 模組支援**雙向操作 (bidirectional operations)**，確保**全雙工通訊(full-duplex communication)**
- **MAC 層** 負責根據 MODCOD 方案產生/調度 BBFrames
- **PHY層** 提供發送和**多載波接收(multi-carrier reception)** ，支援：
  - **鏈路品質監測(Link quality monitoring)**
  - **干擾評估(Interference assessment)**
- 如果沒有使用者資料可用，**GW** 會產生**虛擬 BBFrame (dummy BBFrames)** 來維持同步和 **連續幀傳輸(continuous frame transmission)**
