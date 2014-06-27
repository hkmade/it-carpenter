it-carpenter
============

개인적인 IT관련 기술문서를 저장함

Markdown기본 문서 서식

타이틀은 1번(#)  
주제목은 2번(##)
주제 아래 소제목은 3번(###)

예문
#DIY NAS 설치
##PROLOGUE
이 문서의 목표와 개요 등등
##설치를 위한 준비
###디렉토리 권한
이 곳이 본문
###NAS용 전용 도메인 설치
이곳이 본문

소스코드
`ls -la` 

전체화면 캡쳐는 블럭으로 잡고 탭으로 들여쓰기.

	/home/proworks>df -k
	파일시스템           K바이트    사용    가용   용량    설치지점
	/dev/md/dsk/d0       37091824 15765187 20955719    43%    /
	/devices                   0       0       0     0%    /devices
	ctfs                       0       0       0     0%    /system/contract
	proc                       0       0       0     0%    /proc
	mnttab                     0       0       0     0%    /etc/mnttab
	swap                 9793016    1744 9791272     1%    /etc/svc/volatile
	objfs                      0       0       0     0%    /system/object
	sharefs                    0       0       0     0%    /etc/dfs/sharetab
	/platform/sun4u-us3/lib/libc_psr/libc_psr_hwcap1.so.1
	                     37091824 15765187 20955719    43%    /platform/sun4u-us3/lib/libc_psr.so.1
	/platform/sun4u-us3/lib/sparcv9/libc_psr/libc_psr_hwcap1.so.1
	                     37091824 15765187 20955719    43%    /platform/sun4u-us3/lib/sparcv9/libc_psr.so.1
	fd                         0       0       0     0%    /dev/fd
	swap                 9791496     224 9791272     1%    /tmp
	swap                 9791320      48 9791272     1%    /var/run
	/dev/md/dsk/d2       243091536 124951186 115709435    52%    /home
	/home/proworks>cd ..
	/home>ls
	lost+found  proworks    svms        svms2       sybase      sybprod15   test
	/home>