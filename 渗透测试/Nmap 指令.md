### Nmap主机发现



|     参数     | 解释                 |
| :----------: | -------------------- |
|     -sP      | ping 扫描            |
|     -P0      | 无 ping 扫描         |
|     -PS      | TCP SYN Ping 扫描    |
|     -PA      | TCP ACK Ping 扫描    |
|     -PU      | UDP Ping 扫描        |
| -PE,-PP,-PM  | ICMP Ping Types 扫描 |
|     -PR      | ARP Ping 扫描        |
|      -n      | 禁止 DNS 反向解析    |
|      -R      | 反向解析域名         |
| --system-dns | 使用系统域名解析器   |
|     -sL      | 列表扫描             |
|      -6      | 扫描 IPv6 地址       |
| --traceroute | 路由跟踪             |
|     -PY      | SCTP INIT Ping 扫描  |



#### 一次简单的扫描

```shell
nmap
```

#### Ping 扫描

- Ping 扫描只进行 Ping，然后显示出在线的主机

```
nmap -sP
```

#### 无 Ping 扫描

- 无 Ping 扫描通常用于防火墙禁止 Ping 的情况下

```
nmap -P0
```

#### TCP SYN Ping 扫描



```
nmap -PS -v
```

#### TCP ACK Ping 扫描

```
nmap -PA -v
```

#### UDP Ping 扫描

```
nmap -PU -v
```

#### ICMP Ping Types 扫描

```
nmap -PE -v
nmap -PP -v
nmap -PM -v
```

#### ARP Ping 扫描

```
nmap -PR
```

#### 扫描列表

```
nmap -sL
```

#### 禁止反向域名解析

```
nmap -n -sL
```

#### 反向解析域名

```
nmap -R -sL *.xx.xx.xx/24
```

#### 系统域名解析

```
nmap --system-dns 
```

#### IPv6 扫描

```
nmap -6
```

#### 路由跟踪

```
nmap --traceroute -v
```

#### SCTP INIT Ping 扫描

```
nmap -PY -v
```

