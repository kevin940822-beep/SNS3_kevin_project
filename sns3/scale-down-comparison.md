# Scale-Down Configuration Comparison (beam hopping)

## Purpose
用於比較 **Baseline 模擬設定** 與 **Scale-Down 模擬設定** 在beam hopping 模擬中的差異。

程式碼如下 : 
```
./ns3 run "sat-fwd-link-beam-hopping-example --simTime=2 --scaleDown=1 --OutputPath=results/bh-test/test1"
```
只修改`scaleDown`參數，其餘不變

|項目|`scaleDown=0`|`scaleDown=1`|
|---|---|---|
|
