## chia client commonds
#### 查看public key命令
    //windows powershell
    ~\AppData\Local\chia-blockchain\app-1.1.1\resources\app.asar.unpacked\daemon\chia.exe keys show | select-string "Farmer","Pool"

    //执行后显示的Farmer public key是农民公钥，Pool public key是矿池公钥
#### p盘命令
    chia plots create -k [k大小] -n 1 -f 农民公钥 -p 矿池公钥 -t 临时文件路径 -d 最终文件路径 -r 使用的cpu线程数(最小要2) -u 128 -e

    //window默认安装路径(cmd)：%USERPROFILE%\AppData\Local\chia-blockchain\app-1.1.1\resources\app.asar.unpacked\daemon\chia.exe
    //window默认安装路径(powershell)：~\AppData\Local\chia-blockchain\app-1.1.1\resources\app.asar.unpacked\daemon\chia.exe
    //Mac默认安装路径：/Applications/Chia.app/Contents/Resources/app.asar.unpacked/daemon/chia
#### Storage requirements（以1.0.5以上版本为例）
| K-size      | RAM Default Usage | Temp. Size | Final Size |
| ----------- | -------------------- | -------- | ---------- |
| K=32        | 4608 MiB (4.5 GiB)   | 332 GiB  | 101.4 GiB  |
| K=33        | 7400 MiB (7.3 GiB)   | 589 GiB  | 208.8 GiB  |
| K=34        | 14800 MiB (14.5 GiB) | 1177 GiB | 429.8 GiB  |
| K=35        | 29600 MiB (29 GiB)   | 2355 GiB | 884.1 GiB  |

##### 参考
- CLI-Commands：https://github.com/Chia-Network/chia-blockchain/wiki/CLI-Commands-Reference
- Storage requirements：https://github.com/Chia-Network/chia-blockchain/wiki/k-sizes

