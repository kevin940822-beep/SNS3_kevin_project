# Scale-Down Configuration Comparison (beam hopping)

## Purpose
用於比較 **Baseline 模擬設定** 與 **Scale-Down 模擬設定** 在beam hopping 模擬中的差異。

程式碼如下 : 
```
./ns3 run "sat-fwd-link-beam-hopping-example --simTime=2 --scaleDown=1 --OutputPath=results/bh-test/test1"
```
只修改`scaleDown`參數，其餘不變

test1 有scaledown

<img width="428" height="104" alt="image" src="https://github.com/user-attachments/assets/7c8b5640-7a7d-4898-9aab-ac2f53bc2f3d" />
<img width="468" height="100" alt="image" src="https://github.com/user-attachments/assets/1fac1785-bd7d-4fd6-bfc2-5ea2fb26b5ed" />

test2 原本

<img width="441" height="101" alt="image" src="https://github.com/user-attachments/assets/8c869add-9b9a-44c0-991e-429a48b2778d" />
<img width="468" height="97" alt="image" src="https://github.com/user-attachments/assets/e322d191-7e68-40e6-bfb0-2e56cf5026c9" />


|項目|`scaleDown=0`|`scaleDown=1`|
|---|---|---|
|
