### 本地电脑IP         192.168.1.6
### 阿里云公网服务器IP 110.184.161.x

```javascript
#在公网服务器上面更改SSH配置.
vi /etc/ssh/sshd_config
AllowTcpForwarding yes
GatewayPorts yes

#下面的命令会卡住不动，是正常的.
ssh -R 7890:192.168.1.6:7890 root@110.184.161.x -N
```


### 在公有云服务器上配置代理
```javascript
[root@node1 ~]# cat /root/.bashrc 
# .bashrc

# User specific aliases and functions

alias rm='rm -i'
alias cp='cp -i'
alias mv='mv -i'
alias proxym='export https_proxy=http://110.184.161.x:7890;export http_proxy=http://110.184.161.x:7890;export all_proxy=socks5://110.184.161.x:7890'

# Source global definitions
if [ -f /etc/bashrc ]; then
	. /etc/bashrc
fi
```
