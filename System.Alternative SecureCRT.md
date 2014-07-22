#Ubuntu Linux에서 Securt CRT를 대체하자

##Intro
system admin에게 터미널 프로그램은 생산상에 가장 영향을 많이 미치는 중요한 어플리케이션이다. Windows환경에서는 Secure CRT가 그역활을 10여년 이상 나에게 가장 친숙하고 빈번한 프로그램이라 할만하다.

이제 업무용 데스크탑을  우분투라는 리눅스환경으로 전환하면서 SecureCRT를 대체할만한 환경을 구축해야 한다. 물론 리눅스OS의 기본 터미널은 telnet이나 ssh 접속을 기본으로 제공한다. 하지만 100여대의 시스템의 로그인, 로그인시 적용하는 각종 스크립트를 관리하기 위해서는 기본 터미널의 한계가 분명이 존재한다. 

이러한 Windows환경에서 익숙한 SecureCRT를 대체할수 있는 환경을 구성해보자.

##내가 필요한 기능
- 털넷과 SSH를 모두 지원
- 탭으로 이동이 가능할것.
- 로그인 스크립트를 적용할 수 있을 것
- 100여개 사이트를 구조적으로 목록을 작성할수 있을것
- 콘솔의 작업을 저장할 수 있을 것
- copy/paste 기능을 지원할 것

##Alternative SecureCRT
자 이러한 기능을 지원하기 위해 어떤 절차가 필요한가

###우선 Ubuntu OS 설치
2014.07현재 14.04버젼의 Desktop을 설치할 수 있다.

###후보1. GCM
`http://kuthulu.com/gcm`

리눅스진영에서 SecureCRT에 거의 근접한 오픈소스

특징을 살펴보면.

- GNU General Public License version 3 적용
- PyGTK기반하에서 운영됨. (기본 Ubuntu 설치시 이미 해당 python은 설치되어 있음)
- 자동 로그인 지원
- 다중 ssh 터널 지원
- 가로.세로 제한없이 창분할 가능
- 탭끼리 drap & drop가능
- 호스트 그룹기능
- 별도의 호스트를 동시에 동작을 수행할수 있는 클러스터 기능 지원
- shorcuts(단축키) 편집기능
- 사용자가 편집한 명령을 각 호스트로 보내기
- 당연히 free이고 소스코드 포함

###후보2.  이것보다 더 미끈하고 강력함을 원해.
그럼 PAC Manager로 갑시다.

`https://sites.google.com/site/davidtv/`

PAC는 Per/GTK로 만들어진 SecureCRT/Putty 등을 대체하여 리눅스에서 사용하자라는 프로젝트라고 말할수 있다. SSH/Telnet 접속을 GUI로 구현한 어플리케이션이다. 

기능은 아래와 같이 주루룩

- SecureCRT의 거의 모든 기능을 리눅스에서 구현.
- 원격,로컬의 매크로 지원
- EXPECT 정규식으로 원격지 명령어 전송 기능
- 당연히 동시접속 지원
- Proxy 지원
- KeePassX 지원
- Serial/tty 접속 지원
- RDP/VNC 지원
- 접속전/접속후 실행지원
- TAB이나 WINDOWS 도 지원합니다.
- 트레이의 메뉴아이콘을 통해서 quick 접속이 가능합니다.
- Wake-On-Lan 기등도 있어요
- 또 기억나지 않지만 무지무지 많은 기능이 포함되어 있습니다.
- 공짜입니다. FREE. GNU GPL v3)





