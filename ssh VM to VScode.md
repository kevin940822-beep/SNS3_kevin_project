# TOC
- [Step](#step)

## Step 

### Execute this command on your VM terminal
```
sudo apt update
sudo apt install openssh-server
```

### Go to **VM's** **setting** -> **Network**

choose **NAT** , **連接埠轉送 (Port Forwarding)**

<img width="688" height="374" alt="image" src="https://github.com/user-attachments/assets/f7d02cd6-60fe-4386-b0ea-a25dfdab77cc" />

```
name : ssh
主機連接埠 (Host Port) : 2222
客用連接埠 (Guest Port) : 22
```

### Install extensions in VS Code
```
name : Remote - SSH
```

### ssh your VM to VScode
```
ssh "your VM account"@127.0.0.1 -p 2222
```
- choose `Linux`
- Enter your VM password
