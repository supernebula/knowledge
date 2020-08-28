# monogdb 

基于配置文件启动

```shell
# ./bin/mongod -f ./conf/mongo.conf
```

```shell
# mongod  --shutdown
# ./bin/mongod  --shutdown --dbpath /opt/mongo/data
```

```shell
> show show dbs
> use admin
> db.createUser({
    user:"admin",
    pwd:"213456789",
    roles:[{
        role:"admin",
        db:"admin"
    },{
        role:"readWrite",
        db:"admin"
    }]
})

------------------------------------------ok----------------------------------------
>db.createUser({
    user:"admin",
    pwd:"213456789",
    roles:[{
        role:"readWrite",
        db:"admin"
    }]
})

db.createUser({
    user:"admin",
    pwd:"12121212",
    roles:[{
        role:"dbAdmin",
        db:"admin"
    }]
})

db.createUser({
    user:"admin",
    pwd:"12121212",
    roles:[{
        role:"root",
        db:"admin"
    }]
})


Successfully added user: {
	"user" : "admin",
	"roles" : [
		{
			"role" : "readWrite",
			"db" : "admin"
		}
	]
}



db.auth("admin", "12312312")
-----------------------------------------------------------------------------
db.createUser({
    user:"root",
    pwd:"123142345",
    roles:[{
        role:"root",
        db:"admin"
    },{
        role:"readWrite",
        db:"admin"
    }]
})
db.auth("root", "123142345")



db.auth( <username>, <password> )

```


--------------------------------------追加权限--------------------------

db.grantRolesToUser("admin",[{role:"root",db:"admin"}])
db.grantRolesToUser("admin",[{role:"readWrite",db:"db1"}])
---------------------------

db.createUser({
    user:"admin",
    pwd:"12213234234",
    roles:[{
        role:"root",
        db:"db1"
    }]
})



centos7 mongo添加账号密码

db.dropUser("root")



mongo --host <host:port> -u <username> -p --authenticationDatabase <database>

mongo --host 127.0.0.1:27017 -u admin -p --authenticationDatabase db1


## mongodb数据导出、导入

### mongodump导出 

```shell
mongodump -h 127.0.0.1:27017  -u root -p youpassword1 --authenticationDatabase=admin -d=db1 --collection table1 -o D:\data\dump_db1
```


### mongorestore还原指定数据库

```shell
mongorestore -h 192.168.0.2:27017 -u root -p youpassword2 --authenticationDatabase=admin -d=db@ D:\data\dump_db1\db1
```
