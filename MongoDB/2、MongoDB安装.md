MongoDB的安装

操作系统 Debian10

参照[官方文档](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-debian/)执行

1、导入公钥

wget -qO - https://www.mongodb.org/static/pgp/server-4.4.asc | sudo apt-key add -

正常返回OK，如果返回错误，需要安装gnupg

sudo apt-get install gnupg

2、创建MongoDB的source文件

echo "deb http://repo.mongodb.org/apt/debian buster/mongodb-org/4.4 main" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.4.list

3、更新源

sudo apt-get update

4、安装MongoDB

sudo apt-get install -y mongodb-org

5、运行和管理mongod进程需要使用系统服务，如果不确定你的操作系统使用的初始化系统，可以执行

ps --no-headers -o comm 1

结果显示systemd使用systemctl命令，最近版本的Linux操作系统都是使用systemctl

结果显示init使用service命令

6、开启MongoDB

sudo systemctl start mongod

如果启动失败，提示

Failed to start mongod.service: Unit mongod.service not found.

执行sudo systemctl daemon-reload

7、验证MongoDB启动成功

sudo systemctl status mongod

8、配置远程连接

修改/etc/mongo.conf文件的bindIp，将127.0.0.1改为0.0.0.0，如果在后面继续添加客户端ip会报错

no suitable servers found (serverSelectionTimeOutMS)

9、MongoDB分为mongod、mongos进程