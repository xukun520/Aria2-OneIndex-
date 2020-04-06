# Aria2-OneIndex-
Aria2 + OneIndex 自动上传打造离线网盘
1. 安装 Nginx + PHP
yum -y install wget screen curl python git
wget http://mirrors.linuxeye.com/lnmp-full.tar.gz
tar xzf lnmp-full.tar.gz
cd lnmp
screen -S lnmp
./install.sh
根据提示选择安装 Nginx 和 PHP 即可

2. 绑定 2 个域名
在 DNS 处配置 2 个域名绑定到服务器，然后使用 ./vhost.sh 添加域名。

3. 安装 Aria2 服务端
wget -N --no-check-certificate https://raw.githubusercontent.com/ToyoDAdoubi/doubi/master/aria2.sh && chmod +x aria2.sh && bash aria2.sh
启动： /etc/init.d/aria2 start
停止： /etc/init.d/aria2 stop
重启： /etc/init.d/aria2 restart
查看状态： /etc/init.d/aria2 status
配置文件： /root/.aria2/aria2.conf
**令牌密匙：**随机生成（可以自己修改 6. 修改 配置文件）
默认下载目录： /usr/local/caddy/www/aria2/Download
4. 下载 AriaNg 和 OneIndex 源码
wget https://github.com/mayswind/AriaNg/releases/download/0.5.0/AriaNg-0.5.0.zip
git clone https://github.com/donwa/oneindex .
5. 配置 Aria2 和 OneIndex
修改 aria2.conf 中的下载目录，在配置文件末尾添加一行 on-download-complete=/root/upload2one.sh

创建 /root/upload2one.sh 文件，写入如下内容：

#!/bin/bash
path=$3
downloadpath='/data/aria2/Download'
if [ $2 -eq 0 ]
  then
    exit 0
fi
while true; do
filepath=$path
path=${path%/*};
if [ "$path" = "$downloadpath" ] && [ $2 -eq 1 ]
    then
      /usr/local/php/bin/php /data/wwwroot/pan.2333.blog/one.php upload:file "$filepath" /upload/
    rm -rf "$filepath"
    exit 0
elif [ "$path" = "$downloadpath" ]
    then
      /usr/local/php/bin/php /data/wwwroot/pan.2333.blog/one.php upload:folder "$filepath"/ /upload/"${filepath##*/}"/
    rm -rf "$filepath"/
    exit 0
fi
done
注意：downloadpath 为你的 aria2 下载目录，/usr/local/php/bin/php 为你的 php 可执行文件路径，

/data/wwwroot/pan.2333.blog 为你的 oneindex 源码所在目录。

给执行权限

chmod +x /root/upload2one.sh
重启 Aria2 服务端

/etc/init.d/aria2 restart
6. 安装 OneIndex
chown -R www:www /data/wwwroot
附录
onindex 主页
AriaNg 主页
Aria2 一键脚本
