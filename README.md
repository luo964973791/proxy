### 本地windows电脑IP    192.168.1.6:22
### 本地Linux虚拟机IP    192.168.1.5
### 阿里云公网服务器IP   110.184.161.x

```javascript
#在本地虚拟机上面执行下面的命令会卡住不动，是正常的.
ssh-keygen -f /root/.ssh/id_rsa -t rsa -N ''
ssh -R 222:192.168.1.6:22 root@110.184.161.x -N &   #在192.168.1.5上面执行
sed -i 's/#AllowTcpForwarding no/AllowTcpForwarding yes/' /etc/ssh/sshd_config && systemctl restart sshd
```


### 在公有云服务器上配置代理
```javascript
[root@node1 ~]# cat /root/.bashrc 
# .bashrc

# User specific aliases and functions

alias rm='rm -i'
alias cp='cp -i'
alias mv='mv -i'
alias clear='export https_proxy=http://localhost:222; export http_proxy=http://localhost:222; export all_proxy=socks5://localhost:222; /usr/bin/clear'

# Source global definitions
if [ -f /etc/bashrc ]; then
	. /etc/bashrc
fi
```


### 测试.
```javascript
[root@node1 ~]# source /root/.bashrc 
[root@node1 ~]# myproxy
```


### 在本地Linux虚拟机IP添加任务计划.
```javascript
sudo cat << EOF > /usr/lib/systemd/system/ssh_tunnel.service
[Unit]
Description=SSH Reverse Tunnel Service
After=network.target

[Service]
ExecStart=/usr/bin/ssh -R 222:192.168.1.6:22 root@110.184.161.x -N
User=root
Restart=always
RestartSec=15

[Install]
WantedBy=multi-user.target
EOF
systemctl enable ssh_tunnel

#在本地Linux虚拟机上面更改SSH配置.
vi /etc/ssh/sshd_config
AllowTcpForwarding yes
GatewayPorts yes

systemctl restart sshd
```


### 上面的操作监听的是127.0.0.1:222端口，如果要用0.0.0.0:222端口需要配置sshd_config文件(非必须操作，可执行,也可不执行下面这一步.)
```javascript
#在本地Linux虚拟机上面更改SSH配置.
vi /etc/ssh/sshd_config
AllowTcpForwarding yes
GatewayPorts yes

systemctl restart sshd
```
