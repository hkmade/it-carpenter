#ubuntu desktop install
기본적으로 Host PC 위의 VMware 에 설치를 하는 환경이다. 
32bit. 또는 64bit 어떤 ubuntu OS를 설치할 것인가? VM에 메모리 할당을 3G정도 가능하다면 64bit로 설치하자. 특히 PHP기반의 웹하드 App(Pydio)등을 사용한다면 2G이상의 파일을 업로드/다운로드다운로드하기 위해서는 64bit 설치가 필요하다.

	dpkg -s libc6 | grep Arch
	Multi-Arch: same
	Architecture: amd64

###사전설치Check List
- Ubuntu 홈페이지에서 14.04 LTS Server 버젼을 다운로드한다. iso화일
- VM의 네트웍은 NAT로 진행한다. (즉 Host PC에서 DHCP로 VM에 네트웍을 할당) 

###VM 설치
- Vmware의 File - New Virtual Machine 
- Typical (recommended) - Installer disc image file(iso)
- Download 한 iso화일을 추가한다. 
- Personalize Linux : 개인정보 입력. 로그인 계정
- Virual Server name, VM이 화일단위로 저장되는 위치 지정
- Specify Disk Capacity : disk size 와 Split virtual disk into multiple files 선택
- Ready to Create Virtual Machine : Customize Hardware
- Memory는 3G정도로 할당.
- Finish

###Ubuntu 14.04 install
언어설정은 기본값인 US와 ENGLISH로 설치한다. 
그외는 모두 기본값으로 설정

###네트웍 확인
VM 설치 이후 Host PC가 인터넷에 연결되어 있다면 NAT모드로 설정되어 있으므로 VM도 DHCP로 아이피가 할당되어 인터넷이 연결이 가능.



###Timezone check
date명령어로  PDT(태평양표준시)로 설정되어 있는 경우 서울시간대로 변경 필요.
 
 	root@ubuntu:/etc# date
	Sun Mar 10 04:21:37 PDT 2013
	root@ubuntu:/etc#

아래의 디렉토리로 이동  

	root@ubuntu:/usr/share/zoneinfo# cd Asia
	root@ubuntu:/usr/share/zoneinfo/Asia#
	root@ubuntu:/usr/share/zoneinfo/Asia# ls -la Seoul
	-rw-r--r-- 1 root root 380 Sep  6  2012 Seoul
	root@ubuntu:/usr/share/zoneinfo/Asia#   
	
 기존의 timezone화일 삭제 후 심볼릭 링크를 새로 설정  
 
	root@ubuntu:/usr/share/zoneinfo/Asia# ln -sf /usr/share/zoneinfo/Asia/Seoul /etc/localtime
	root@ubuntu:/usr/share/zoneinfo/Asia#
	
//ln -sf명령은 심볼릭 링크(일종의 단축키 만들기)이며 기존에 tiemzone 화일을 삭제하고 강제로 새로운 파일을 만드는 옵션이다. 

	root@ubuntu:/etc/init.d# date
	Sun Mar 10 20:37:11 KST 2013

#rdate
최초 ubuntu 설치 언어를 한국어로 한다면 이과정은 필요없음.  

	hkmade@ubuntu:/var/log/apache2$ sudo apt-get install rdate
	[sudo] password for hkmade:
	Reading package lists... Done
	Building dependency tree
	Reading state information... Done
	The following packages were automatically installed and are no longer required:
	  linux-headers-3.5.0-17 linux-headers-3.5.0-17-generic
	Use 'apt-get autoremove' to remove them.
	.
	Unpacking rdate (from .../rdate_1%3a1.2-5_i386.deb) ...
	Processing triggers for man-db ...
	Setting up rdate (1:1.2-5) ...

#한글세팅
최초 ubuntu 설치 언어를 한국어로 한다면 이과정은 필요없음.  

	root@ubuntu:/etc/default# locale-gen ko_KR.EUC-KR
	Generating locales...
	  ko_KR.EUC-KR... up-to-date
	Generation complete.
	root@ubuntu:/etc/default# locale-gen ko_KR.UTF-8
	Generating locales...
	  ko_KR.UTF-8... up-to-date
	Generation complete.

	root@ubuntu:~# env | grep LANG
	LANG=en_US.UTF-8
	root@ubuntu:~#
	root@ubuntu:~# more /etc/default/locale
	LANG="en_US.UTF-8"
	root@ubuntu:~#vi /etc/default/locale
	ko_KR.UTF-8로 변경할것.
	root@ubuntu:~# env | grep LANG
	LANG=ko_KR.UTF-8
	root@ubuntu:~#

리부팅후 적용.

###FTP설치
제일 무난한 ftp 프로그램인 vsftpd  
`sudo apt-get install vsftpd`   
`cd /etc ; vi vsftpd.conf`
anonymus_enable=NO  
locale_enable=YES  
두개 항목이 위와 같이 설정되어 있는지 확인할것.
또한 write_enable 주석 해제 후 프로세스 재기동

	hkmade@ubuntu:/etc$ vi vfspd.conf
	hkmade@ubuntu:/etc$ vi vsftpd.conf
	hkmade@ubuntu:/etc$ sudo -i
	[sudo] password for hkmade:
	root@ubuntu:~# vi /etc/vsftpd.conf
	root@ubuntu:~# /etc/init.d/vsftpd restart
	Rather than invoking init scripts through /etc/init.d, use the service(8)
	utility, e.g. service vsftpd restart

	Since the script you are attempting to invoke has been converted to an
	Upstart job, you may also use the stop(8) and then start(8) utilities,
	e.g. stop vsftpd ; start vsftpd. The restart(8) utility is also available.
	vsftpd stop/waiting
	vsftpd start/running, process 10881



###SSH
`sudo apt-get install ssh`  
기본적으로 시스템설치는 sudo명령을 통해 root 권한으로 설치할것. 최초 OS설치시 설정한 사용자아이디와 패스워드가 root계정 패스워드로 사용된다.
인터넷으로 연결된 상태에서 우분투서버를 통해 필요한 패키지를 설치한다 

####기본포트변경
`sudo vi /etc/ssh/sshd_config`  
항상 강조하지만  왠만한 시스템 작업은 sudo를 통한 어드민권한으로의 접근으로 진행하자. 
`Port 22` 부분에서 포트번호를 수정한다.   
`/etc/init.d/ssh restart` 

###공유기 설정.외부접근을 위한 포트포워딩
연결된 공유기의 포트포워딩을 설정.  
`http://111.222.333.33:yyyy   --> 192.168.xxx.xxx:yyyy`  
이건 외부에서 공유기 접근할때의 정보임. 

#Hostname 변경
/etc/hostname  값 변경
hostname -F /etc/hostname


###외부에서 원격 세션 접근(telnet)
무료유틸리티인 iTerm을 이용하자.
Preference 오픈
프로파일을 추가.   
Name: SSH telnet my Ubuntu
Shutcut key: 원하는 단축키 설정
Command : ssh -p yyyy ID@111.222.333.33
Terminal 파트에서 Character Encoding : Unicode(UTF-8) 확인

