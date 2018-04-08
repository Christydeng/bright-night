# bright-night

提问网址：https://github.com/dengjiayao/bright-night/issues 

因为后来添加的学生管理模块需要用到接口， 所以需要配置数据库（MongoDB）的环境， 还要写接口， 接口代码还没贴， 启动时先启动MongoDB数据库， 再启动后台， 再启动此项目


附： MongoDB的安装和使用
1.先安装（D:\mongoDB）

2.配置环境变量（D:\mongoDB\bin）

3.D盘建立数据库文件夹,然后执行以下命令(在D盘创建data文件夹，在data中创建db文件夹)

mongod --dbpath D:\data\db

4.全局安装（在D盘的data下创建dbConf文件夹，在mongod.exe所在的文件夹执行以下命令）
mongod.exe --bind_ip yourIPadress --logpath "D:\data\dbConf\mongodb.log" --logappend --dbpath "D:\data\db" --port 2017 --serviceName "admin" --serviceDisplayName "admin" --install

5.双击安装目录bin下的mongod.exe

6.cmd输入mongo(链接数据库， 看到相关版本信息则安装成功)

7.接下来就可以手敲数据库命令（对MongoDB进行增删查改）

创建表：
db.createCollection("students")
db.createCollection("teachers") 

增：往runoob集合中插入数据
db.runoob.insert({x:"呵呵",y:23456})

删：
db.runoob.remove( { "name": "小强" } )

查：查询runoob集合中所有数据
db.runoob.find()

改：
db.runoob.update({"x":"10"},{$set:{"y":2345}})
