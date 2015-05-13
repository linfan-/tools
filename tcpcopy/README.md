#TCPCOPY使用

## 安装
- ./configure --enable-nfqueue --enable-mysql (3.5内核使用nfqueue,mysql流量复制采用--enable-mysql)
- make
- make install


## 启动

### client 启动

  普通流量复制
  tcpcopy -l /tmp/tcpcopy.log -x src_port-dst_host:dst_port(src_port为复制流量的端口,dst_host和dst_port为接收复制流量的主机和端口)

  MySQL流量复制
  tcpcopy -l /tmp/tcpcopy.log -x src_port-dst_host:dst_port -u username@passwd(src_port为复制流量的端口,dst_host和dst_port为接收复制流量的主机和端口, -u后为mysql的用户名和密码) 


### server 启动

内核 < 3.5
  - modprobe ip_queue
  - iptables -I OUTPUT -p tcp --sport port -j QUEUE(port替换成接收流量的端口)
  - intercept -d -l /tmp/intercept.log

内核 > 3.5
  - iptables -I OUTPUT -p tcp --sport port -j NFQUEUE(port替换成接收流量的端口)
  - intercept -d -l /tmp/intercept.log
   
## tips
   - 启动时先启动server，再启动client端,关闭时反之
