## DVB-RCS2 waveform.txt

| 欄位 | 名稱 | 說明 | 範例 |
|---|---|---|---|
| 1 | Waveform Index | SNS3 內部索引 |`1`~`22`<br>且 `default_waveform.txt` : 預設 UT 會使用第 3 行的配置來起步)|
| 2 | DVB-RCS2 ID | 官方標準波形編號 |`2`~`22`|
| 3 | Modulation Order | 調變階數<br>每個symbols的位元數 |`2`=QPSK (最穩定、抗雜訊最強，但速度慢) <br> `3`=8PSK <br> `4`=16QAM(速度最快，但需要極高的訊號品質)|
| 4 | FEC Code Rate | 前向錯誤更正編碼率 |`1/2`：代表傳送出去的資料裡，只有一半是真實的使用者資料<br> `5/6`：代表容錯碼越少、傳輸速度越快|
| 5 | Payload Size | 有效載荷大小 (Bytes) |`59`：一個Frame真正能運送多少 Bytes 的應用程式資料|
| 6 | Frame Length | 叢發長度 (Symbols) |太空中發送資料時，所佔用的物理長度（符號數量）<br>  `262`：短訊框<br> `536`：一般訊框 <br> `1616`：長訊框|
