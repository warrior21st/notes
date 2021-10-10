
## WSL Ubuntu20.04 LTS 设置静态ip并设置ssh连接
### 设置静态ip
    //linux执行以下命令
    sudo ip addr del $(ip addr show eth0 | grep 'inet\b' | awk '{print $2}' | head -n 1) dev eth0
    sudo ip addr add 192.168.50.2/24 broadcast 192.168.50.255 dev eth0
    sudo ip route add 0.0.0.0/0 via 192.168.50.1 dev eth0
    sudo echo nameserver 192.168.50.1 > /etc/resolv.conf

    //windows在以管理员身份运行的powershell中运行以下命令
    Get-NetAdapter 'vEthernet (WSL)' | Get-NetIPAddress | Remove-NetIPAddress -Confirm:$False
    New-NetIPAddress -IPAddress 192.168.50.1 -PrefixLength 24 -InterfaceAlias 'vEthernet (WSL)'
    Get-NetNat | ? Name -Eq WSLNat | Remove-NetNat -Confirm:$False
    New-NetNat -Name WSLNat -InternalIPInterfaceAddressPrefix 192.168.50.0/24;

### 设置ssh
#### 卸载WSL上自带的openssh并重装（自带的有问题）
    sudo apt-get update
    sudo apt-get remove openssh-server 
    sudo apt-get install openssh-server

#### 修改ssh的配置
    sudo vi /etc/ssh/sshd_config
    
    //保持sshd_config文件中下面几个设置正确
    Port 22 //端口号
    PermitRootLogin yes //允许root登录
    PasswordAuthentication yes //密码验证
#### 重启ssh服务
    sudo service ssh --full-restart