이후 작업은 텔넷으로 진행.

SecureCRT에서는 SSH2 프로토콜로 변경은 포트는 지정한 포트로 변경



###Apache2 설치
	hkmade@ubuntu:~$ sudo -i  
	[sudo] password for hkmade:  
	root@ubuntu:~# apt-get install apache2  
	Reading package lists... Done  
	.
	.
	Setting up apache2-mpm-worker (2.2.22-6ubuntu2.1) ...
	* Starting web server apache2                                               
	* apache2: Could not reliably determine the server's fully qualified domain name, using 127.0.1.1 for ServerName
	[ OK ]
	Setting up apache2 (2.2.22-6ubuntu2.1) ...
	Processing triggers for libc-bin ...
	ldconfig deferred processing now taking place
	root@ubuntu:~# 
	root@ubuntu:~#/etc/apache2/ports.conf
공유기 설정에서 포트포워딩 추가 할것. 만약 인터넷 서비스의 제한으로 80포트를 사용할수 없는 경우 다른 포트로 웹서비스가 필요. 
다른 포트를  웹서비스 포트로 사용한다면 위의 ports.conf에서 조정한다. 

http://xxx.xxx.xxx.xx를 통해 홈페이지 확인 할 것. 
설치후 기본 htdocs(홈페이지root위치)는 /var/www/html로 지정되어 있다. (Ubuntu 14.04의 경우)
기존대로 /var/www로 변경하고자 한다면
/etc/apache2/sites-enabled/000-default.conf 화일에서 DocumentRoot에서 변경한다. 


###Mysql
mysql root 패스워드는 설치 중간 창에서 설정한다. 

	hkmade@ubuntu:~$ sudo -i
	[sudo] password for hkmade:
	root@ubuntu:~# ls
	root@ubuntu:~# apt-get install mysql-server
	Reading package lists... Done
	Building dependency tree
	Reading state information... Done
	The following packages were automatically installed and are no longer required:
	  linux-headers-3.5.0-17 linux-headers-3.5.0-17-generic
	Use 'apt-get autoremove' to remove them.
	The following extra packages will be installed:
	  libaio1 libdbd-mysql-perl libdbi-perl libhtml-template-perl libmysqlclient18 libnet-daemon-perl libplrpc-perl
	  libterm-readkey-perl mysql-client-5.5 mysql-client-core-5.5 mysql-common mysql-server-5.5
	  mysql-server-core-5.5
	Suggested packages:
	  libipc-sharedcache-perl tinyca mailx
	The following NEW packages will be installed:
	  libaio1 libdbd-mysql-perl libdbi-perl libhtml-template-perl libmysqlclient18 libnet-daemon-perl libplrpc-perl
	  libterm-readkey-perl mysql-client-5.5 mysql-client-core-5.5 mysql-common mysql-server mysql-server-5.5
	  mysql-server-core-5.5
	0 upgraded, 14 newly installed, 0 to remove and 0 not upgraded.
	Need to get 26.6 MB of archives.
	After this operation, 92.6 MB of additional disk space will be used.
	Do you want to continue [Y/n]? y
	Get:1 http://us.archive.ubuntu.com/ubuntu/ quantal/main libaio1 i386 0.3.109-2ubuntu1 [6,648 B]
	.
	.
	password 설정. (mysql root passwd)
	mysql start/running, process 6227
	Setting up libhtml-template-perl (2.91-1) ...
	Processing triggers for ureadahead ...
	Setting up mysql-server (5.5.29-0ubuntu0.12.10.1) ...
	Processing triggers for libc-bin ...
	ldconfig deferred processing now taking place

루트로 로그인 후 pat-get 명령을 통해h 패키지로 설치한다. netstat -tab명령으로 실제 서비스를 제공하고 있는지 확인  

	root@ubuntu:~# netstat -tap | grep mysql
	tcp        0      0 localhost:mysql         *:*                     LISTEN      6227/mysqld

mysql 설정화일은 my.cnf이며 포트와 로그 화일등을 설정할 수 있다. 

	root@ubuntu:~# vi /etc/mysql/my.cnf

###PHP5
	hkmade@ubuntu:~$ sudo -i
	[sudo] password for hkmade:
	root@ubuntu:~# sudo apt-get install libapache2-mod-php5
	Reading package lists... Done
	Building dependency tree
	Reading state information... Done
	The following packages were automatically installed and are no longer required:
	  linux-headers-3.5.0-17 linux-headers-3.5.0-17-generic
	Use 'apt-get autoremove' to remove them.
	The following extra packages will be installed:
	  apache2-mpm-prefork php5-cli php5-common
	Suggested packages:
	  php-pear
	The following packages will be REMOVED:
	  apache2-mpm-worker
	The following NEW packages will be installed:
	  apache2-mpm-prefork libapache2-mod-php5 php5-cli php5-common
	0 upgraded, 4 newly installed, 1 to remove and 0 not upgraded.
	.
	.
	Do you want to continue [Y/n]? y
	Get:1 http://us.archive.ubuntu.com/ubuntu/ quantal-updates/main apache2-mpm-prefork i386 2.2.22-6ubuntu2.1 [2,360 B]
	.
	dpkg: apache2-mpm-worker: dependency problems, but removing anyway as you requested:
	 apache2 depends on apache2-mpm-worker (= 2.2.22-6ubuntu2.1) | apache2-mpm-prefork (= 2.2.22-6ubuntu2.1) | apache2-mpm-		event (= 2.2.22-6ubuntu2.1) | apache2-mpm-itk (= 2.2.22-6ubuntu2.1); however:
	  Package apache2-mpm-worker is to be removed.
	  Package apache2-mpm-prefork is not installed.
	  Package apache2-mpm-event is not installed.
	  Package apache2-mpm-itk is not installed.
	 * Stopping web server apache2
	 apache2: Could not reliably determine the server's fully qualified 		domain name, using 127.0.1.1 for ServerName
	.. waiting                                                                                                            [ OK ]
	Selecting previously unselected package apache2-mpm-prefork.
	(Reading database ... 182857 files and directories currently installed.)
	Unpacking apache2-mpm-prefork (from .../apache2-mpm-prefork_2.2.22-6ubuntu2.1_i386.deb) ...
	Setting up apache2-mpm-prefork (2.2.22-6ubuntu2.1) ...
 	* Starting web server apache2   
	apache2: Could not reliably determine the server's fully qualified 		domain name, using 127.0.1.1 for ServerName
                                                                                                                        [ OK ]
	
apache에서 php모듈이 활성화되었는지 다시 확인.


	root@ubuntu:~# sudo a2enmod php5
	Module php5 already enabled
	root@ubuntu:~#

