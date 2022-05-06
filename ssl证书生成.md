# ssl证书生成(ubuntu 20.04LTS)

## 安装snapd
    sudo apt install snap
    snap version

## 安装core
    sudo snap install core
    sudo snap refresh core

## 安装cert-bot
    sudo snap install --classic certbot
    sudo ln -s /snap/bin/certbot /usr/bin/certbot

## 生成ssl证书
    // 此命令会自动生成一个随机字符串，将随机字符串添加到域名的txt记录里，等几分钟再按回车验证
    // 证书有效期3个月
    sudo certbot certonly --email xxx@gmail.com -d xxx.com,*.xxx.com --manual --preferred-challenges dns --server https://acme-v02.api.letsencrypt.org/directory

## 测试重新生成
    certbot renew --dry-run

## 重新生成
    certbot renew

## nginx配置
    ssl_certificate /etc/letsencrypt/live/xxx.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/xxx.com/privkey.pem;

## 参考资料
- https://certbot.eff.org/instructions?ws=nginx&os=ubuntufocal
- https://www.jianshu.com/p/23aa1eef5b23