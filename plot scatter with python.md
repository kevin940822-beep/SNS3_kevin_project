### check virtual machine have python
```
python3 --version
```

### install python 
```
sudo apt update
sudo apt install -y python3-pip python3-matplotlib python3-numpy
```

### file location

<img width="352" height="217" alt="image" src="https://github.com/user-attachments/assets/e59fadba-11f2-4ad1-9815-93c6aa868977" />


### create a new script
```
nano plot.py
```

###
```
import matplotlib.pyplot as plt
import os
import argparse
import glob

def get_labels(file_name):
    """根據檔名自動判斷 Y 軸標籤與單位"""
    name_lower = file_name.lower()
    if 'throughput' in name_lower:
        return "Time (sec)", "Throughput (kbps)"
    elif 'delay' in name_lower:
        return "Time (sec)", "Delay (ms)"
    elif 'jitter' in name_lower:
        return "Time (sec)", "Jitter (ms)"
    elif 'drop' in name_lower:
        return "Time (sec)", "Packet Drop Rate"
    else:
        return "X-Axis", "Y-Axis"

def plot_data(folder, file_keyword, output_root):
    # 搜尋數據路徑
    search_pattern = os.path.join(folder, f"*{file_keyword}*.txt")
    files = glob.glob(search_pattern)
    
    if not files:
        print(f"❌ 找不到檔案！路徑: {search_pattern}")
        return

    # 建立輸出的資料夾 (例如: output_plots/leocon-ex1)
    # os.path.basename(folder) 會取得 "leocon-ex1"
    target_output_dir = os.path.join(output_root, os.path.basename(folder))
    os.makedirs(target_output_dir, exist_ok=True)
    print(f"📁 圖片將儲存至: {target_output_dir}")

    for file_path in files:
        file_name = os.path.basename(file_path)
        print(f"📊 正在處理: {file_name}...")
        
        x, y = [], []
        try:
            with open(file_path, 'r') as f:
                for line in f:
                    line = line.strip()
                    if not line or line.startswith('%'): continue
                    values = line.split()
                    if len(values) >= 2:
                        x.append(float(values[0]))
                        y.append(float(values[1]))
            
            if not x: continue

            xlabel, ylabel = get_labels(file_name)

            plt.figure(figsize=(10, 6))
            plt.plot(x, y, label=file_name, color='dodgerblue', linewidth=1.2)
            plt.title(f"Simulation Analysis: {ylabel}\nSource: {file_name}", fontsize=12)
            plt.xlabel(xlabel)
            plt.ylabel(ylabel)
            plt.grid(True, linestyle='--', alpha=0.6)
            plt.legend(loc='upper right', fontsize='small')

            # --- 修改存檔路徑 ---
            # 檔名從 .txt 換成 .png，並存到 target_output_dir
            save_name = file_name.replace('.txt', '.png')
            save_path = os.path.join(target_output_dir, save_name)
            
            plt.savefig(save_path, dpi=200, bbox_inches='tight')
            plt.close()
            print(f"✅ 已儲存圖片: {save_path}")

        except Exception as e:
            print(f"⚠️ 處理 {file_name} 時發生錯誤: {e}")

if __name__ == "__main__":
    parser = argparse.ArgumentParser(description="自動化繪圖工具 - 支援獨立輸出資料夾")
    parser.add_argument("--dir", default="leocon-ex1", help="數據來源資料夾")
    parser.add_argument("--name", default="", help="檔案關鍵字")
    parser.add_argument("--out", default="output_plots", help="圖片輸出總目錄 (預設: output_plots)")

    args = parser.parse_args()
    plot_data(args.dir, args.name, args.out)
```

### 前往資料夾
```
cd ~/ns-3.43/results
```
### 執行程式檔
```
python3 plot.py --dir leocon-ex1 --name sat --out sat
```
### Output
<img width="918" height="222" alt="image" src="https://github.com/user-attachments/assets/dcba62b0-a125-495b-9e57-57ba54c10b7b" />

ttps://github.com/user-attachments/assets/cb5bb845-3f66-4404-9f35-6b37d07ca5f7" />