php기반 웹스토리지 프로그램을 이용할때 upload, download size의 제약이 있다. 이건 php의 설정에 좌우되므로 수정한 후 apache2 프로세스를 재기동 할것.
우분투에서는 아래의 경로에 php설정화일이 존재한다. 
기본적으로 32bit OS레벨에서는 2GB이상의 size는 불가능하다.  
변경해야할 factor는 아래와 같다.  

	memory_limit
	post_max_size
	upload_max_filesize

	root:/etc/php5/apache2/php.ini

###pear 설치
pear (PHP Extension and Application Repository)
	root@ubuntu:/var/www# apt-get install php-pear
	Reading package lists... Done
	Building dependency tree
	Reading state information... Done
	The following packages were automatically installed and are no longer required:
  	linux-headers-3.5.0-17 linux-headers-3.5.0-17-generic
	Use 'apt-get autoremove' to remove them.
	Suggested packages:
  	php5-dev
	The following NEW packages will be installed:
  	php-pear
	.
	Unpacking php-pear (from .../php-pear_5.4.6-1ubuntu1.1_all.deb) ...
	Setting up php-pear (5.4.6-1ubuntu1.1) ...
	root@ubuntu:/var/www#


###PHP설치 확인
기본 apache 서버의 htdocs인 /var/www 디렉토리 아래에 test.php화일 생성 후 아래 내용 기입  
`<?php phpinfo(); ?>`  
http://221.122.xx.xx/test.php  실행 후 정상적인 출력이 되는지 확인.


###PHPmyadmin 설치
	root@ubuntu:/var/www# apt-get install phpmyadmin
	.
	The following extra packages will be installed:
	  dbconfig-common libmcrypt4 php5-gd php5-mcrypt php5-mysql
	Suggested packages:
	  libmcrypt-dev mcrypt
	The following NEW packages will be installed:``
	  dbconfig-common libmcrypt4 php5-gd php5-mcrypt php5-mysql phpmyadmin
	0 upgraded, 6 newly installed, 0 to remove and 0 not upgraded.
	Do you want to continue [Y/n]? y
	.
	Processing triggers for libapache2-mod-php5 ...
	 * Reloading web server config                                                  
	apache2: Could not reliably determine the server's fully qualified domain name, using 	127.0.1.1 for ServerName
	[ OK ]
	Creating config file /etc/dbconfig-common/config with new version
	Setting up libmcrypt4 (2.5.8-3.1) ...
	Setting up php5-mcrypt (5.4.6-0ubuntu1) ...
	Processing triggers for libapache2-mod-php5 ...
	* Reloading web server config
	apache2: Could not reliably determine the server's fully qualified domain name, using 127.0.1.1 for ServerName
	[ OK ]
	Setting up phpmyadmin (4:3.4.11.1-1) ...
	dbconfig-common: writing config to /etc/dbconfig-common/phpmyadmin.conf
	Creating config file /etc/dbconfig-common/phpmyadmin.conf with new version
	Creating config file /etc/phpmyadmin/config-db.php with new version
 	* Reloading web server config
	apache2: Could not reliably determine the server's fully qualified domain name, using 127.0.1.1 for ServerName
	[ OK ]
	Processing triggers for libc-bin ...
	root@ubuntu:/var/www#

중간 apache 자동모듈설정부분에서는 스페이스바로 apache를 선택하고 OK
Configuration phpmyadmin부분에서는 mysql를 설치하고 root비번을 설정한 상태라면.. no로 선택
**http://xxx.xxx.xxx.xx/phpmyadmin**   으로 접근시 로그인 화면이 나오면 OK.

2013.03현재 phpMyAdmin은 3.4.11.1 버젼.  appearance부분에서 한글은 지원하지 않는다.
한글 문자셋의 충돌을 막기위해서 우분투서버는 utf-8, phpMyadmin에서 보여지는 
General Settings - MySQL connection collation은  utf8_general_ci로 설정되어 있음을 확인한다.















#IP 수동할당
이제 기본값으로 설치된 ubuntu에서 VM의 네트웍 설정을 기본값인 NAT에서 Bridged mode로 변경. (HOST PC와 별도로 네트웍을 독자적으로 구성.) 왜냐하면 공유기에서 HostPC와는 별도로 아이피를 할당받아야  외부에서 접근이 가능하다.   

- 기존 VM1은 192.168.25.20
- Host PC는 192.168.25.45
- VM2는 192.168.25.30 으로 설정할 것.

command line에서 network 선택

- Edit Connections.. 
- Wired - Wired connection1 -> edit
- IPv4 Settings
- Method - Manual
- Address, Netmask, Gateway, DNS Server 입력  

적용 후 Establised 된 아이콘 확인.   
참고로 터니널 창은 시작버튼- 검색에서 terminal 입력

###네트웍 패킷 트래픽
iptraf 프로그램은 기본적으로 설치되어 있지 않으며 apt-get으로 따로 설치할 것.

	root@ubuntu:~#apt-get install iptraf
	root@ubuntu:~#apt-get install wireshark
	




#DIY NAS App설치
###Ajaxplorer
웹하드 인터페이스를 구현한 오픈소스 프로젝트
홈페이지. http://pyd.io
2013.03현재 stable version은 4.2.3
2014.03현재 5.2.2
apt-get install 을 이용해서 편리하게 설치(는 안됨.)

site에서 download후 해당 웹서버에 업로드

	root@ubuntu:/home/hkmade# ls
	ajaxplorer-core-4.2.3.tar.gz  Documents  examples.desktop  Pictures  Templates
	Desktop                       Downloads  Music             Public    Videos
	root@ubuntu:/home/hkmade# gunzip *.gz
	root@ubuntu:/home/hkmade# ls
	ajaxplorer-core-4.2.3.tar  Documents  examples.desktop  Pictures  Templates
	Desktop                    Downloads  Music             Public    Videos
	root@ubuntu:/home/hkmade# mv *.tar /var/www
	root@ubuntu:/home/hkmade# cd /var/www
	root@ubuntu:/home/hkmade# tar xvf ajax*.tar 
	root@ubuntu:/var/www# ls -la
	total 15580
	drwxr-xr-x  3 root   root       4096 Mar  7 20:29 .
	drwxr-xr-x 15 root   root       4096 Mar  7 06:13 ..
	drwxr-xr-x  6 root   root       4096 Mar  7 20:29 ajaxplorer-core-4.2.3
	-rw-------  1 hkmade hkmade 15932416 Mar  7 20:25 ajaxplorer-core-4.2.3.tar
	-rw-r--r--  1 root   root        177 Mar  7 06:13 index.html
	-rw-r--r--  1 root   root         20 Mar  7 17:35 test.php
	root@ubuntu:/var/www#rm aja*.tar
	root@ubuntu:/var/www# mv ajaxplorer-core-4.2.3 ajaxp
	root@ubuntu:/var/www# ls -la
	total 20
	drwxr-xr-x  3 root root 4096 Mar  7 20:31 .
	drwxr-xr-x 15 root root 4096 Mar  7 06:13 ..
	drwxr-xr-x  6 root root 4096 Mar  7 20:29 ajaxp
	-rw-r--r--  1 root root  177 Mar  7 06:13 index.html
	-rw-r--r--  1 root root   20 Mar  7 17:35 test.php
	root@ubuntu:/var/www#
	
