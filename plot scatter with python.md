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

def plot_individual_data(data_dir, file_keyword):
    # 1. 自動偵測來源資料夾名稱 (例如 'leocon-ex1')
    source_folder_name = os.path.basename(os.path.normpath(data_dir))
    
    # 2. 動態建立輸出資料夾 (例如 'results/plot_leocon-ex1')
    output_dir = os.path.join("results", f"plot_{source_folder_name}")
    os.makedirs(output_dir, exist_ok=True)
    print(f"📁 所有個別圖檔將存入: {output_dir}")

    # 3. 搜尋數據路徑
    search_pattern = os.path.join(data_dir, f"*{file_keyword}*.txt")
    files = glob.glob(search_pattern)
    
    if not files:
        print(f"❌ 在 {data_dir} 找不到符合條件的檔案。")
        return

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
            plt.title(f"Analysis: {ylabel}\nSource: {file_name}", fontsize=12)
            plt.xlabel(xlabel)
            plt.ylabel(ylabel)
            plt.grid(True, linestyle='--', alpha=0.6)
            plt.legend(loc='upper right', fontsize='small')

            # --- 存入統一的 plot_ 資料夾 ---
            save_name = file_name.replace('.txt', '.png')
            save_path = os.path.join(output_dir, save_name)
            
            plt.savefig(save_path, dpi=200, bbox_inches='tight')
            plt.close()
            print(f"✅ 已儲存: {save_path}")

        except Exception as e:
            print(f"⚠️ 處理 {file_name} 時發生錯誤: {e}")

if __name__ == "__main__":
    parser = argparse.ArgumentParser(description="自動化個別繪圖工具 - 支援自動分類資料夾")
    parser.add_argument("--dir", required=True, help="數據來源資料夾路徑 (例如 results/leocon-ex1)")
    parser.add_argument("--name", default="", help="檔案關鍵字過濾")

    args = parser.parse_args()
    plot_individual_data(args.dir, args.name)
```

### 前往資料夾
```
cd ~/ns-3.43/results
```
### 執行程式檔
```
python3 plot.py --dir leocon-ex1 --name sat 
```
### Output
<img width="918" height="222" alt="image" src="https://github.com/user-attachments/assets/dcba62b0-a125-495b-9e57-57ba54c10b7b" />

ttps://github.com/user-attachments/assets/cb5bb845-3f66-4404-9f35-6b37d07ca5f7" />
