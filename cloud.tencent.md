## cloud.tencent ##
#### 云服务器设置 （Linux CentOS） ####
- 设置安全组，开放端口
#### 配置node环境 ####
- 安装nvm
- 安装nginx
	- yum install nginx
	- nginx -v
	- 查看nginx安装目录
		- rpm -ql nginx
			- rpm是linux的rpm包管理工具，-q 代表询问模式，-l 代表返回列表，这样我们就可以找到nginx的所有安装位置了
	- nginx.conf文件解读
		- nginx.conf 文件是Nginx总配置文件，在我们搭建服务器时经常调整的文件
		- 进入etc/nginx目录下，然后用 vim进行打开nginx.conf文件
			- cd /etc/nginx
			- vim nginx.conf
				- 按 i 编辑； esc退出编辑; shift+: 输入wq保存退出
	- 查看nginx是否启动
		- ps aux | grep nginx
	- 启动 nginx
		- nginx
	- 停止 nginx
		- 立刻停止，无论程序有没有运行完，都立刻停止
			- nginx -s stop
		- 从容停止，有未运行完的程序，运行完成后才停止
			- nginx -s quit
	- killall 方法杀死进程
		- killall nginx
	- 查看端口
		- netstat -tlnp
	- 至此，nginx 安装完成
	- 
- 安装mysql
	1. 进入 /usr/local/soft/目录，
	2. 下载mysql压缩包
		- 可以在自己电脑下载然后通过xftp上传到CentOS的该目录下
		- 也可以在此目录执行 wget【mysql链接】 直接下载
		- 官网：https://dev.mysql.com/downloads/file/?id=498903
	3. 解压缩
		- tar -zxvf mysql-5.7.29-linux-glibc2.12-x86_64.tar.gz
	4. 修改文件名为mysql
		- mv mysql-5.7.29-linux-glibc2.12-x86_64 mysql
	5. 创建mysql用户组和用户并修改权限
		- groupadd mysql
		- useradd -r -g mysql mysql
	6. 接着创建数据目录并赋予权限，使用的命令如下（注意必须使用mysql用户，不能使用root用户，否则会由于文件从属关系导致mysql启动失败）
		- mkdir -p  /data/mysql              #创建目录
		- chown mysql:mysql -R /data/mysql   #赋予权限
	7. 使用命令vi /etc/my.cnf修改配置文件

		    [mysqld]
    		bind-address=0.0.0.0
    		port=3306
    		user=mysql
    		basedir=/usr/local/soft/mysql  # mysql安装目录
    		datadir=/data/mysql  # 数据存放目录
    		socket=/tmp/mysql.sock
    		log-error=/data/mysql/mysql.err
    		pid-file=/data/mysql/mysql.pid
    		#character config
    		character_set_server=utf8mb4
    		symbolic-links=0
    		explicit_defaults_for_timestamp=true

	8. 开始初始化数据库。进入mysql的bin目录,然后在里面执行下面一行代码（注意里面两个路径必须与你在my.cnf配置文件中设置的一致，否则会报错）
		- ./mysqld --defaults-file=/etc/my.cnf --basedir=/usr/local/soft/mysql/ --datadir=/data/mysql/ --user=mysql --initialize
		- 路径一致了，报 ./mysqld: error while loading shared libraries
			- yum -y install numactl 在执行初始化
		- 执行完如果最后面有临时密码，先复制保存起来

	9. 启动mysql服务，注意先将mysql.server放置到/etc/init.d/mysql中，可以让dameon来管理Mysql的启动(即也就是service，CentOS7就是syetemctl)，可以使用下面的命令复制一份过去，且将mysql.server修改为mysql，这样便于记忆启动命令：
		- cp /usr/local/soft/mysql/support-files/mysql.server /etc/init.d/mysql
	10. 完成复制后就使用命令 service mysql start 启动mysql
		- 如果报 Starting MySQL...The server quit without updating PID file [FAILED]ysql/iZuf67on1pthsx5glu6ohyZ.pid).
			- 查看日志文件/data/mysql/mysql.err
			- 然后使用命令ps -ef|grep mysql查看一下mysql是否真的已经启动了
	11. 进入到bin目录
		- ./mysql -uroot -p临时密码
		- 如果没有临时密码 打之前安装MySQL的路径下data文件。打开以.err为后缀的文件,找到类似如下就是你的密码
		- A temporary password is generated for root@localhost: hF&Hl(7AoJ6t
	12. 执行如下，修改密码
		- SET PASSWORD = PASSWORD('123456');
		- ALTER USER 'root'@'localhost' PASSWORD EXPIRE NEVER;
		- FLUSH PRIVILEGES;
	13. 目前可能远程连接不上，执行如下，开放访问IP端口。先进入到数据库，接着执行以下三行代码，这样就开放了数据库访问IP端口
		- use mysql;     #访问mysql库
		- update user set host = '%' where user = 'root';   #使root用户能在任何IP进行访问
		- FLUSH PRIVILEGES;
	14. 目前只能在bin目录下使用mysql操作数据库，执行如下，创建软连接，不用进到bin也可以直接使用 mysql
		- ln -s  /usr/local/soft/mysql/bin/mysql    /usr/bin
		- 这样，直接执行 mysql -uroot -p密码 就可以进入数据库了.
		- 进入之后，执行 show databases; 试下？
		- update user set authentication_string=PASSWORD("你的密码") where User = 'root';
		- FLUSH PRIVILEGES;
#### redis ####
- 进入到bin目录
- 启动服务
	- redis-server
- 查看 redis 进程
	- ps -ef | grep redis
- 客户端启动
	- ./redis-cli -p 6379
- 管理服务连接
	- shutdown
- 退出
	- exit
- 结束进程
	- pkill redis
- 启动reidis服务：并指定使用的配置文件
	- redis-server ../redis.conf
		- 我这里 redis.conf 文件在 redis-server 的上一层
			- redis-server => /opt/redis-3.2.9/src
			- redis.conf => /opt/redis-3.2.9


#### 打包上线路径问题 ####
- 去掉路由中的#
	- assetsPublicPath 改成绝对路径 '/'
	- 路由模式改为history
- 刷新页面404
	- nginx配置
	
			server {
				listen          8082;
				server_name     _;
				root            /root/var/app/ELDEMO/dist/;
				
				location / {
				    index   index.html;
				    try_files $uri $uri/ @router;
				}
				location @router {
				    rewrite ^.*$ /index.html last;
				}
			}

- css资源404
	- build/utils.js
		- publicPath => '../../'

