# TOC
- [Step](#steo)

## Step 

### Execute this command on your VM terminal
```
sudo apt update
sudo apt install openssh-server
```
### Go to **VM's** **setting** -> **Network**

choose **NAT**, **連接埠轉送 (Port Forwarding)**

```
name : ssh
主機連接埠 (Host Port) : 2222
客用連接埠 (Guest Port) : 22
```

### Install extensions in VS Code
```
Remote - SSH
```

### ssh your VM to VScode
```
ssh "your VM account"@127.0.0.1 -p 2222
```
