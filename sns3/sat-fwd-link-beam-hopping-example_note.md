# sns3 sat-fwd-link-beam-hopping-example.cc
> Refrence
> https://github.com/sns3/sns3-satellite/blob/master/examples/sat-fwd-link-beam-hopping-example.cc

# Table of contents
- [預設參數](#預設參數)
- [Step](#step)
  - [重要檔案](#重要檔案)
  - [Feeder link v.s. User link](#feeder-link-vs-user-link)
  - [SINR（Signal to Interference plus Noise Ratio）](#sinrsignal-to-interference-plus-noise-ratio)
- [Beam Hopping vs Static Beam](#beam-hopping-vs-static-beam)
- [一個 beam 的 UT 數量](#一個beam-的-ut數量)
- [superframe定義](#superframe定義)


## 預設參數
- UT : 252
- beam : 14
- duration time : 10ms

## Step  
```
cd ~/workspace/bake/source/ns-3.43
./ns3 run "sat-fwd-link-beam-hopping-example --PrintHelp"
```
*We need to move to path ns-3.43 so that we can run the code*

*but the code path at*  
```
cd ~/workspace/bake/source/ns-3.43/contrib/satellite/examples
```

### Output : 
<img width="1157" height="468" alt="image" src="https://github.com/user-attachments/assets/bcbeaedf-bf25-484c-a1ea-dc26acec9511" />

| 參數名稱           | 功能說明                                                | 預設值        | 修改範例                               |
| -------------- | --------------------------------------------------- | ---------- | ---------------------------------- |
| `--simTime`    | **模擬時間長度（秒）**。決定整個衛星系統跑多久                           | `3`（秒）     | `--simTime=10`<br>（模擬 10 秒）        |
| `--scaleDown`  | 是否**縮小 Forward Link 的頻寬**，方便在低流量下觀察 beam hopping 差異 | `true`     | `--scaleDown=0`<br>(關閉縮放，使用原始較大頻寬) |
| `--OutputPath` | **輸出統計結果的資料夾路徑**                                    | 未指定（用預設路徑） | `--OutputPath=./bh-test1`            |

### 修改參數並執行
```
mkdir -p results/bh-test1
./ns3 run "sat-fwd-link-beam-hopping-example --simTime=3 --scaleDown=1 --OutputPath=results/bh-test1"
```
*At fIrst time runinng, we can use less beam, to ensure the system run correctly*

*Cause beam hopping will cost some time*

### Output

<img width="173" height="363" alt="image" src="https://github.com/user-attachments/assets/da14ab8f-f044-43a5-be4d-0741bf1080a1" />

### 輸出結果解釋 : 
`0.2` 代表 目前模擬時間已跑到 0.2 秒，`/3` 代表總模擬時間是 3 秒

---
### 檢視結果檔
```
cd ~/workspace/bake/source/ns-3.43/results/bh-test1
ls
```
### Output

<img width="993" height="550" alt="image" src="https://github.com/user-attachments/assets/5c5fc1f7-6271-48fc-8a29-dafdb01758a5" />

## 重要檔案
### （A）Global（整個系統，只有 1 組）
| 檔案名稱                                                  | 層級     | 指標             | 實際作用                                       |
| ----------------------------------------------------- | ------ | -------------- | ------------------------------------------ |
| `stat-global-fwd-app-throughput-scalar.txt`           | Global | App Throughput | **整個系統的平均 Forward Link 吞吐量**               |
| `stat-global-fwd-app-throughput-scatter-0.txt`        | Global | App Throughput | 隨時間變化的吞吐量                             |
| `stat-global-fwd-app-delay-cdf-0-ATTN.txt`            | Global | App Delay      | **Forward link end-to-end delay 的 CDF 分佈** |
| `stat-global-fwd-composite-sinr-cdf-0-ATTN.txt`       | Global | **SINR**           | **整體 Forward link 的 SINR 分佈**              |
| `stat-global-fwd-feeder-mac-throughput-scalar.txt`    | Global | MAC Throughput | Feeder link MAC 層的平均吞吐量                    |
| `stat-global-fwd-feeder-mac-throughput-scatter-0.txt` | Global | MAC Throughput | Feeder link MAC 隨時間變化的吞吐量                    |
| `stat-global-fwd-user-mac-throughput-scalar.txt`      | Global | MAC Throughput | User link MAC 層平均吞吐量                       |
| `stat-global-fwd-user-mac-throughput-scatter-0.txt`   | Global | MAC Throughput | User link MAC 隨時間變化的吞吐量                       |

## Feeder link v.s. User link

| 項目              | Feeder link MAC                | User link MAC             |
| --------------- | ------------------------------ | ------------------------- |
| 位置              | Gateway ↔ Satellite            | Satellite ↔ UT            |
| 層級              | 核心 / 回程                        | 使用者存取                     |
| 服務對象            | 多個 beam / 多個 UT 的總計流量 | 單一 UT                     |
| 頻寬              | 通常較大                           | 較小、共享                     |
| Link quality    | 穩定                             | 易受 **SINR** / beam hopping 影響 |
| 是否容易成為瓶頸        | ❌ 較少                           | ✅ **非常容易**                |
| Beam hopping 影響 | 間接                             | **直接影響**                  |
| 對應檔案          | `fwd-feeder-mac-*`             | `fwd-user-mac-*`          |

### 查看feeder mac throughput 
```
less stat-global-fwd-feeder-mac-throughput-scalar.txt
```
### Output
<img width="502" height="111" alt="image" src="https://github.com/user-attachments/assets/67950241-7899-46b0-8ab1-53af5e790fb4" />

### 查看user mac throughput
```
less stat-global-fwd-user-mac-throughput-scalar.txt
```
### Output
<img width="502" height="111" alt="image" src="https://github.com/user-attachments/assets/34c14398-1918-44df-8be6-8470f9f12003" />

### 為甚麼feederlink沒有資料？
這個範例的重點是：**FWD User Link 的 Beam Hopping**

不是 end-to-end（GW → SAT → UT）完整傳輸。

所以Application 流量只跑在 **User link（Satellite → UT）** `1.00848e+06 kps`(大約=1Gbps)

**User link 才是真正受 beam hopping 影響**的部分，並計算：
- Throughput
- Delay
- SINR
等數據。

## SINR（Signal to Interference plus Noise Ratio）
**接收到的有用訊號功率，相對於干擾＋雜訊的比例**

<img width="301" height="74" alt="image" src="https://github.com/user-attachments/assets/a76f3120-13fe-42ce-b240-8f427f9d09fe" />

通常用dB表示

| 符號                      | 意義                 |
| --- | --- |
| Psingal      | beam 訊號來源   |
| Pinterference | 其他 beam、其他使用者造成的干擾 |
| Pnoise       | 熱雜訊、接收器雜訊          |

**SINR = 實體層品質的總結指標**
​​
- **SINR**越高：連線品質好 → throughput 高、delay 低
- **SINR**越低：需要多重船 → throughput 掉、delay 高

**Beam Hopping可以改善環境 → 改變 SINR 分佈**
- 同一時間只有部分 beam 被點亮
- 收到的訊號S ↑
- 受到其他beam的干擾 ↓

### 對應程式碼
```
stat-global-fwd-composite-sinr-cdf-0-ATTN.txt
```
### Output
<img width="500" height="600" alt="image" src="https://github.com/user-attachments/assets/d36b2eb2-797d-403f-add4-d5f50750a2a4" />

| 欄位 | 意義 |
| ---| ---|
| `mean`     | 平均 SINR ≈ **9.65 dB** |
| `stddev`   | 標準差 ≈ 1.44 dB         |
| `variance` | 方差(代表 SINR 集中但有一定波動)                 |
| `% percentile_5`  | 最差 5% 的 SINR ≈ **8.28 dB**  |
| `% percentile_50%` | 中位數 ≈ **9.87 dB**           |
| `% percentile_95%` | 最好 5% 的 SINR ≈ **11.13 dB** |
| `sinr_db` | SINR（dB）                     |
| `freq`    | **CDF 值**（P[SINR ≤ sinr_db]） |

---
### （B）Per-beam（每個 Beam 一筆，Beam hopping 核心）
| 檔案名稱 | 層級 | 指標 | 實際作用 |
| --- | --- | --- | --- |
| `stat-per-beam-beam-service-time-scalar.txt`  | Per-beam | Service Time   | **每個 beam 被點亮 / 服務的時間比例（beam hopping 證據）** |
| `stat-per-beam-fwd-app-throughput-scalar.txt` | Per-beam | App Throughput | **每個 beam 的 Forward Link 吞吐量**             |


|檔名|輸出|說明|
|---|---|---|
|`stat-per-beam-beam-service-time-scalar.txt`|<img width="390" height="390" alt="image" src="https://github.com/user-attachments/assets/bda536c8-c569-44ec-977b-5022131d37dd" />|`beam_id` :	衛星上的 spot beam 編號<br><br>`service_time` : 該 beam 在整個模擬期間中，被「點亮／服務」的相對時間份額|
|`stat-per-beam-fwd-app-throughput-scalar.txt`|<img width="390" height="390" alt="image" src="https://github.com/user-attachments/assets/153fe96d-ae7f-4f1f-a925-2059ff74162c" />|`1-41` : GW 1 → Beam 41 <br> <br>`throughput_kbps` : 該 beam 底下所有 UT 的 Forward Link Application Throughput（kbps）|

example 預設的 beam ID (在程式裡): 
```
simulationHelper->SetBeams("1 2 3 4 11 12 13 14 25 26 27 28 40 41");
```

依照此輸出可得知`service_time` 多 → `throughput` 通常高
| Beam | service_time | throughput   |
| ---- | ------------ | ------------ |
| 12   | 1.715        | 119,993 kbps |
| 28   | 1.287        | 120,714 kbps |
| 27   | 0.428        | 63,881 kbps  |
| 25   | 0.429        | 34,217 kbps  |

---

### （C）Per-GW（每個 Gateway）
| 檔案名稱                                                  | 層級     | 指標             | 實際作用                          |
| ----------------------------------------------------- | ------ | -------------- | ----------------------------- |
| `stat-per-gw-fwd-app-throughput-scalar.txt`           | Per-GW | App Throughput | 每個 Gateway 的 Forward Link 吞吐量 |
| `stat-per-gw-fwd-app-throughput-scatter-1.txt`        | Per-GW | App Throughput | Gateway 吞吐量               |
| `stat-per-gw-fwd-feeder-mac-throughput-scalar.txt`    | Per-GW | MAC Throughput | 每個 GW 在 feeder link 的 MAC 吞吐量 |
| `stat-per-gw-fwd-feeder-mac-throughput-scatter-1.txt` | Per-GW | MAC Throughput | 隨時間變化的GW feeder MAC 吞吐量      |
| `stat-per-gw-fwd-user-mac-throughput-scalar.txt`      | Per-GW | MAC Throughput | GW user link MAC 吞吐量          |
| `stat-per-gw-fwd-user-mac-throughput-scatter-1.txt`   | Per-GW | MAC Throughput | 隨時間變化的GW user MAC 吞吐量           |

因為此系統只有一個GW，所以數值可以對應到global

---

### （D）Per-UT (一個UT生成一個輸出檔)

進階分析才需要看到**per-UT**

| 檔案名稱                                                     | 層級     | 指標             | 實際作用                         |
| -------------------------------------------------------- | ------ | -------------- | ---------------------------- |
| `stat-per-ut-fwd-app-throughput-scalar.txt`              | Per-UT | App Throughput | **所有 UT 的平均 throughput 彙整**  |
| `stat-per-ut-fwd-app-throughput-scatter-0.txt ~ 252.txt` | Per-UT | App Throughput | **每一個 UT 的 throughput 時間序列** |
| `stat-per-ut-fwd-feeder-mac-throughput-scalar.txt`          | Per-UT | MAC Throughput | UT 在 feeder link MAC 的平均 throughput |
| `stat-per-ut-fwd-feeder-mac-throughput-scatter-0 ~ 232.txt` | Per-UT | MAC Throughput | 每一個 UT 在 feeder MAC 的 throughput    |
| `stat-per-ut-fwd-user-mac-throughput-scatter-0 ~ 228.txt`   | Per-UT | MAC Throughput | 每一個 UT 在 user MAC 的 throughput      |
### Output
<img width="462" height="252" alt="image" src="https://github.com/user-attachments/assets/cb884be7-15a0-4854-b957-c7e7625f61ff" />

| 欄位                | 意義                                 |
| ----------------- | ---------------------------------- |
| `OUTPUT_TYPE_SUM` | 每個時間點是 **該秒 throughput 的加總**       |
| `count`           | 在 beam 照到時，且**實際有 Application 資料被成功送出的時間點數**         |
| `sum`             | 所有時間點 throughput 的 **總和（kbps 累加）** |

|檔名|輸出|
|---|---|
|`stat-per-ut-fwd-app-throughput-scatter-78.txt`|<img width="462" height="252" alt="image" src="https://github.com/user-attachments/assets/6ba12ce8-928b-4527-9810-99e5f2e7ee9b" />|
|`stat-per-ut-fwd-app-throughput-scatter-141.txt`|<img width="462" height="252" alt="image" src="https://github.com/user-attachments/assets/2f2d1107-5fd2-4198-8e03-e071e44706cc" />|
|`stat-per-ut-fwd-app-throughput-scatter-2.txt`|<img width="462" height="252" alt="image" src="https://github.com/user-attachments/assets/95ea0a96-04b9-40a3-b5f2-6d6468683cae" />|

`% time_sec` : 時間（秒）<BR>`throughput_kbps` : 該秒 UT 的 Forward App Throughput

`count` 越多，通常代表該 UT 被服務的次數越多，因此 `throughput`（sum）通常也會越大
但`count` 多不等於 `throughput`（sum） 一定大，可能會受
 - 同一 beam 下 UT 很多
 - 當次 SINR 較差

 影響
 
---


## Beam Hopping vs Static Beam

| 項目        | Static Beam | Beam Hopping |
| --------- | ----------- | ------------ |
| Beam 是否常亮 | 是           | 否（輪流）        |
| 頻寬分配      | 固定          | 動態           |
| 適合流量      | 平均          | 不平均          |
| 系統彈性      | 低           | 高            |
| 控制複雜度     | 低           | 高            |
| 模擬時間      | 快           | 較慢           |
|示意圖        |<img width="435" height="527" alt="image" src="https://github.com/user-attachments/assets/0882cb51-6e74-4b22-94a5-4aae2a9ad242" /><br>refrence https://www.2cm.com.tw/2cm/zh-tw/tech/A411B36580BD4541BB8E9350815D2A63|<img width="945" height="528" alt="image" src="https://github.com/user-attachments/assets/f5ecd453-f319-4863-9583-0d0ea6989edc" /><br>refrence https://dvb.org/wp-content/uploads/2020/02/20200330_webinar_beam_hopping_FINAL.pdf|

---

## 一個beam 的 UT數量
```
std::map<uint32_t, uint32_t> utsInBeam = {{1, 30},
                                              {2, 9},
                                              {3, 15},
                                              {4, 30},
                                              {11, 15},
                                              {12, 30},
                                              {13, 9},
                                              {14, 18},
                                              {25, 9},
                                              {26, 15},
                                              {27, 18},
                                              {28, 30},
                                              {40, 9},
                                              {41, 15}};

```
`std::map<uint32_t, uint32_t> utsInBeam = {{beam ID, UT}`
### 目的
製造**不均勻流量需求**  

增加高需求 beam 的 `service_time` (beam 4, beam 28)

減少低需求 beam 的 `service_time` (bema 2, beam 40)


## superframe定義
beam hopping 主要由 **BSTP（Beam Switching Time Plan）** 描述各 beam 在不同時間區段的啟用與關閉排程。(以 superframe 為單位，)

分別定義在兩個檔案中

- [`satellite-bstp-controller.cc`](https://github.com/sns3/sns3-satellite/blob/master/model/satellite-bstp-controller.cc) : 根據 BSTP 設定與 superframe duration 控制beam 啟用／關閉與切換時機
```
 .AddAttribute("SuperframeDuration",
                          "Superframe duration in Time.",
                          TimeValue(MilliSeconds(10)),
                          MakeTimeAccessor(&SatBstpController::m_superFrameDuration),
                          MakeTimeChecker());
```
- [`satellite-static-bstp.cc`](https://github.com/sns3/sns3-satellite/blob/master/model/satellite-static-bstp.cc) : 負責解析與提供 BSTP 設定內容 Line160 - 206
