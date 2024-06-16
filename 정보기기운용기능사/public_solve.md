## 1. IP 주소 체크
- IP
  - sales_pc 클릭 후 desktop란에서 ip config...에 들어가기
    - IP address : 100.0.0.1
    - subnet-mask : 255.192.0.0
    - default-gateway : 100.63.255.254
    - server : 10.10.10.10
  - manage_pc 클릭후 desktop란에서 ip config...에 들어가기
    - IP address : 100.128.0.1
    - subnet-mask : 255.192.0.0
    - default-gateway : 100.191.255.254
    - server : 10.10.10.10

## 2. 기본 라우터 설정
- 배너 설정 - 라우터 클릭 후 CLI창 들어가기
  - en
  - conf t
  - ba mo C ^$#~ Router_01 ~#$^ C // 배너 설정
- 원격 로그인, 로그인 시 privileged mode에 접속되게 하기
  - us user01 pass router## // 유저네임과 암호를 설정한다
  - line console 0
  - pass router##
  - login
  - exit
  - line vty 0 4 
  - login lo // privileged mode 접속되게 하기
  - exit
- 암호설정
  - enable pass router##   // 암호설정
  - ser pa   // 인코딩
  - exit

## 스위치 설정 => VLAN
- VLAN 구성 - 스위치 클릭 후 CLI창 들어가기
  - enable
  - config terminal
  - hostname Switch_01   // 스위치의 호스트명을 찾아 넣는다
  - vlan 10
  - name Sales // vlan 10에 할당할 기기의 별칭을 적는다
  - vlan 20
  - name Manage // vlan 20에 할당할 기기의 별칭을 적는다
  - exit
- inter-VLAN
  - int ra fa0/1 - 10 // fast-ethernet 0/1 부터 fa0/10까지 범위를 잡아서 관리
  - sw mo acc
  - sw acc vl 10
  - int ra fa0/11 - 20         // fa0/11 ~ fa0/20 까지 범위를 잡아서 관리
  - sw mo acc
  - sw acc vl 20
  - int fa0/24 
  - sw mo tr
  - sw tr al vl 10,20
  - int vlan 10
  - no sh
  - ip add 100.0.0.2 255.192.0.0 // 스위치의 ip와 subnet-mask를 추가함
  - ip de 100.63.255.254 // default-gateway를 작성함

## 라우터 설정 (당연히 CLI창을 연 후 진행)
- se0/0/0 열어주기
  - en
  - conf t
  - int se0/0/0
  - no sh
  - ip add 172.30.0.9 255.255.255.252 // IP , subnet-mask
  - clock rate 64000
  - exit |또는| ctrl+Z
- fa0/0 열어주기
  - int fa0/0
  - no sh     // fa0/0의 통신 경로를 열어줌
- fa0/0.10, fa0/0.20 설정하기
  - int fa0/0.10
  - enc d 10
  - ip add 100.63.255.254 255.192.0.0 // default-gateway , subnet-mask
  - int fa0/0.20
  - enc d 20
  - ip add 100.191.255.254 255.192.0.0 // default-gateway , subnet-mask

## 동적 라우팅 (당연히 CLI창을 연 후 진행)
- en
- conf t
- r rip
- v 2 (라우터 RIP 버젼)
- net 100.0.0.0
- net 100.128.0.0  // 셋 다 "네트워크(구간)"의 값을 입력한다
- net 172.30.0.8
- no au
- pass fa0/0