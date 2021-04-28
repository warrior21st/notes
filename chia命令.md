## chia client commonds
#### 查看public key命令
    //windows powershell
    ~\AppData\Local\chia-blockchain\app-1.1.1\resources\app.asar.unpacked\daemon\chia.exe keys show | select-string "Farmer","Pool"

    //执行后显示的Farmer public key是农民公钥，Pool public key是矿池公钥
#### p盘命令
    chia plots create -k [k大小] -b 使用的内存（以MiB为单位） -n 1 -f 农民公钥 -p 矿池公钥 -t 临时文件路径 -d 最终文件路径 -r 使用的cpu线程数(最小要2) -u 128 -e

    //window默认安装路径(cmd)：%USERPROFILE%\AppData\Local\chia-blockchain\app-1.1.1\resources\app.asar.unpacked\daemon\chia.exe
    //window默认安装路径(powershell)：~\AppData\Local\chia-blockchain\app-1.1.1\resources\app.asar.unpacked\daemon\chia.exe
    //Mac默认安装路径：/Applications/Chia.app/Contents/Resources/app.asar.unpacked/daemon/chia

    //windows命令执行循环示例（循环执行10次）  
    set time=10
    title ploting temp on d:\\ progress 0/%time%

    FOR /l %%i IN (1,1,%time%) DO (
        title  ploting temp on c:\\ progress %%i/%time%
        cd %USERPROFILE%\AppData\Local\chia-blockchain\app-1.1.1\resources\app.asar.unpacked\daemon\
        chia.exe plots create -k 32 -n 1 -b 5000 -f 80d9db5e03d373d45fd7e2e315b00d2ebac9b5a0b652319d94906da21b3a30f101fcb673ffbe17d4949d2723f6a4f3bb -p 87b2974d5ed1634b04d2e2d39c641f288130677cfd70d97e943838a904bd191bcfdecaa34e591d4a8456e3b995a38dbf -t d:\\chia_temp -d z:\\ -r 2 -u 128
    )

    echo all polt create tasks complete.
    pause

#### Storage requirements（以1.0.5以上版本为例）
| K-size      | RAM Default Usage | Temp. Size | Final Size |
| ----------- | -------------------- | -------- | ---------- |
| K=32        | 4608 MiB (4.5 GiB)   | 332 GiB  | 101.4 GiB  |
| K=33        | 7400 MiB (7.3 GiB)   | 589 GiB  | 208.8 GiB  |
| K=34        | 14800 MiB (14.5 GiB) | 1177 GiB | 429.8 GiB  |
| K=35        | 29600 MiB (29 GiB)   | 2355 GiB | 884.1 GiB  |

#### 建议参数
    //以k=32为例
    chia plots create -k 32 -n 1 -b 6000 -f 农民公钥 -p 矿池公钥 -t 临时文件路径 -d 最终文件路径 -r 4 -u 128 -e

#### 建议p盘配置
    //以单个k=32的p盘任务为例（多个可自行*个数）
    cpu主频：3.6G+ 线程*4 （2线程也可，4线程更佳，线程数影响阶段1效率，主频影响所有阶段效率）
    内存：8G+ （内存影响阶段2排序效率）
    ssd：240GiB+ （单个p盘任务独享一块ssd时sata与nvme差距不大）   

##### 参考
- CLI-Commands：https://github.com/Chia-Network/chia-blockchain/wiki/CLI-Commands-Reference
- Storage requirements：https://github.com/Chia-Network/chia-blockchain/wiki/k-sizes