/var/www에서 해당 화일의 압축을 풀고 http://주소/ajaxp 로 접근하기 위해 디렉토리이름을 mv명령어로 변경함.

###AjaXplorer 설정
http://221.148.161.172/ajaxp 로 웹브라우져 접근
AjaXplorer Diagnostic Tool화면이 나옴.  
이제 디렉토리 권한 설정할 차례  

	root@ubuntu:/var/www# chown -R www-data:www-data ajaxp
	root@ubuntu:/var/www# ls -la
	total 20
	drwxr-xr-x  3 root     root     4096 Mar  7 20:31 .
	drwxr-xr-x 15 root     root     4096 Mar  7 06:13 ..
	drwxr-xr-x  6 www-data www-data 4096 Mar  7 20:29 ajaxp
	-rw-r--r--  1 root     root      177 Mar  7 06:13 index.html
	-rw-r--r--  1 root     root       20 Mar  7 17:35 test.php
	root@ubuntu:/var/www#
	
		
apache config 에서 /var/www/ajaxp/data 영역을 Override ALL로 설정.

	/etc/apache2/apache2.conf 에서 추가
	<Directory "/var/www/pydio/data">
        AllowOverride All
	</Directory>

Ajajxp5부터는 wizard방식으로 기본 설정 진행.
최초 언어는 한국어로 설정. 
Start wizard
Admin Access 계정설정
Global options  Default langae -한국어
Configurations storage 지점은 빠른속도를 위해서는 No Database

설치 이후 upload 파일 사이즈를 확인 한다.
Global Configurations - Application Core - Uploaders Options
Limitation의 File Size를 체크한다. php에 설정한대로 4G의 byte수가 할당됨.

대용량 업로드를 위해서는 java 기반의 uploader plugin을 활성화 한다.
이를 위해서 jar화일의 download와 pydio의 plugin 디렉토리로의 upload가 필요

download jar file
http://pyd.io/plugins/uploader/jumploader - 설치가이드

http://jumploader.com 에서 jar 화일 download
jumploader_z.jar 화일 선택후 download
pyd.io의 jumploader 폴더에 해당 jar화일 이동

	root:/var/www/pydio/plugins# cd *jumploader
	root:/var/www/pydio/plugins/uploader.jumploader# ls
	class.JumploaderProcessor.php  i18n  jumploader_tpl.html  manifest.xml  plugin_doc.html
	root:/var/www/pydio/plugins/uploader.jumploader# cp /tmp/*.jar .


pydio.io의 Gloabal Configuration - Feature plugins - Uploader에서
기존의 활성화되어 있는 Flash upload, HTML upload 모두 Enable에서 해제.
Jumploader를 활성화 하고 applet install 할것.
저장 후 재로그인.
파일 업로드시 기존의 html방식이 아닌 applet방식으로 upload창이 뜨는지 확인 할것.

단 이방식은 applet기반이기 때문에 uploader의 PC에 JRE이상이 설치되어 있어야 한다. 


###Ubuntu에서 host 디렉토리공유
#### VMware tool 인스톨.
VM이 반드시 기동된 상태에서 Vmware workstation - VM - install VMware Tools
만약 설치시 아래의 메세지가 나온다면 
VMware Tools installation cannot be started manually while the easy install is in progress.
http://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=1017687
위 내용은 핵심은 바로 Floppy dirvie를 auto detect로 설정하는 부분이다. 
VM - Setting - Floppy - Use physical dirive : Auto detect로 설정할 것.

URL참고하여 VMtool 설치할것.
 install VMware Tools 설치.
 로그인 하면 /media 부분에  VMware tools 가 마운트되어 있다. 


	 root@miles-base-ubt02:/media/VMware Tools# ls -la
	합계 60599
	dr-xr-xr-x 2 miles miles     2048  8월 27  2013 .
	drwxr-xr-x 5 root  root      4096  3월  6 16:52 ..
	-r--r--r-- 1 miles miles 60654199  8월 27  2013 VMwareTools-9.6.0-1294478.tar.gz
	-r-xr-xr-x 1 miles miles     1961  8월 27  2013 manifest.txt
	-r--r--r-- 1 miles miles     1847  8월 27  2013 run_upgrader.sh
	-r-xr-xr-x 1 miles miles   689456  8월 27  2013 vmware-tools-upgrader-32
	-r-xr-xr-x 1 miles miles   698376  8월 27  2013 vmware-tools-upgrader-64
	root@miles-base-ubt02:/media/VMware Tools#

read only media 폴더에서  /tmp폴더로 해당 gz화일을 복사한 후 압축해제한다.
 
 root@miles-base-ubt02:/tmp/vmware-tools-distrib# ls
FILES  INSTALL  bin  doc  etc  installer  lib  vmware-install.pl
root@miles-base-ubt02:/tmp/vmware-tools-distrib# ./vmware-install.pl

매우 길게 설치작업이 진행되며.  선택값은 모두 엔터. (기본값으로 충분)
이 VMware tool을 설치해야 하는 이유는 host PC의 파일시스템의 공유기능을 이용하기 위해서이다. 

설치완료후 hgfs가 활성화 된 것을 확인할 것.

	root@miles-base-ubt02:~# cd /mnt
	root@miles-base-ubt02:/mnt# ls
	hgfs
	root@miles-base-ubt02:/mnt#


이제 마운트 포인트를 만들고 /etc/fstab을 통해 부팅후 자동으로 마운트할수 있도록 설정 추가. 마친 후 시스템 리부팅

VM 메뉴에서 Options - SHared Folders 에서 등록할것.


	root@ubuntu:/mnt/hgfs# ls -la
	total 8
	drwxr-xr-x 2 root root 4096  3월 20 10:08 .
	drwxr-xr-x 3 root root 4096  3월 20 10:08 ..
	root@ubuntu:/mnt/hgfs# mkdir /nas
	root@ubuntu:/mnt/hgfs# vi /etc/fstab
		.host:/TMS	/nas	vmhgfs defaults,ttl=5,uid=1000,gid=1000 0 0  // 라인을 추가한다.

	root@ubuntu:/mnt/hgfs# reboot
	root@ubuntu:/mnt/hgfs#
	Broadcast message from hkmade@ubuntu
		(/dev/pts/0) at 10:10 ...
	The system is going down for reboot NOW!	
	root@ubuntu:~#
	....
	....
	root@ubuntu:~# df -k
	Filesystem      1K-blocks      Used  Available Use% Mounted on
	/dev/sda1        19609276   3800064   14813116  21% /	
	udev               505560         4     505556   1% /dev
	tmpfs              205324       804     204520   1% /run
	none                 5120         0       5120   0% /run/lock
	none               513304       152     513152   1% /run/shm
	none               102400        36     102364   1% /run/user
	.host:/NAS     1953381372 828871844 1124509528  43% /nas
	root@ubuntu:~#
	
	
