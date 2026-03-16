# TOC
- [前往example](#前往example)
- [結果檔位置](#結果檔位置)
- [使用python畫圖](#使用python畫圖)
- [創建資料夾](#創建資料夾)
- [刪除資料夾](#刪除資料夾)
- [Install](#install-chrome)
  - [chrome](#install-chrome)
  - [discord](#install-discord)
- [check storage space](#check-storage-space)

## 沒有要使用wireshark看封包，將程式碼中 PacketTraceEnabled 改為 false

### 前往example
```
cd ~/workspace/bake/source/ns-3.43/contrib/satellite/examples

若已經在 workspace/bake/source/ns-3.43/contrib/satellite

則可以直接cd examples

也可以pwd查看現在位置
```

### 結果檔位置
```
cd ~/workspace/bake/source/ns-3.43/results
```
### 使用python畫圖
```
python3 plot_scatter.py ""
```

### 創建資料夾
```
mkdir -p results/leocon-exp1                                      
./ns3 run sat-constellation-example -- --OutputPath=results/leocon-exp1
```

### 刪除資料夾
```
rm -rf 資料夾名稱
```
### Install chrome
```
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
sudo apt install ./google-chrome-stable_current_amd64.deb -y
```

### Install discord
```
wget -O discord.deb "https://discord.com/api/download?platform=linux&format=deb"
sudo apt install ./discord.deb -y
```

### check storage space
```
du -sh ~/* | sort -h
```
