# Earlybird

This is a "check in morning" Wechat Mini Program based on uni-app.

这是一个“早起打卡”微信小程序，基于 uni-app 框架编写。

➡ [Earlybird 项目总结+功能说明](https://github.com/tLLWtG/Earlybird/blob/main/doc/Earlybird项目总结.pdf)

## Deployment

1. 本项目使用 HbuilderX 和微信开发者工具开发，部署前请安装好这两个软件。

2. 拉取此项目到本地。

3. 通过 uniCloud 关联此项目到阿里云服务空间。

4. 在 uniCloud 的云数据库中创建两个数据表："login"和"checkrecord"。

   数据表的表结构(schema2code)如下：

   * login

     ```
     {
       "bsonType": "object",
       "required": [],
       "permission": {
         "read": true,
         "create": false,
         "update": false,
         "delete": false
       },
       "properties": {
         "_id": {
           "description": "ID，系统自动生成"
         },
         "id": {},
         "name": {},
         "class": {},
         "admin": {},
         "password": {}
       }
     }
     ```

   * checkrecord

     ```
     {
       "bsonType": "object",
       "required": [],
       "permission": {
         "read": true,
         "create": true,
         "update": false,
         "delete": true
       },
       "properties": {
         "_id": {
           "description": "ID，系统自动生成"
         },
         "id": {},
         "name": {},
         "date": {},
         "time": {}
       }
     }
     ```

5. 下面是几条表中数据的样例：

   * login 表中的学生数据

     ```
     {
         "name": "张三",
         "class": "1",
         "id": "3722001",
         "password": "123456",
         "admin": false
     }
     ```

   * login 表中的管理员数据

     ```
     {
         "name": "赵老师",
         "class": "1",
         "id": "1000001",
         "password": "123",
         "admin": true
     }
     ```
     
   * checkrecord 表中的打卡数据

     ```
     {
         "id": "3722001",
         "date": "2023-12-18",
         "time": "06:48",
         "name": "张三"
     }
     ```

6. 建立好云数据库后，使用 HbuilderX 运行项目到小程序模拟器中即可查看效果。

## Attention

此项目目前的版本不考虑如下特殊的情况（实际使用中一般不会出现）：

* 同一设备上多账号切换打卡。
* 手动清除小程序缓存后再次打卡。

因此在测试时请注意：

* 每次登录不同的账号前请**将小程序模拟器的缓存完全清空**。
* **不在清除小程序缓存后登录同一账号再次打卡**。
> 若确实需要多次登录同一账号打卡，请先在云数据库中手动将此账号今天的打卡记录删除。

## Developer Team

Here are the members of Half Team(半个队):
* [tllwtg](https://github.com/tLLWtG)
* [Corgi inequality](https://github.com/xmu22)
> Names are in no particular order.

## License

This project is licensed under the [MIT license](https://github.com/tLLWtG/Earlybird/blob/main/LICENSE). External libraries used by Earlybird are licensed under their own licenses.
