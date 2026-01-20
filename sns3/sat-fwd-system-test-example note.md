# sat-fwd-system-test-example.cc
> refrence : https://github.com/sns3/sns3-satellite/blob/master/examples/sat-fwd-system-test-example.cc

# Table of Contents 
- [Table of Contents](#table-of-contents)
- [Step](#step)
- [BBframe 結構](#bbframe-結構)
  - [BBHEADER](#bbheader)
  - [BBframe Tx](#bbframe-tx)
  - [BBFrame Merge](#bbframe-merge)


## Step 
```
cd ~/workspace/bake/source/ns-3.43
./ns3 run "sat-fwd-system-test-example.cc --PrintHelp"
```
### Output
<img width="1038" height="251" alt="image" src="https://github.com/user-attachments/assets/f6d5fd46-e9d3-444e-bf6f-0e05703ec906" />


| 參數名稱 | 預設值      | 參數用途說明 |
| ---| ---| ---|
| `--testCase`            | `0`| 指定要執行的測試案例（Test case）。<br>• `0`：啟用 scheduler、**ACM 關閉**<br>• `1`：啟用 scheduler、**ACM 開啟**<br>• `2`：**ACM 模式**，僅模擬 **單一 UT（User Terminal）** |
| `--gwEndUsers`         | `10`     | Gateway（GW）端所連接的 **終端使用者數量**。<br>在 FWD 中，代表 GW 同時向多少個使用者發送資料流量。                                 |
| `--simLength`          | `40`     | 模擬總時間長度（通常單位為 **秒**）。<br>控制整個 ns-3 模擬執行多久。                                                              |
| `--traceFrameInfo`     | `false`  | 是否輸出 **BBFRAME（Baseband Frame）詳細資訊**。<br>• `true`：列印每個 BB frame 的資訊（用於除錯與分析）<br>• `false`：不輸出      |
| `--traceMergeInfo`     | `false`  | 是否輸出 **BBFRAME 合併（merge）相關資訊**。<br>通常用於觀察多個流量如何被合併進同一個 BB frame。                                  |
| `--beamId`             | `26`     | 指定使用的 **Beam ID**。<br>代表 Forward Link 中 GW 對哪一個衛星波束（beam）進行傳輸。                                            |
| `--trafficModel`       | `cbr`    | 設定流量模型（Traffic Model）。<br>• `cbr`：固定速率流量（Constant Bit Rate）<br>• `onoff`：間歇式流量（On/Off）                   |
| `--senderAppStartTime` | `+100ms` | 傳送端應用程式的 **啟動時間**。<br>避免模擬一開始系統尚未穩定就立刻送資料。                                                        |

```
./ns3 run sat-fwd-system-test-example -- --testCase=0 --gwEndUsers=2 --simLength=4 --traceFrameInfo=false --traceMergeInfo=false --beamId=26 --trafficModel=cbr --senderAppStartTime=100ms 
```
### Output
<img width="212" height="263" alt="image" src="https://github.com/user-attachments/assets/63cf4d64-809c-4a32-860f-937feeec4e7a" />

**主要內容在BBframe，因為將**
`--traceFrameInfo`  `--traceMergeInfo`
**設為false，所以才沒有輸出內容**

---

## BBframe（BaseBand Frame）
**BBFrame（BaseBand Frame）**  : 
- 是來自 DVB-S2 / DVB-S2X 標準，屬於 **實體層（Physical Layer）** 中，基頻處理的資料單位。
- 所有上層封包（IP / MPEG / GSE）都必須先被封裝進 BBFrame，才能形成**PLFRAME** 經調變後送上衛星載波。
- 通道編碼（channel coding）之前的資料格式，用來承載上層輸入資料。

### BBFrame 結構
<img width="735" height="253" alt="image" src="https://github.com/user-attachments/assets/a96f9e97-2299-4a56-a429-6e4993734a22" />

> Refrence : [ETSI EN 302 307-1](https://www.etsi.org/deliver/etsi_en/302300_302399/30230701/01.04.01_20/en_30230701v010401a.pdf) 5.2

BBFrame 包含三個主要部分：
- [BBHEADER](#bbheader)（固定長度 80 bits）
- DATA FIELD（payload，可變長度，由DFL決定)
- Padding
  - 當 DATA FIELD 沒有填滿可用容量時，用 padding bits 補齊至𝐾𝑏𝑐ℎ長度。
  - 內容為「全 0 bits」 [ETSI EN 302 307-1](https://www.etsi.org/deliver/etsi_en/302300_302399/30230701/01.04.01_20/en_30230701v010401a.pdf) 5.2.1
  
此模擬器𝐾𝑏𝑐ℎ = 32208 bits​ (4026 bytes)

## BBHEADER
<img width="889" height="279" alt="image" src="https://github.com/user-attachments/assets/c8dbf7af-308b-4f92-95be-87390d255076" />

>Refrence :
>
> [ETSI EN 302 307-1](https://www.etsi.org/deliver/etsi_en/302300_302399/30230701/01.04.01_20/en_30230701v010401a.pdf) 5.1.5
>
>[ETSI EN 302 307-1](https://www.etsi.org/deliver/etsi_en/302300_302399/30230701/01.04.01_20/en_30230701v010401a.pdf) 5.1.6

### MATYPE（2 bytes）: BBFRAME 承載資料流的種類

### First Byte (MATYPE-1)
| 欄位 | 位元數 | 設定值 | 功能說明|
| --- | ---| ---| ---|
| **TS / GS**   | 2 bits | **TS**       | **Transport Stream Input**：典型為 **MPEG-TS（188 bytes/packet）**，常用於廣播 / 電視傳輸，所有規格已定義好。|
|               |        | **GS**       | **Generic Stream Input**：泛用資料流（封包化或連續位元流），常用於 **IP / 資料服務**。|
|               |        |              |讓接收端知道 DATA FIELD 裡的**內容格式**，接收端才能用正確方式切封包、找同步、解封裝。              |
| **SIS / MIS** | 1 bit  | **SIS**      | **Single Input Stream**：僅一條輸入流（單一 service / 單一 stream）。                                                       |
|               |        | **MIS**      | **Multiple Input Stream**：多條輸入流（多 service / 多 stream）共用同一 DVB-S2 系統。                                      |
| **CCM / ACM** | 1 bit  | **CCM**      | **Constant Coding and Modulation**：調變與編碼固定（MODCOD 固定）。                                                         |
|               |        | **ACM**      | **Adaptive Coding and Modulation**：可依通道狀況調整 MODCOD。                                         |
| **ISSYI**     | 1 bit  | 0 / 1        | **Input Stream Synchronization Indicator**：<br>ISSYI=1 時，會在 User Packets 後插入 **ISSY field**（見 Annex D）<br>表示是否啟用**Input Stream Synchronization（輸入流同步）** 機制。<br>讓接收端做 **時序/同步相關處理**(更精準的時間對齊、同步恢復)。|
| **NPD**       | 1 bit  | 0 / 1        | **Null-packet Deletion**：<br>在 **TS（MPEG-TS）** 系統中，NPD=1 表示送端會刪除 **Null packet（PID 0x1FFF）**，以節省衛星資源（ACM 情境常見）。<br> 用於提升頻譜效率，避免傳送**純填充用**的封包。      |
| **RO**        | 2 bits | 00 / 01 / 10 | **Roll-off factor (α)**：成形濾波（如 Root-Raised-Cosine）參數，影響訊號頻寬與頻譜形狀；DVB-S2 常見值如 **0.35 / 0.25 / 0.20**。<br>用於讓接收端知道傳輸用的頻譜成形參數，以便正確匹配接收濾波與解調。                 |

### Second Byte(MATYPE-2)
| MATYPE-1型態                     | MATYPE-2 內容                      | 說明                                        |
| ------------------------------ | -------------------------------- | ----------------------------------------- |
| **SIS（Single Input Stream）**   | Reserved(保留)                       單一輸入流時，MATYPE-2 保留不用。                     |
| **MIS（Multiple Input Stream）** | **ISI（Input Stream Identifier）** | 指示此 BBFRAME 屬於哪一條輸入流，讓接收端在多輸入流情境下正確分流與解碼。 |

<img width="912" height="179" alt="image" src="https://github.com/user-attachments/assets/f019f278-8db3-4a0e-b1c3-b8d12c864317" />

> Refrence : [ETSI EN 302 307-1](https://www.etsi.org/deliver/etsi_en/302300_302399/30230701/01.04.01_20/en_30230701v010401a.pdf) 5.1.6

### UPL（User Packet Length，2 bytes)
- DATA FIELD 中，每個 user packet 的長度（bits）,in the range 0 to 65 535. 
  - **固定長度封包**（例如 MPEG-TS）：UPL 固定
- SNS-3 會依據 packet size + UPL 規則，把封包一個一個放進 BBFRAME
- Example 1: 0000 (HEX) = continuous stream. (連續位元流，沒有「封包邊界」)
- Example 2: 000A (HEX) = UP length of 10 bits.
- Example 3: UPL = 188×8_D for **MPEG** transport stream packets (每個封包188 Bytes)(已定義好的規格)。  


### DFL（Data Field Length，2 bytes）
- DATA FIELD 的**有效長度**（bits）
  - 不包含 BBHEADER
  - 不包含 padding
- 告訴接收端：「後面有多少 bits 是有效資料」。
- Example 4: 000A (HEX) = Data Field length of 10 bits(有效資料10bits).

### SYNC (1 byte)
- for packetized Transport or Generic Streams : copy of the User Packet Sync byte.
- 用於封包對齊，讓接收端知道「封包的起始點」。
- Example 5: SYNC = 47 (HEX) for **MPEG** transport stream packets(已定義好的規格).
- Example 6: SYNC = 00 (HEX) 代表封包化 **generic stream**的輸入本身沒有 sync-byte，sync-byte 視為 0。

### SYNCD （Sync Distance，2 bytes）
- for packetized Transport or Generic Streams: distance in bits from the beginning of the DATA FIELD and the first UP from this frame(first bit of the CRC-8).
- SYNCD = 65535_D means that no UP starts in the DATA FIELD;
- for **Continuous Generic Streams** : SYNCD= 0000 - FFFF reserved for future uses.
- 從 DATA FIELD 開始，到下一個 UP 的 CRC-8 第一個 bit 的距離（bits）
- 接收端可以快速定位封包邊界。

### CRC-8 (1 byte)
- error detection code(錯誤檢驗碼) applied to the first 9 bytes of the BBHEADER
- 對 BBHEADER 本身做 CRC-8 檢查，確保 header 沒被錯誤接收。




### BBHEADER表格 : 
<img width="1040" height="287" alt="image" src="https://github.com/user-attachments/assets/50ba0809-9a9f-4b87-bca7-26e492fc3cf9" />

> Refrence : [ETSI EN 302 307-1](https://www.etsi.org/deliver/etsi_en/302300_302399/30230701/01.04.01_20/en_30230701v010401a.pdf) 5.1.6

---

`[BBFrameTx] Frame Type: NORMAL_FRAME, ModCod: QPSK_1_TO_2` 代表Scheduler : 

- 從 GW 的佇列中拿出多個 packet
- 依照 ModCod 計算可用空間
- 把 packet 塞進 BBFrame (盡量塞滿)
- 把這個 BBFrame 丟到 PHY 層傳送出去

<img width="316" height="376" alt="image" src="https://github.com/user-attachments/assets/fb03e5aa-b453-442b-b4ef-57c59c3a8ccc" />

`Frame Type: DUMMY_FRAME` : 代表此時還未有資料要送，但PHY 時序仍需維持

### BBFrame Tx
```
--traceFrameInfo=true --traceMergeInfo=false
```
`PrintBbFrameInfo()` 會在每次 BBFrame 傳送時，印出：
- 時間
- Frame type
- ModCod
- Occupancy (佔用的空間%)、Duration (固定`+3.19507e+06 ns`)
- Space used/left (固定4050)
- BBFrame 裡每個 payload packet 的 接收端地址（DestAddress）

BBframe Tx會將每一個封包所抵達的目的做一次紀錄，所以在Output會看到很多位址
> line 44-84

一個封包大小固定為128 Bytes

### Output
<img width="1845" height="106" alt="image" src="https://github.com/user-attachments/assets/6f7f70a5-10b4-441e-b096-d96f0b5f63f9" />




### BBFrame Merge
```
--traceFrameInfo=false --traceMergeInfo=true
```
將多個低佔用率的 BBFRAME（來自不同 UT）逐一合併進同一個正在填充的 BBFRAME（Merge To）

### Output
```
[Merge Info Begins]
Merge To   -> [BBFrameTx] Time: 3.95405, Frame Type: NORMAL_FRAME, ModCod: QPSK_1_TO_2, Occupancy: 0.00395062, Duration: +3.19507e+06ns, Space used: 16, Space Left: 4034 [Receivers: ff:ff:ff:ff:ff:ff]
Merge From <- [BBFrameTx] Time: 3.95405, Frame Type: NORMAL_FRAME, ModCod: QPSK_1_TO_2, Occupancy: 0.261481, Duration: +3.19507e+06ns, Space used: 1059, Space Left: 2991 [Receivers: 00:00:00:00:00:17, 00:00:00:00:00:18, 00:00:00:00:00:19, 00:00:00:00:00:1a, 00:00:00:00:00:1b, 00:00:00:00:00:1c, 00:00:00:00:00:1d]
[Merge Info Ends]
[Merge Info Begins]
Merge To   -> [BBFrameTx] Time: 3.95405, Frame Type: NORMAL_FRAME, ModCod: QPSK_1_TO_2, Occupancy: 0.262963, Duration: +3.19507e+06ns, Space used: 1065, Space Left: 2985 [Receivers: ff:ff:ff:ff:ff:ff, 00:00:00:00:00:17, 00:00:00:00:00:18, 00:00:00:00:00:19, 00:00:00:00:00:1a, 00:00:00:00:00:1b, 00:00:00:00:00:1c, 00:00:00:00:00:1d]
Merge From <- [BBFrameTx] Time: 3.95405, Frame Type: NORMAL_FRAME, ModCod: QPSK_1_TO_2, Occupancy: 0.0259259, Duration: +3.19507e+06ns, Space used: 105, Space Left: 3945 [Receivers: 00:00:00:00:00:17]
[Merge Info Ends]
[Merge Info Begins]
Merge To   -> [BBFrameTx] Time: 3.95405, Frame Type: NORMAL_FRAME, ModCod: QPSK_1_TO_2, Occupancy: 0.28642, Duration: +3.19507e+06ns, Space used: 1160, Space Left: 2890 [Receivers: ff:ff:ff:ff:ff:ff, 00:00:00:00:00:17, 00:00:00:00:00:18, 00:00:00:00:00:19, 00:00:00:00:00:1a, 00:00:00:00:00:1b, 00:00:00:00:00:1c, 00:00:00:00:00:1d, 00:00:00:00:00:17]
Merge From <- [BBFrameTx] Time: 3.95405, Frame Type: NORMAL_FRAME, ModCod: QPSK_1_TO_2, Occupancy: 0.0437037, Duration: +3.19507e+06ns, Space used: 177, Space Left: 3873 [Receivers: 00:00:00:00:00:18]
[Merge Info Ends]
[Merge Info Begins]
Merge To   -> [BBFrameTx] Time: 3.95405, Frame Type: NORMAL_FRAME, ModCod: QPSK_1_TO_2, Occupancy: 0.327654, Duration: +3.19507e+06ns, Space used: 1327, Space Left: 2723 [Receivers: ff:ff:ff:ff:ff:ff, 00:00:00:00:00:17, 00:00:00:00:00:18, 00:00:00:00:00:19, 00:00:00:00:00:1a, 00:00:00:00:00:1b, 00:00:00:00:00:1c, 00:00:00:00:00:1d, 00:00:00:00:00:17, 00:00:00:00:00:18]
Merge From <- [BBFrameTx] Time: 3.95405, Frame Type: NORMAL_FRAME, ModCod: QPSK_1_TO_2, Occupancy: 0.0437037, Duration: +3.19507e+06ns, Space used: 177, Space Left: 3873 [Receivers: 00:00:00:00:00:19]
[Merge Info Ends]
```
`Merge to` : 正在填充的目標 frame

`Merge From` : 被合併的來源 frame

可以看到一個BBframe將`Space used: 16`與`Space used: 1059`合併後，此BBframe大小變為`Space used: 1065`，此過程將持續到封包被送出或者空間被完全利用

而`Receivers`也會隨著Merge越加越多

所以「 一個 BBFRAME 會同時攜帶多個 UT 的資料 」

提高 frame 的 occupancy，減少 padding 浪費。

---

<!--
### CRC-8 (for UP)

- 如果 UPL = 0_D (continuous generic stream)連續位元流，CRC-8 encoder 不做任何事，直接把資料往下送。
- 如果 UPL ≠ 0_D ，資料為一連串 User Packets（UP），長度為 UPL bits，都有一個 sync-byte(如果沒有，視為0)
- CRC-8 的「**規格固定**多項式」 :
<img width="601" height="43" alt="image" src="https://github.com/user-attachments/assets/54008325-33ae-4382-8bee-ce8377091192" />

> refrence : [ETSI EN 302 307-1](https://www.etsi.org/deliver/etsi_en/302300_302399/30230701/01.04.01_20/en_30230701v010401a.pdf) 5.1.4

- CRC 算法 :
  - 把資料位元序列寫成多項式 𝑢(𝑋)
  - 乘上 𝑋^8（等於在資料後面補 8 個 0）
  - 再除以 𝑔(𝑋)
  - 餘數為CRC-8（8 個 bits） 
<img width="312" height="38" alt="image" src="https://github.com/user-attachments/assets/4f1ae6b8-a07b-4b98-8243-9abceaaa955d" />

> refrence : [ETSI EN 302 307-1](https://www.etsi.org/deliver/etsi_en/302300_302399/30230701/01.04.01_20/en_30230701v010401a.pdf) 5.1.4

- 算完CRC-8之後，會被插入在 User Packet stream 中
- 此做法在不增加額外 byte 的情況下，把 CRC 資訊串在封包之間傳遞。

<img width="906" height="304" alt="image" src="https://github.com/user-attachments/assets/1d94a697-d9db-4620-b435-aa03b4ba564f" />

> refrence : [ETSI EN 302 307-1](https://www.etsi.org/deliver/etsi_en/302300_302399/30230701/01.04.01_20/en_30230701v010401a.pdf) 5.1.4


- 實際CRC-8運算過程 :
  - 每次開始計算一段 CRC（每個 sequence/每個 UP）之前
  - CRC shift register 都要清除為 0
    
<img width="1160" height="438" alt="image" src="https://github.com/user-attachments/assets/df59f133-1dfe-46f2-a09f-67c5885b9c66" />

> refrence : [ETSI EN 302 307-1](https://www.etsi.org/deliver/etsi_en/302300_302399/30230701/01.04.01_20/en_30230701v010401a.pdf) 5.1.4

-->

