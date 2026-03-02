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

### 察看結果檔
```
cd ~/workspace/bake/source/ns-3.43/results/leocon-exp1
ls
```
<img width="1464" height="820" alt="image" src="https://github.com/user-attachments/assets/8db817e0-88de-47d0-9882-c3187e3decad" />
