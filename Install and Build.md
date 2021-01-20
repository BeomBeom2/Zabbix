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

> mysql에 자빅스 테이블 생성됐는지 확인 	sudo mysql -u root -p 	use zabbix; 	show tables;

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



