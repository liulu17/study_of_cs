# dns
dns就是根据域名查找ip地址，在linux系统上当需要域名解析时，会根据以下配置文件解析

## /etc/hosts 
hosts文件相当于一个缓存，当在该文件中找到了域名的ip地址则查找结束

## /etc/resolv.conf 
当hosts文件没找到时，会根据/etc/resolv.conf 文件中配置的dns服务器，去该服务器查找（发送查找请求）。dns服务器有可能在本地也有可能不在本地。本地dns服务器可以使用dnsmasq程序

## /etc/nsswitch.conf
这个文件定义了解析的顺序，即是先找hosts文件还是直接去dns服务器查询