###AFP설치
Apple Filing Protocol 설치. 
MAC OS에서 원격지 폴더를 마운트하여 사용이 가능.
응용범위. 타임머신 백업, iPhoto라이브러리 공유. iTunes뮤직서버. 
2013.03현재 AFP를 지원하는 Netatalk 프로토콜은 3.0.2.

우선 apt-get list 업데이트 및 호환성 업데이트가 반드시 필요. 호환성 업데이트를 하지 않으면 신버젼의 Netatalk가 설치되지 않음.
	
	hkmade@ubuntu:~$ sudo -i
	[sudo] password for hkmade:
	root@ubuntu:~#
	root@ubuntu:~#
	root@ubuntu:~# apt-get update
	무시 http://extras.ubuntu.com quantal InRelease
	받기:1 http://extras.ubuntu.com quantal Release.gpg [72 B]
	무시 http://us.archive.ubuntu.com quantal InRelease
	무시 http://security.ubuntu.com quantal-security InRelease
	기존 http://extras.ubuntu.com quantal Release
	기존 http://extras.ubuntu.com quantal/main Sources
	무시 http://us.archive.ubuntu.com quantal-updates InRelease
	받기:2 http://security.ubuntu.com quantal-security Release.gpg [933 B]
	기존 http://extras.ubuntu.com quantal/main i386 Packages
	무시 http://us.archive.ubuntu.com quantal-backports InRelease
	받기:3 http://security.ubuntu.com quantal-security Release [49.6 kB]
	기존 http://us.archive.ubuntu.com quantal Release.gpg
	받기:4 http://us.archive.ubuntu.com quantal-updates Release.gpg [933 B]
	....
	....

	무시 http://us.archive.ubuntu.com quantal-backports/restricted Translation-ko
	무시 http://us.archive.ubuntu.com quantal-backports/universe Translation-ko_KR
	무시 http://us.archive.ubuntu.com quantal-backports/universe Translation-ko
	내려받기 866 k바이트, 소요시간 1분 43초 (8,385 바이트/초)
	패키지 목록을 읽는 중입니다... 완료
	root@ubuntu:~#	  


