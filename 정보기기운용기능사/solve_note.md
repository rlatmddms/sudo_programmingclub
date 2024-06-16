## 1. IP 주소 체크
- IP : 주어진 대로 입력
- subnet-mask : 문제 좌측 "네트워크(구간)" 의 "xxx.xxx.xxx.xxx / N" 코드에서 N값을 활용    
ex) 11111111.11000000.00000000.00000000 (N = 10) => 255.192.0.0
- default-gateway : subnet-mask와 "네트워크(구간)" 값을 AND연산 후, N번째 값 이후는 그냥 1로 채움. 마지막 값만 254     
ex) 255.192.0.0(N = 10) 과 100.0.0.0을 AND연산 후 N번째 값 이후 1로 바꾸면
    11111111.11000000.00000000.00000000
    01100100.00000000.00000000.00000000 AND 연산
=>  01100100.00111111.11111111.11111111 => 100.63.255.255 => 100.63.255.254

## 2. 기본 라우터 설정
- 배너 설정
  - 라우터 클릭 후 CLI창 들어가기
  - enable
  - config terminal
  - banner modify @######@ (##는 지정된 암호 설정이다) |혹은| ba mo C ###### C // 배너 설정
  - exit // 배너 설정 후 탈출
- 원격 로그인, 로그인 시 privileged mode에 접속되게 하기
  - enable
  - config terminal
  - username #### password #### // 유저네임과 암호를 설정한다
  - line vty 0 4 
  - login local (privileged mode 접속되게 하기)
  - exit
- 암호설정
  - enable password #### // 암호설정
  - security password // 인코딩
  - exit

## 스위치 설정 => VLAN
- VLAN 구성
  - enable
  - config terminal
  - hostname ####   (##에는 스위치의 호스트명을 찾아 넣는다)
  - vlan 10
  - name #### (vlan 10에 할당할 기기의 별칭을 적는다)
  - vlan 20
  - name #### (vlan 20에 할당할 기기의 별칭을 적는다)
  - exit
- inter-VLAN
  - interface range fa0/1 - 10 // fast-ethernet 0/1 부터 fa0/10까지 범위를 잡아서 관리
  - switch mode access
  - switch access vlan 10
  - interface range fa0/11 - 20         // fa0/11 ~ fa0/20 까지 범위를 잡아서 관리
  - switch mode access 
  - switch access vlan 20
  - interface fa0/24 
  - switch mode trunk
  - switch trunk allow vlan 10,20
  - interface vlan 10
  - no shutdown
  - ip address xxx.xxx.xxx.xxx xxx.xxx.xxx.xxx // 스위치의 ip와 subnet-mask를 추가함
  - ip default-gateway xxx.xxx.xxx.xxx // default-gateway를 작성함

## 라우터 설정
- fa0/0 열어주기
  - enable
  - config terminal
  - interface fa0/0
  - no shutdown     // fa0/0의 통신 경로를 열어줌
- fa0/0.10, fa0/0.20 설정하기
  - interface fa0/0.10
  - enc d 10
  - ip address xxx.xxx.xxx xxx.xxx.xxx // default-gateway , subnet-mask
  - interface fa0/0.20
  - enc d 20
  - ip address xxx.xxx.xxx xxx.xxx.xxx // default-gateway , subnet-mask
- se0/0/0 열어주기
  - interface se0/0/0
  - no shutdown
  - ip address xxx.xxx.xxx xxx.xxx.xxx // IP , subnet-mask
  - clock rate 64000
  - exit |또는| ctrl+Z
## 동적 라우팅
- enable
- config terminal
- router rip
- v 2 (라우터 RIP 버젼)
- network xxx.xxx.xxx
- network xxx.xxx.xxx  // 셋 다 "네트워크(구간)"의 값을 입력한다
- network xxx.xxx.xxx
- no au
- pass fa0/0