## 📌 DHCP
- [python3 dhcp client/server implementation](https://github.com/1ueur/dhcp-implementation)

**DHCP(Dynamic Host Configuration Protocol)** : 동적 호스트 설정 통신 규약으로 각 컴퓨터에서 IP 관리를 쉽게하기 위한 프로토콜. TCP/IP 통신을 위해 필요한 정보를 동적 할당한다.
*IP/Subnet mask/default gateway/DNS etc...* DHCP 서버가 자동 할당해주기 때문에 네트워크 관리가 편하다.

![dhcp-1](https://user-images.githubusercontent.com/66156026/204346466-82f43023-e945-4d35-b2c7-bc2ba515bf40.jpg)

<img width="534" alt="스크린샷 2022-11-29 오전 2 58 47" src="https://user-images.githubusercontent.com/66156026/204347924-00a34f7a-3485-423e-b94c-edc8f6e40603.png">
<br/>

````Message type````: 현재 패킷의 유형을 나타냄. 2는 응답, 1은 요청 패킷이다.<br/>
```Hardware type/address length```: type은 물리적인 네트워크 타입을 나타낸다. 1은 이더넷. address length는 하드웨어 주소의 길이이다.<br/>
```Hops```: DHCP 서버에 도착하는 동안 통과하는 패킷의 최대 홉 수<br/>
```Transaction ID```: 응답 패킷에서 자신이 보낸 요청에 대한 응답이 맞는지 구별하는 데 사용된다. 같은 Request, Reply라면 동일한 Transaction ID를 가질 것이다.<br/>
```Seconds elapsed```: 기본 단위를 초로 새로운 IP를 요청하거나 IP 갱신을 요청했을 때부터 경과한 시간을 표시한다.<br/>
```Bootp flags```: 클라이언트가 브로드캐스트 or 유니캐스트 중 어디로 MAC 패킷을 받을지 정의한다. 0은 유니캐스트, 1은 브로드캐스트<br/>
```Client IP address```: DHCP 서버로부터 할당 받은 IP 주소로 채워진다. IP 할당 받기 전이기 때문에 0.0.0.0으로 나타난다.<br/>
```Your (client) IP address```: DHCP 서버에서 할당하는 임대 가능한 IP 주소이다.<br/>
```Next server IP address```: 중계 에이전트가 사용될 때 DHCP의 IP 주소가 채워진다.<br/>
```Relay agent IP address```: 중계 에이전트가 사용될 때 DHCP 중계 에이전트의 IP이다.<br/>
```Client MAC address```: IP 임대를 요청하는 클라이언트의 하드웨어 주소이다. 이 필드로 신뢰성이 확보된다. 이더넷 타입이기 때문에 MAC 주소가 설정되어 있다. **(Xircom??)** <br/>
```Server host name```: DHCP 서버의 호스트 이름이다. <br/>
```Boot file name```: DHCP가 사용할 부트 파일의 이름이다.<br/>
```Magic cookie```: DHCP와 BOOTP를 구분하는 코드이다.<br/>
```Option (53) DHCP Message Type```: DHCP 메세지 유형 옵션<br/>
```Option (54) DHCP Server Identifier```: 선택한 서버의 IP 주소<br/>
```Option (51) IP Address Lease Time```: IP 주소의 임대시간<br/>
```Option (1) Subnet Mask```: Subnet Mask<br/>
```Option (3) Router```: Router<br/>
...<br/>
```Option (255) End```: 끝을 의미



## 📌 USE dummy interface
디버깅 및 테스트 목적으로 dummy network interface를 만들어 사용할 수 있다.
실제로 전송하지 않고 패킷을 라우팅할 장치를 제공한다.

```
sudo modprobe dummy

sudo ip link add dum0 type dummy
ifconfig -a

// wireshark open

sudo ifconfig dum0 up
sudo dhclient -i dum0

// del
sudo ip link del dum0
```