이제 의존성 업데이트를 해주자

	root@ubuntu:~#
	root@ubuntu:~#
	root@ubuntu:~# ls
	root@ubuntu:~# apt-get build-dep netatalk
	패키지 목록을 읽는 중입니다... 완료
	의존성 트리를 만드는 중입니다
	상태 정보를 읽는 중입니다... 완료
	주의, 'libltdl3-dev' 대신에 'libltdl-dev' 패키지를 선택합니다
	다음 새 패키지를 설치할 것입니다:
	  autoconf automake autotools-dev build-essential cdbs comerr-dev d-shlibs debhelper devscripts dh-apparmor
	  dh-buildinfo dh-translations dpkg-dev g++ g++-4.7 html2text intltool krb5-multidev libacl1-dev libattr1-dev
	  libavahi-client-dev libavahi-common-dev libcrack2-dev libcups2-dev libdb-dev libdb5.1-dev libdbus-1-dev
	  libencode-locale-perl libfile-listing-perl libgcrypt11-dev libgnutls-dev libgnutls-openssl27 libgnutlsxx27
	  libgpg-error-dev libgssrpc4 libhtml-parser-perl libhtml-tagset-perl libhtml-tree-perl libhttp-cookies-perl
	  libhttp-date-perl libhttp-message-perl libhttp-negotiate-perl libio-socket-ssl-perl libkadm5clnt-mit8
	  libkadm5srv-mit8 libkdb5-6 libkrb5-dev libldap2-dev libltdl-dev liblwp-mediatypes-perl
	  liblwp-protocol-https-perl libnet-http-perl libnet-ssleay-perl libp11-kit-dev libpam0g-dev libstdc++6-4.7-dev
	  libtasn1-3-dev libwrap0-dev libwww-perl libwww-robotrules-perl libxml-parser-perl m4 po-debconf python-scour
	  zlib1g-dev
	0개 업그레이드, 65개 새로 설치, 0개 제거 및 39개 업그레이드 안 함.
	18.8 M바이트 아카이브를 받아야 합니다.
	이 작업 후 54.0 M바이트의 디스크 공간을 더 사용하게 됩니다.
	계속 하시겠습니까 [Y/n]? y
	받기:1 http://us.archive.ubuntu.com/ubuntu/ quantal-updates/main libgnutls-openssl27 i386 2.12.14-5ubuntu4.2 [21.6 kB]
	받기:2 http://us.archive.ubuntu.com/ubuntu/ quantal/main libgssrpc4 i386 1.10.1+dfsg-2 [57.5 kB]
	받기:3 http://us.archive.ubuntu.com/ubuntu/ quantal/main libkadm5clnt-mit8 i386 1.10.1+dfsg-2 [38.7 kB]
	받기:4 http://us.archive.ubuntu.com/ubuntu/ quantal/main libkdb5-6 i386 1.10.1+dfsg-2 [37.8 kB]
	받기:5 http://us.archive.ubuntu.com/ubuntu/ quantal/main libkadm5srv-mit8 i386 1.10.1+dfsg-2 [55.1 kB]
	받기:6 http://us.archive.ubuntu.com/ubuntu/ quantal-updates/main libgnutlsxx27 i386 2.12.14-5ubuntu4.2 [23.4 kB]
	받기:7 http://us.archive.ubuntu.com/ubuntu/ quantal/main m4 i386 1.4.16-3 [200 kB]
	받기:8 http://us.archive.ubuntu.com/ubuntu/ quantal/main autoconf all 2.69-1ubuntu1 [568 kB]
	....
	....
	받기:64 http://us.archive.ubuntu.com/ubuntu/ quantal/main libdb-dev i386 5.1.6 [2,294 B]
	받기:65 http://us.archive.ubuntu.com/ubuntu/ quantal/main libldap2-dev i386 2.4.31-1ubuntu2 [509 kB]
	내려받기 18.8 M바이트, 소요시간 4분 4초 (77.1 k바이트/초)
	패키지에서 템플릿을 추출하는 중: 100%
	Selecting previously unselected package libgnutls-openssl27:i386.
	(데이터베이스 읽는중 ...현재 185161개의 파일과 디렉터리가 설치되어 있습니다.)
	libgnutls-openssl27:i386 패키지를 푸는 중입니다 (.../libgnutls-openssl27_2.12.14-5ubuntu4.2_i386.deb에서) ...
	Selecting previously unselected package libgssrpc4:i386.
	....
	....
	cdbs (0.4.111ubuntu1) 설정하는 중입니다 ...
	dh-buildinfo (0.9+nmu1ubuntu1) 설정하는 중입니다 ...
	libc-bin에 대한 트리거를 처리하는 중입니다 ...
	ldconfig deferred processing now taking place
	root@ubuntu:~#

	netatalk socecode download
	root@ubuntu:/tmp# wget http://sourceforge.net/projects/netatalk/files/netatalk/3.0.2/netatalk-3.0.2.tar.gz
	--2013-03-20 15:18:34--  http://sourceforge.net/projects/netatalk/files/netatalk/3.0.2/netatalk-3.0.2.tar.gz
	Resolving sourceforge.net (sourceforge.net)... 216.34.181.60
	Connecting to sourceforge.net (sourceforge.net)|216.34.181.60|:80... connected.
	HTTP request sent, awaiting response... 302 Found
	Location: http://sourceforge.net/projects/netatalk/files/netatalk/3.0.2/netatalk-3.0.2.tar.gz/download [following]
	--2013-03-20 15:18:36--  http://sourceforge.net/projects/netatalk/files/netatalk/3.0.2/netatalk-3.0.2.tar.gz/download
	Connecting to sourceforge.net (sourceforge.net)|216.34.181.60|:80... connected.
	HTTP request sent, awaiting response... 302 Found
	Resolving downloads.sourceforge.net (downloads.sourceforge.net)... 216.34.181.59
	Connecting to downloads.sourceforge.net (downloads.sourceforge.net)|216.34.181.59|:80... connected.
	HTTP request sent, awaiting response... 302 Found
	Location: http://jaist.dl.sourceforge.net/project/netatalk/netatalk/3.0.2/netatalk-3.0.2.tar.gz [following]
	--2013-03-20 15:18:38--  http://jaist.dl.sourceforge.net/project/netatalk/netatalk/3.0.2/netatalk-3.0.2.tar.gz
	Resolving jaist.dl.sourceforge.net (jaist.dl.sourceforge.net)... 150.65.7.130, 2001:200:141:feed::feed
	Connecting to jaist.dl.sourceforge.net (jaist.dl.sourceforge.net)|150.65.7.130|:80... connected.
	HTTP request sent, awaiting response... 200 OK
	Length: 2137249 (2.0M) [application/x-gzip]
	Saving to: `netatalk-3.0.2.tar.gz'

	100%[=======================================================================>] 2,137,249    405K/s   in 5.9s

	2013-03-20 15:18:45 (353 KB/s) - `netatalk-3.0.2.tar.gz' saved [2137249/2137249]

	root@ubuntu:/tmp#
	root@ubuntu:/tmp# gunzip *.gz
	root@ubuntu:/tmp# tar xvf neta*.tar
	netatalk-3.0.2/
	netatalk-3.0.2/Makefile.am
	netatalk-3.0.2/config/
	....
	....
	netatalk-3.0.2/test/afpd/subtests.c
	netatalk-3.0.2/test/afpd/test.sh
	netatalk-3.0.2/test/afpd/subtests.h
	netatalk-3.0.2/test/afpd/afpfunc_helpers.c
	root@ubuntu:/tmp#
	
	root@ubuntu:/tmp/netatalk-3.0.2# ./configure --help
	`configure' configures this package to adapt to many kinds of systems.
	
	Usage: ./configure [OPTION]... [VAR=VALUE]...
	
	To assign environment variables (e.g., CC, CFLAGS...), specify them as
	VAR=VALUE.  See below for descriptions of some of the useful variables.
	
	Defaults for the options are specified in brackets.
	
	Configuration:
	  -h, --help              display this help and exit
	      --help=short        display options specific to this package
	      --help=recursive    display the short help of all the included packages
	       -V, --version           display version information and exit
	  -q, --quiet, --silent   do not print `checking ...' messages
	      --cache-file=FILE   cache test results in FILE [disabled]
	  -C, --config-cache      alias for `--cache-file=config.cache'
	  -n, --no-create         do not create output files
	      --srcdir=DIR        find the sources in DIR [configure dir or `..']
	
	Installation directories:
	  --prefix=PREFIX         install architecture-independent files in PREFIX
	                          [/usr/local]
	  --exec-prefix=EPREFIX   install architecture-dependent files in EPREFIX
	                          [PREFIX]
	
	By default, `make install' will install all the files in
	`/usr/local/bin', `/usr/local/lib' etc.  You can specify
	an installation prefix other than `/usr/local' using `--prefix',
	for instance `--prefix=$HOME'.
	
	For better control, use the options below.
	
	Fine tuning of the installation directories:
	  --bindir=DIR            user executables [EPREFIX/bin]
	  --sbindir=DIR           system admin executables [EPREFIX/sbin]
	  --libexecdir=DIR        program executables [EPREFIX/libexec]
	  --sysconfdir=DIR        read-only single-machine data [PREFIX/etc]
	  --sharedstatedir=DIR    modifiable architecture-independent data [PREFIX/com]
	  --localstatedir=DIR     modifiable single-machine data [PREFIX/var]
	  --libdir=DIR            object code libraries [EPREFIX/lib]
	  --includedir=DIR        C header files [PREFIX/include]
	  --oldincludedir=DIR     C header files for non-gcc [/usr/include]
	  --datarootdir=DIR       read-only arch.-independent data root [PREFIX/share]
	  --datadir=DIR           read-only architecture-independent data [DATAROOTDIR]
	  --infodir=DIR           info documentation [DATAROOTDIR/info]
	  --localedir=DIR         locale-dependent data [DATAROOTDIR/locale]
	  --mandir=DIR            man documentation [DATAROOTDIR/man]
	  --docdir=DIR            documentation root [DATAROOTDIR/doc/PACKAGE]
	  --htmldir=DIR           html documentation [DOCDIR]
	  --dvidir=DIR            dvi documentation [DOCDIR]
	  --pdfdir=DIR            pdf documentation [DOCDIR]
	  --psdir=DIR             ps documentation [DOCDIR]
	
	Program names:
	  --program-prefix=PREFIX            prepend PREFIX to installed program names
	  --program-suffix=SUFFIX            append SUFFIX to installed program names
	  --program-transform-name=PROGRAM   run sed PROGRAM on installed program names
	
	System types:
	  --build=BUILD     configure for building on BUILD [guessed]
	  --host=HOST       cross-compile to build programs to run on HOST [BUILD]
	  --target=TARGET   configure for building compilers for TARGET [HOST]
	
	Optional Features:
	  --disable-option-checking  ignore unrecognized --enable/--with options
	  --disable-FEATURE       do not include FEATURE (same as --enable-FEATURE=no)
	  --enable-FEATURE[=ARG]  include FEATURE [ARG=yes]
	  --disable-maintainer-mode  disable make rules and dependencies not useful
				  (and sometimes confusing) to the casual installer
	  --disable-dependency-tracking  speeds up one-time build
	  --enable-dependency-tracking   do not reject slow dependency extractors
	  --enable-shared[=PKGS]  build shared libraries [default=yes]
	  --enable-static[=PKGS]  build static libraries [default=yes]
	  --enable-fast-install[=PKGS]
	                          optimize for fast installation [default=yes]
	  --disable-libtool-lock  avoid locking (might break parallel builds)
	  --disable-largefile     omit support for large files
	  --disable-admin-group   disable admin group
	  --enable-afs            enable AFS support
	  --enable-debug          enable verbose debug code
	  --enable-debugging      disable SIGALRM timers and DSI tickles (eg for debugging with gdb/dbx/...)
	  --enable-quota           Turn on quota support (default=auto)
	  --enable-zeroconf[=DIR]   enable Zeroconf support [auto]
	  --disable-tcp-wrappers  disable TCP wrappers support
	  --disable-shell-check   disable checking for a valid shell
	  --enable-pgp-uam        enable build of PGP UAM module
	  --enable-krbV-uam       enable build of Kerberos V UAM module
	  --enable-overwrite      overwrite configuration files during installation
	  --disable-sendfile       disable sendfile syscall
	  --enable-fhs            use Filesystem Hierarchy Standard (FHS) compatibility
	  --enable-developer      whether to enable developer build (ABI checking)
	  --enable-silent-rules          less verbose build output (undo: `make V=1')
	  --disable-silent-rules         verbose build output (undo: `make V=0')
	
	Optional Packages:
	  --with-PACKAGE[=ARG]    use PACKAGE [ARG=yes]
	  --without-PACKAGE       do not use PACKAGE (same as --with-PACKAGE=no)
	  --with-pic[=PKGS]       try to use only PIC/non-PIC objects [default=use
	                          both]
	  --with-gnu-ld           assume the C compiler uses GNU ld [default=no]
	  --with-sysroot=DIR Search for dependent libraries within DIR
	                        (or the compiler's sysroot if not specified).
	  --with-pkgconfdir=DIR   package specific configuration in DIR
	                          [$sysconfdir]
	  --with-message-dir=PATH path to server message files [$localstatedir/netatalk/msg/]
	  --with-cracklib[=DICT]  enable/set location of cracklib dictionary [no]
	  --with-libiconv=BASEDIR Use libiconv in BASEDIR/lib and BASEDIR/include [default=auto]
	  --with-cnid-dbd-backend       build CNID with Database Daemon Data Store [yes]
	  --with-cnid-cdb-backend	build CNID with Concurrent BDB Data Store  [no]
	  --with-cnid-last-backend	build LAST CNID scheme                     [yes]
	  --with-cnid-tdb-backend	build TDB CNID scheme                      [yes]
	  --with-cnid-default-backend=val	set default DID scheme [dbd]
	  --with-pam[=PATH]       specify path to PAM installation [auto]
	  --with-shadow           enable shadow password support [auto]
	  --with-init-style       use OS specific init config [redhat-sysv|redhat-systemd|suse-sysv|suse-systemd|gentoo|netbsd|debian|solaris|systemd]
	  --with-uams-path=PATH   path to UAMs [$libdir/netatalk/]
	  --with-libgcrypt-dir=PATH
	                          path where LIBGCRYPT is installed (optional). Must
	                          contain lib and include dirs.
	  --with-ssl-dir=PATH     specify path to OpenSSL installation (must contain
	                          lib and include dirs)
	  --with-bdb=PATH         specify path to Berkeley DB installation[auto]
	  --with-gssapi[=PATH]    path to GSSAPI for Kerberos V UAM [auto]
	  --with-kerberos         Kerberos 5 support (default=auto)
	  --with-ldap             LDAP support (default=auto)
	  --with-acls             Include ACL support (default=auto)
	  --with-libevent         whether to use the bundled libevent (default: yes)
	  --with-libevent-header  path to libevent header files
	  --with-libevent-lib     path to libevent library
	
	Some influential environment variables:
	  CC          C compiler command
	  CFLAGS      C compiler flags
	  LDFLAGS     linker flags, e.g. -L<lib dir> if you have libraries in a
	              nonstandard directory <lib dir>
	  LIBS        libraries to pass to the linker, e.g. -l<library>
	  CPPFLAGS    (Objective) C/C++ preprocessor flags, e.g. -I<include dir> if
	              you have headers in a nonstandard directory <include dir>
	  CPP         C preprocessor
	  PKG_CONFIG  path to pkg-config utility
	  AVAHI_CFLAGS
	              C compiler flags for AVAHI, overriding pkg-config
	  AVAHI_LIBS  linker flags for AVAHI, overriding pkg-config
	  AVAHI_TPOLL_CFLAGS
	              C compiler flags for AVAHI_TPOLL, overriding pkg-config
	  AVAHI_TPOLL_LIBS
	              linker flags for AVAHI_TPOLL, overriding pkg-config
	
	Use these variables to override the choices made by `configure' or to help
	it to find libraries and programs with nonstandard names/locations.
	
	Report bugs to the package provider.
	root@ubuntu:/tmp/netatalk-3.0.2#
	
	root@ubuntu:/tmp/netatalk-3.0.2#./configure -enable-debian
	....
	....
	config.status: executing depfiles commands
	config.status: executing libtool commands
	Using libraries:
	    LIBS           = -ldl
	    CFLAGS         = -I$(top_srcdir)/include -I$(top_srcdir)/sys -D_U_="__attribute__((unused))" -g -O2
	    PTHREADS:
	        LIBS   =
	        CFLAGS = -pthread
	    LIBGCRYPT:
	        LIBS   = -L/lib/i386-linux-gnu -lgcrypt
	        CFLAGS =
	    PAM:
	        LIBS   =  -lpam
	        CFLAGS =
	    WRAP:
	        LIBS   = -lwrap
	        CFLAGS =
	    BDB:
	        LIBS   =  -ldb-5.1
	        CFLAGS =
	    ZEROCONF:
	        LIBS   =  -lavahi-common -lavahi-client
	        CFLAGS =  -D_REENTRANT
	    LDAP:
	        LIBS   =  -lldap
	        CFLAGS =
	    LIBEVENT:
	        bundled
	Configure summary:
	    INIT STYLE:
	         none
	    AFP:
	         Extended Attributes: ad | sys
	         ACL support: yes
	    CNID:
	         backends:  dbd last tdb
	    UAMS:
	         DHX2    (PAM SHADOW)
	         clrtxt  (PAM SHADOW)
	         guest
	    Options:
	         Zeroconf support:        yes
	         tcp wrapper support:     yes
	         quota support:           yes
	         admin group support:     yes
	         valid shell check:       yes
	         cracklib support:        no
	         ACL support:             yes
	         Kerberos support:        yes
	         LDAP support:            yes
	 
	 
	root@ubuntu:/tmp/netatalk-3.0.2# make
	make all-recursive
	make[1]: Entering directory `/tmp/netatalk-3.0.2’
	..
	..
	echo '/* event2/event-config.h' > include/event2/event-config.h
	echo ' *' >> include/event2/event-config.h
	echo ' * This file was generated by autoconf when libevent was built, and post-' >> include/event2/event-config.h
	echo ' * processed by Libevent so that its macros would have         
	  make  all-recursive
	make[1]: Entering directory `/tmp/netatalk-3.0.2'
	Making all in libevent
	make[2]: Entering directory `/tmp/netatalk-3.0.2/libevent'
	/bin/mkdir -p ./include/event2
	echo '/* event2/event-config.h' > include/event2/event-config       
	....
	....
	make[2]: Leaving directory `/tmp/netatalk-3.0.2/test'
	make[2]: Entering directory `/tmp/netatalk-3.0.2'
	make[2]: Leaving directory `/tmp/netatalk-3.0.2'
	make[1]: Leaving directory `/tmp/netatalk-3.0.2'
	root@ubuntu:/tmp/netatalk-3.0.2#
	
	
	root@ubuntu:/tmp/netatalk-3.0.2#make install
	....
	....
	make[3]: Leaving directory `/tmp/netatalk-3.0.2'
	make[2]: Nothing to be done for `install-data-am'.
	make[2]: Leaving directory `/tmp/netatalk-3.0.2'
	make[1]: Leaving directory `/tmp/netatalk-3.0.2'
	root@ubuntu:/tmp/netatalk-3.0.2#
	
	
	서비스기동 및 확인
	root@ubuntu:/tmp/netatalk-3.0.2# cd /etc/init.d
	root@ubuntu:/etc/init.d# netatalk restart
	
	root@ubuntu:/etc/init.d# /usr/local/sbin/afpd -V
	afpd 3.0.2 - Apple Filing Protocol (AFP) daemon of Netatalk
	
	This program is free software; you can redistribute it and/or modify it under
	the terms of the GNU General Public License as published by the Free Software
	Foundation; either version 2 of the License, or (at your option) any later
	version. Please see the file COPYING for further information and details.
	
	afpd has been compiled with support for these features:
	
	          AFP versions:	2.2 3.0 3.1 3.2 3.3
	         CNID backends:	dbd last tdb
	      Zeroconf support:	Avahi
	  TCP wrappers support:	Yes
	         Quota support:	Yes
	   Admin group support:	Yes
	    Valid shell checks:	Yes
	      cracklib support:	No
	            EA support:	ad | sys
	           ACL support:	Yes
	          LDAP support:	Yes
	
	              afp.conf:	/usr/local/etc/afp.conf
	           extmap.conf:	/usr/local/etc/extmap.conf
	       state directory:	/usr/local/var/netatalk/
	    afp_signature.conf:	/usr/local/var/netatalk/afp_signature.conf
	      afp_voluuid.conf:	/usr/local/var/netatalk/afp_voluuid.conf
	       UAM search path:	/usr/local/lib/netatalk//
	  Server messages path:	/usr/local/var/netatalk/msg/
	
	root@ubuntu:/etc/init.d#
	
	root@storycube:/usr/local/sbin# ./netatalk restart
	netatalk is already running (pid = 27310), or the lock file is stale.
	root@storycube:/usr/local/sbin#
	
	해당 pid kill
	
	root@storycube:/usr/local/sbin# ./netatalk restart
	netatalk is already running (pid = 27310), or the lock file is stale.
	root@storycube:/usr/local/sbin# kill -9 27310
	root@storycube:/usr/local/sbin# ./netatalk restart
	root@storycube:/usr/local/sbin#
	
	;
	; Netatalk 3.x configuration file
	;
	
	[Global]
	; Global server settings
	;Change this to increase the maximum number of clients tah can connection. new default=200
	max connections = 50
	
	;ATALK_NAME
	hostname = `/bin/hostname --short`
	
	;specify the Mac and unix charsets to be used.
	unix charset = UTF8
	mac charset = MAC_ROMAN
	
	;Change this to set th id of the guest user
	guest account = nobody
	
	
	; [Homes]
	; basedir regex = /xxxx
	
	; [My AFP Volume]
	; path = /path/to/volume
	
	[TimeMachine]
	path = /nas/time
	options = tm
	allow = hkmade,root
	
	[Nas]
	path = /nas
	allow = hkmade,root






AFP의 포트포워딩 설정
포트번호는 548


	johangyuui-MacBook-Pro:~ miles$ telnet 221.148.161.172:548
	221.148.161.172:548: nodename nor servname provided, or not known
	johangyuui-MacBook-Pro:~ miles$ telnet 221.148.161.172:548
	221.148.161.172:548: nodename nor servname provided, or not known
	johangyuui-MacBook-Pro:~ miles$ telnet 221.148.161.172 548
	Trying 221.148.161.172...
	Connected to 221.148.161.172.
	Escape character is '^]'.
	^CConnection closed by foreign host.
	johangyuui-MacBook-Pro:~ miles$




## JDK설치.
windows 8 
www.java.com 에서 desktop OS에 맞게 자동 다운로드
JAVA SE Runtime Environment 7 update 17   .2013.04.09


##Airvideo설치.
windows 8
컴퓨터의 영상을 무선랜(와이파이, 와이브로등등)을 지원하는 개인용 모바일 기기에서 볼 수 있게 해주는 프로그램. 컴퓨터의 영상을 스마트 기기에 직접 복사해서 넣지 않고도 동영상을 볼수 있다. 지원하는 단말기기는 iPhone, iPad, iPod touch

http://www.inmethod.com/air-video/index.html
2.46beta3.exe  2013.04.09

설정해야 할 내용
Shared Folders
-Add Disk Folder
Subtitiles
-Font : 맑은고딕 또는 나눔고딕
Default Encoding: Korean(Windows)
Preferred Languages : 1번은 korean 2번은 english

Remote탭의 Enable Access from Internet 체크
PIN NUMBER 525 627 041

Settings - Require Pasword - hankyu12

---

포트포워드 
45631포트를 포트포워드 규칙에 추가할것.
외부에서  http://공유기공인아이피:45631  체크.
웹브라우저에서 Air Video화면이 뜨면 성공.

아이폰 air video설치.
서버추가
Enter Server PIN : ..

---

이토렌트 자동으로 download받기.
dropbox에  torrent 폴더 생성.
utorrent 설치
ctrl+P 설정 : 디렉토리 - 아래의 토렌트 자동 불러오기
dropbox의 폴더 연결

---

Network Speed

Location | Ping | Upload Speed | Download Speed | ETC
-------|---------|--
Office.SK Wibro |59ms | 4.20 Mbps | 1.93 Mbps |
Office.ipTime | 30ms | 3.42 Mbps | 0.54 Mbps| KT 221.148.161.172
Home   | fn + ← | cc
End    | fn + → | dd
PageUp | fn + ↑ | ee
PageDn | fn + ↓ | ff

##Qloud Server 설치
다운로드 사이트
http://j.mp/qloud-server
http://app.qiss.mobi  
http://54.248.222.241/wordpress/?cat=10

























