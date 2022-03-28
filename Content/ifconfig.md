# ifconfig说明

## 回环地址
```
lo0: flags=8049<UP,LOOPBACK,RUNNING,MULTICAST> mtu 16384
	options=1203<RXCSUM,TXCSUM,TXSTATUS,SW_TIMESTAMP>
	inet 127.0.0.1 netmask 0xff000000
	inet6 ::1 prefixlen 128
	inet6 fe80::1%lo0 prefixlen 64 scopeid 0x1
	nd6 options=201<PERFORMNUD,DAD>
```
  
## 软件网络接口，通用 IP-in-IP隧道(RFC2893)
```
gif0: flags=8010<POINTOPOINT,MULTICAST> mtu 1280

```

## 6to4连接(RFC3056)，ipv6->ipv4通道接口
```
stf0: flags=0<> mtu 1280
```

## 以太网：有线网卡和无线网卡，还有蓝牙等
```
en0: flags=8863<UP,BROADCAST,SMART,RUNNING,SIMPLEX,MULTICAST> mtu 1500
	options=400<CHANNEL_IO>
	ether a4:5e:60:bb:b7:33
	inet6 fe80::c48:5588:236e:9c9f%en0 prefixlen 64 secured scopeid 0x4
	inet 192.168.31.132 netmask 0xffffff00 broadcast 192.168.31.255
	nd6 options=201<PERFORMNUD,DAD>
	media: autoselect
	status: active
	
en2: flags=8963<UP,BROADCAST,SMART,RUNNING,PROMISC,SIMPLEX,MULTICAST> mtu 1500
	options=460<TSO4,TSO6,CHANNEL_IO>
	ether 82:13:01:99:c1:c1
	media: autoselect <full-duplex>
	status: inactive
	
en1: flags=8963<UP,BROADCAST,SMART,RUNNING,PROMISC,SIMPLEX,MULTICAST> mtu 1500
	options=460<TSO4,TSO6,CHANNEL_IO>
	ether 82:13:01:99:c1:c0
	media: autoselect <full-duplex>
	status: inactive
	
en3: flags=8863<UP,BROADCAST,SMART,RUNNING,SIMPLEX,MULTICAST> mtu 1500
	options=400<CHANNEL_IO>
	ether 2a:5a:eb:16:a4:e6
	inet6 fe80::c89:b168:fd34:430f%en3 prefixlen 64 secured scopeid 0xe
	inet 169.254.44.179 netmask 0xffff0000 broadcast 169.254.255.255
	nd6 options=201<PERFORMNUD,DAD>
	media: autoselect (100baseTX <full-duplex>)
	status: active
```
  

## 第二层网桥
```
bridge0: flags=8863<UP,BROADCAST,SMART,RUNNING,SIMPLEX,MULTICAST> mtu 1500
	options=63<RXCSUM,TXCSUM,TSO4,TSO6>
	ether 82:13:01:99:c1:c0
	Configuration:
		id 0:0:0:0:0:0 priority 0 hellotime 0 fwddelay 0
		maxage 0 holdcnt 0 proto stp maxaddr 100 timeout 1200
		root id 0:0:0:0:0:0 priority 0 ifcost 0 port 0
		ipfilter disabled flags 0x0
	member: en1 flags=3<LEARNING,DISCOVER>
	        ifmaxaddr 0 port 6 priority 0 path cost 0
	member: en2 flags=3<LEARNING,DISCOVER>
	        ifmaxaddr 0 port 5 priority 0 path cost 0
	nd6 options=201<PERFORMNUD,DAD>
	media: <unknown type>
	status: inactive
```
  
  
## Point-to-Point 协议
```
p2p0: flags=8843<UP,BROADCAST,RUNNING,SIMPLEX,MULTICAST> mtu 2304
	options=400<CHANNEL_IO>
	ether 06:5e:60:bb:b7:33
	media: autoselect
	status: inactive
```
  
## airdrop peer to peer（一种mesh网络），airdrop特有 
```
awdl0: flags=8943<UP,BROADCAST,RUNNING,PROMISC,SIMPLEX,MULTICAST> mtu 1484
	options=400<CHANNEL_IO>
	ether 06:61:45:ca:08:60
	inet6 fe80::461:45ff:feca:860%awdl0 prefixlen 64 scopeid 0x9
	nd6 options=201<PERFORMNUD,DAD>
	media: autoselect
	status: active
```
  
## 无线局域网低延迟接口
```
llw0: flags=8863<UP,BROADCAST,SMART,RUNNING,SIMPLEX,MULTICAST> mtu 1500
	options=400<CHANNEL_IO>
	ether 06:61:45:ca:08:60
	inet6 fe80::461:45ff:feca:860%llw0 prefixlen 64 scopeid 0xa
	nd6 options=201<PERFORMNUD,DAD>
	media: autoselect
	status: active
```
  
# 虚拟设备
```
utun0: flags=8051<UP,POINTOPOINT,RUNNING,MULTICAST> mtu 1380
	inet6 fe80::44a9:ccff:4f1b:21ed%utun0 prefixlen 64 scopeid 0xb
	nd6 options=201<PERFORMNUD,DAD>
utun1: flags=8051<UP,POINTOPOINT,RUNNING,MULTICAST> mtu 2000
	inet6 fe80::7d02:7eb:8105:e2fd%utun1 prefixlen 64 scopeid 0xc
	nd6 options=201<PERFORMNUD,DAD>
utun2: flags=8051<UP,POINTOPOINT,RUNNING,MULTICAST> mtu 1000
	inet6 fe80::ce81:b1c:bd2c:69e%utun2 prefixlen 64 scopeid 0xd
	nd6 options=201<PERFORMNUD,DAD>
```

