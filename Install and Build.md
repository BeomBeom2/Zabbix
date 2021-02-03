# ZABBIX INSTALLATION on UBUNTU 

# STEP 1
 ### 플랫폼에 맞는 구성 선택
 
[Download Zabbix](https://www.zabbix.com/download)

ZABBIX VERSION, OS DISTRIBUTION 등 자신에게 맞는 항목 선택 후 명령어 실행.

ZABBIX 5.2, Ubuntu 18.04, MYSQL, Apache로 구성

# STEP 2
### 우분투에 자빅스 설치

 - **a. 자빅스 레포지토리 설치**
	  - wget https://repo.zabbix.com/zabbix/5.2/ubuntu/pool/main/z/zabbix-release/zabbix-release_5.2-1+ubuntu18.04_all.deb  
	 - dpkg -i zabbix-release_5.2-1+ubuntu18.04_all.deb  
	 - sudo apt upgrade && apt update

- **b. 자빅스 서버, 프론트앤드, 에이전트 설치**
	-  apt install -y zabbix-server-mysql zabbix-frontend-php zabbix-apache-conf zabbix-agent mysql-server

		> 설치가 됬는지 확인
		> sudo php --version 
		> sudo mysql -V 
		> sudo apache2 -V

		> 라즈베리파이(debian)는 mysql-server X
		> mysql-server대신 sudo apt install mariadb-server

 - **c. MYSQL서버 구성하기(in 우분투)**
	 - ssudo mysql_secure_installation 
		  -  Press y|Y for Yes, and other key for No : n(No super complex PW)
		  - New password: "root PW for MYSQL Server" 절차대로했다면 password가 PW
		  - Remove anonymous users? : y
		  - Disallow root login? : n(I want to log in as root remotely) 
		- Remove test database and access to it? : y
		-  Reload privilege tables now? : y

- **d. 초기 DB 생성**
	- sudo mysql -u root -p
	- create database zabbix character set utf8 collate utf8_bin;
	- create user zabbix@localhost identified by 'password';
	- grant all privileges on zabbix.* to zabbix@localhost;
	- flush privileges;
	- quit;

#### 자빅스 서버 호스트에서 초기 스키마 및 데이터를 가져옵니다. 새로 생성 된 비밀번호를 입력하라는 메시지가 표시됩니다.

 - zcat /usr/share/doc/zabbix-server-mysql*/create.sql.gz | mysql -uzabbix -p zabbix
- Enter password: password

> mysql에 자빅스 테이블 생성됐는지 확인 
>	sudo mysql -u root -p 
>	use zabbix; 	
>	show tables;

**자빅스 서버 conf파일 업뎃** 

- sudo vi /etc/zabbix/zabbix_server.conf
	DBPassword의 주석을 지우고 password라고입력

**자빅스 php conf파일 업뎃**

- sudo vi /etc/zabbix/apache.conf
	php_value date.timezone Asia/Seoul으로 수정

- **e. 자빅스 서버, 에이전트 시작**
	 - systemctl restart zabbix-server zabbix-agent apache2
		 
	> sudo service zabbix-server status

	 - systemctl enable zabbix-server zabbix-agent apache2
	

# STEP 3

웹에서 자신IP/zabbix로 들어가 zabbix설정

![zabbix PW](https://user-images.githubusercontent.com/62642155/105120336-6c0a7e80-5b15-11eb-9ec3-4d4927bc85cb.png)
1. PW설정 (=password)

2. 로그인 Admin, zabbix

3. 통신할 컴퓨터에 SNMP설치

![1](https://user-images.githubusercontent.com/62642155/105120382-7f1d4e80-5b15-11eb-8b64-f81a53bec095.png)

![2](https://user-images.githubusercontent.com/62642155/105120378-7c225e00-5b15-11eb-9c1d-505bd09dae58.png)

![3](https://user-images.githubusercontent.com/62642155/105120379-7dec2180-5b15-11eb-9cce-7ba369d369e2.png)

4. zabbix 대쉬보드 호스트 생성

![4](https://user-images.githubusercontent.com/62642155/105120385-804e7b80-5b15-11eb-9281-210bfb67edf2.png)

Template항목에서 사진과 달리 OS Windows SNMP밖에 없음.
![5](https://user-images.githubusercontent.com/62642155/105120389-82b0d580-5b15-11eb-8609-dcc9bbc049b0.png)

이 후 그래픽과 디스커버리 항목을 설정해 원하는 항목을 보면 된다.



## zabbix 대쉬보드 팁
Monitering 탭 --> screens 들어가면 관리자가 보고 싶은 에이전트의 그래프를 골라 설정할 수 있다.
또한 그래프는 사용자 정의 그래프가 있어서 관리자가 항목을 선택할 수 있다. 하지만 커스텀 그래프를 각 에이전트 마다 설정해야 하는게 번거로울 수 있다.

그래프는 여러 종류가 있으나 다음 그래프는 동일한 상황을 보여준다.

Interface ethernet_9(Embedded LOM 1 Port 1-WFP Native MAC Layer LightWeight Filter-00): Network traffic
( = Interface ethernet_10, Interface ethernet_11, Interface ethernet_32770)

에이전트는 특정 목적을 위해 사용자를 대신해 작업을 수행하는 자율적 프로세스다.에이전트는 독자적으로 존재하지 않고 어떤 환경의 일부이거나 그 안에서 동작하는 시스템이다. 여기서의 환경은 운영 체제, 네트워크 등을 지칭한다. 에이전트는 지식베이스와 추론 기능을 가지며 사용자, 자원(resource), 또는 다른 에이전트와의 정보교환과 통신을 통해 문제해결을 도모한다. 에이전트는 스스로 환경의 변화를 인지하고 그에 대응하는 행동을 취하며, 경험을 바탕으로 학습하는 기능을 가진다. 에이전트는 수동적으로 주어진 작업만을 수행하는 것이 아니고, 자신의 목적을 가지고 그 목적 달성을 추구하는 능동적 자세를 지닌다. 에이전트의 행동의 결과로 환경의 변화를 가져올 수 있다. 에이전트의 행동은 한번에 끝나는 것이 아니라 지속적으로 이루어진다.

대시보드는 이벤트를 모니터링하고, 결정을 내리며, 다른 사람에게 정보를 제공하고, 추세를 볼 수 있는 공간 정보와 데이터를 보여줍니다.
