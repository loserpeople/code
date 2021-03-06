前置准备：
1.docker 安装：
命令：bash <(curl -s -L get.docker.com)
# 添加用户组：
	# 将当前用户添加到docker用户组
	sudo usermod -aG docker $USER
	# 重启docker服务
	sudo systemctl restart docker
	docker --version
	配置Docker国内镜像源
	mkdir -p /etc/docker
	# 创建/修改daemon.json配置文件
	sudo gedit /etc/docker/daemon.json
	# 配置内容如下：
		{
			"registry-mirrors": ["https://docker.mirrors.ustc.edu.cn"]
		}
	#重启服务
	systemctl daemon-reload
	systemctl restart docker.service
	![dokcer安装](https://user-images.githubusercontent.com/102428352/163790676-47592333-050d-4624-805a-cd7c94a1203d.jpg)


# docker-compose：
	sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
	#由于GitHub可能获取访问不了，可以用下面这条
	sudo curl -L "https://get.daocloud.io/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
	#修改权限
	sudo chmod +x /usr/local/bin/docker-compose
	#检查
	docker-compose --version
	

# 3.安装python和pymysql（此处采用fisco官网提供命令，Ubuntu20.4上的版本自带的python都是3.8，所以可忽略安装，直接安装pip）
	# 添加仓库，回车继续
	sudo add-apt-repository ppa:deadsnakes/ppa
	# 安装python 3.6
	sudo apt-get install -y python3.6
	sudo apt-get install -y python3-pip
	sudo pip3 install PyMySQL

# 4.安装Java
	sudo apt install -y default-jdk
	#配置环境变量
	sudo gedit /etc/profile
		export JAVA_HOME=/etc/java-11-openjdk(因个人版本和位置而异)
		export PATH = $JAVA_HOME/bin:$PATH
	source /etc/profile

# 5.安装msql
	sudo apt install mysql-server
	#检查
	mysql -V
	#登入MySQL 默认密码123456
	sudo mysql -uroot -p123456
	#创建新账户提供WeBase使用(用户名‘test’和密码‘123’，看个人情况修改)
	CREATE USER 'test'@'localhost' IDENTIFIED BY '123';
	GRANT ALL PRIVILEGES ON *.* TO 'test'@'localhost';
	Flush privileges;
	exit;
	![mysql](https://user-images.githubusercontent.com/102428352/163790719-3ae9cda8-6975-44c3-9934-58ea0a886f95.jpg)


# 6 开放5000和5002端口
	sudo apt-get install iptables-persistent
	sudo iptables -I INPUT -p tcp --dport 5000 -j ACCEPT
	sudo iptables -I INPUT -p tcp --dport 5002 -j ACCEPT
	# 安装iptables-persistent后 开放端口需要root权限

# 7 拉取部署脚本
	wget https://osp-1257653870.cos.ap-guangzhou.myqcloud.com/WeBASE/releases/download/v1.5.4/webase-deploy.zip
	#解压
	unzip webase-deploy.zip
	#进入目录
	cd webase-deploy

# !!!最重要的一步！！！
	sudo gedit common.properties

	#（# Mysql database configuration of WeBASE-Node-Manager）下的mysql.user=，mysql.password= 修改为配置mysql时的账户密码#
	## Mysql database configuration of WeBASE-Sign（同上）#
	#在第74行按照自身情况选课yes,no选no就可以直接保存退出，选yes则需要配置fisco.dir和node0.dir
	![mysql配置](https://user-images.githubusercontent.com/102428352/163790733-ba185d17-14c6-4c91-8eb1-2b3a2d5af71a.jpg)


#	拉取镜像
	python3 deploy.py pullDockerAll	
	#超时可手动拉取
	docker pull webasepro/webase-front:v1.5.3
	docker pull webasepro/webase-node-mgr:v1.5.3
	docker pull webasepro/webase-sign:v1.5.3
	docker pull webasepro/webase-web:v1.5.3
	docker pull fiscoorg/fiscobcos:v2.8.0

# 最后运行
	python3 deploy.py installAll
	#成功后便可以直接登录了
	![image](https://user-images.githubusercontent.com/102428352/163790779-266eeff1-2d9f-4d5c-be9b-efe96e932ad2.png)

	http://localhost:5000
	http://127.0.0.1:5000	
	默认账户密码：
	admin Abcd1234








