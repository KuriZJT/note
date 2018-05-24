1. ifconfig -a 其中 HWaddr字段就是mac地址

2. cat /sys/class/net/eth0/address 查看eth0的mac地址

3. cat /proc/net/arp 查看连接到本机的远端ip的mac地址

4. 程序中使用SIOCGIFHWADDR的ioctl命令获取mac地址
