Demo API 接口说明
====

**说明**: 所有请求必须携带预先申请的参数 `AppKey` 。


## Service API

- 注册

        POST /api/auth/register
      
        { "user": "test", "password": "xxxx", "type": "1", "sms_code": "zh8Wke1M", "code_id": 2192 }

    **说明**
    
    - `type` : 1 - 学生； 10 - 老师 （目前暂时可忽略）
    - `sms_code` `code_id`  分别为请求短信验证码时服务器返回的值（参考 **获取验证码** 接口）
    - ~~用户类型为老师时，需携带 `icode` 邀请码参数~~
    
    **应答**

        {
            "result": "OK"
        }

- 登录

        POST /api/auth/login
      
        { "user": "test", "password": "xxxx" }

    **说明**
    
    - 需要保存应答中的 `token` ，作为后续API调用的凭证。
    - 当某个API请求遇到 HTTP 401 错误时，说明 `token` 过期，需要重新登录，申请新的 `token`

    **应答**

        {
            user: "test"
            token: "0b4b55e0-0613-11e5-a69d-746573743100"
        }

- 退出登录

        POST /api/auth/logout
      
        { "user": "test", "token": "0b4b55e0-0613-11e5-a69d-746573743100" }

    **说明**
    
    - 可能只有 Web 版本需要使用
    - `TODO`
    
    **应答**

        {
            "result": "OK"
        }

- 获取短信验证码

        POST /api/auth/smscode
      
        { "user": "test", "token": "0b4b55e0-0613-11e5-a69d-746573743100", "phone": "13828473948", "service_type": 1 }

    **说明**
    
    - `service_type` 表示验证码用途。 1 - 注册；2 - 修改密码；3 - 忘记密码；4 - 绑定手机
    - 注册时无需提供 `user` 和 `token` 参数，其它已确定用户名的情况则需要提供该参数
    - 客户端应提供保护机制，避免用户短时间内重复获取短信验证码
    - 返回的结果包含 `code_id` , 在校验短信验证码（例如提交注册信息）时，需要同时提供该参数
    
    **应答**

        {
            "code_id": 213
        }

- 获取用户信息

        POST /api/user
      
        { "user": "test", "token": "0b4b55e0-0613-11e5-a69d-746573743100" }

    **说明**
    
    - 获取 `display_name`
    - 将来可能会包含用户其它属性：老师、班级属性
    - 不同类型用户返回的附加信息结构不同
    
    **应答**

        {
            id: 4,
            name: "13912257904",
            display_name: "xiaoming",
            type: 10,
            phone: 13938373737,
            teacher: {
                id: 12,
                name: "js962981",
                display_name: "abc"
            },
            class: {
                id: 1,
                name: "className"
            }
        }
        或
        {
          id: 32,
          name: "sjk0000103",
          type: 10,
          phone: 13938373737,
          display_name: "",
          director: {
            id: 27,
            name: "sjkd000104",
            type: 20,
            phone: 12929293747,
            display_name: "asdsf"
          }
        }

- 更新用户信息

        POST /api/user/update
      
        { "user": "test", "token": "0b4b55e0-0613-11e5-a69d-746573743100", "display_name": "Wang Ming" }

    **说明**
    
    - 修改 `display_name` 等除了电话号码/电子邮件之外的信息
    - 将来可能会包含用户其它属性
    - 会返回所有可见的用户信息
    
    **应答**

        {
            "user": "test", "display_name": "Wang Xiaoming"
        }

- 绑定手机

        POST /api/user/phone/update
      
        { "user": "test", "token": "0b4b55e0-0613-11e5-a69d-746573743100", "phone": "13023456789" }

    **说明**
    
    - 修改 电话号码
    - 用户将来收到手机短信验证码，验证通过才表示修改成功

    **应答**

        {
            result: "OK"
        }

- 验证邀请码

        POST /api/icode/verify
      
        { "icode": "R6InSgyh" }

    **说明**
    
    - 邀请码验证之后才允许注册
    - `HTTP 200` 表示有效，其它值表示无效

    **应答**

        { "msg": "Valid" }
        { "msg": "Invalid" }

- 生成邀请码
        `TODO`
- 获取套餐

        POST /api/bookset/all
      
        { "user": "test", "token" :"0b4b55e0-0613-11e5-a69d-746573743100" }

    **说明**
    
    - 获取产品的所有可选套餐，其中包括已订阅和未订阅项，由 `subscribed` 描述
    - 套餐项内包含所有免费的和已推送（可见）的书本 **ID** 列表
    - 应答中的 `subscribed` 表示该套餐是否订阅过， `sub_status` 表示已订阅项中的当前订阅状态，最多只有一项为真
    
    **应答**

        [
          {
            "id": 10007,
            "name": "一年级",
            "description": "",
            "books": "1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20",
            "free_books": "1,2,3,4,5",
            "assigned_books": "2,8",
            "total": 61,
            "subscribed": false,
            "sub_status": false
          },
          {
            "id": 10008,
            "name": "这是二年级套餐",
            "description": "",
            "books": "101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132",
            "free_books": "101,102,103,104,105",
            "assigned_books": "102,108",
            "total": 60,
            "subscribed": true
            "sub_status": true
          },
          {
            "id": 10009,
            "name": "正式版三年级套餐",
            "description": "",
            "books": "201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238",
            "free_books": "201,202,203,204,205,206,207,208",
            "total": 58,
            "subscribed": true
            "sub_status": false
          }
        ]

- 创建套餐

        POST /api/bookset/create
      
        { "user": "test", "token" :"0b4b55e0-0613-11e5-a69d-746573743100", "name": "bookset_name", "books": "book_id_1,book_id_2,book_id_3", "free": "book_id_1,book_id_2" }

    **说明**
    
    - Admin API
    - `free` 是免费书本列表，不论它是否列在 `books` 中，缺省都会将其记做 `books` 的一部分
    
    **应答**

        {
            "bookset_id": "10012"
        }

- 订阅套餐

        POST /api/bookset/subscribe
      
        { "user": "test", "token" :"0b4b55e0-0613-11e5-a69d-746573743100", "bookset_id": "bookset_id_1", "months": "4" }

    **说明**
    
    - `months` 为订阅时间（可能改为 `weeks` 更便于计算）
    - 订阅新套餐后，之前订阅的套餐自动截止，不会再有后续更新
    
    **应答**

        {
            "result": "OK"
        }

- 获取老师信息

        POST /api/teacher
      
        { "user": "test", "teacher": "tj859583" }

    **说明**
    
    - 可以作为学生查找老师、绑定老师用
    -  `teacher` 为老师注册ID，非后台数据库ID
    - 不作为老师获取个人信息用

    **应答**

        {
            "id": 69
            "name": "tj859583"
            "display_name": "Mike"
        }

- 绑定一位老师

        POST /api/teacher/assign
        POST /api/teacher/associate
      
        { "user": "13828374485", "teacher": "tj859583" }

    **说明**
    
    - 可以作为学生查找老师、绑定老师用（两个接口功能相同）
    -  `teacher` 为老师注册ID，非后台数据库ID

    **应答**

        { result: "OK" }

- 绑定一位教导员

        POST /api/director/associate
      
        { "user": "tjs859583", "director": "tjsd59583" }

    **说明**
    
    - 可以作为老师关联/绑定教导员用，只有老师有权限调用该接口
    -  `director` 为教导员注册ID，非后台数据库ID

    **应答**

        { result: "OK" }

- 获取老师列表

        POST /api/teacher/list
      
        { "user": "tjsd00102" }

    **说明**
    
    - 获取与一位教研员相关联的老师列表，只有教研员有权限调用该接口
    -  ~~`director` 为教导员注册ID，非后台数据库ID~~

    **应答**

        {
            "id": 27,
            "name": "tjsd00102",
            "teachers": [
            {
                "id": 32,
                "name": "sjk0000103",
                "type": 10,
                "display_name": ""
            },
            {
                "id": 33,
                "name": "sjk0000106",
                "type": 10,
                "display_name": ""
            }]
        }

- 获取班级信息

        POST /api/class
      
        { "user": "tj859583", "class_id": 518 }

    **说明**
    
    - 只有老师有权限调用该接口， `user` 对应调用者ID
    - 应答中的 `created_by` 为该班级的老师（创建者）
    - 应答中的 `bookset_id` 对应年级（即业务逻辑中得套餐ID）

    **应答**

        {
            "id": 1
            "name": "className"
            "bookset_id": 2
            "created_by": 12
        }

- 获取班级学生列表

        POST /api/class/member
      
        { "user": "tj859583", "class_id": 518 }

    **说明**
    
    - 只有老师有权限调用该接口， `user` 对应调用者ID
    - 应答中的 `created_by` 为该班级的老师（创建者）
    - 应答中的 `bookset_id` 对应年级（即业务逻辑中得套餐ID）

    **应答**

        {
            class: {
                id: 1,
                name: "className",
                bookset_id: 2,
                created_by: 12
            }
            members: [
                { id: 4, name: "13911257724", display_name: "jackww"
                },
                { id: 5, name: "13912345892", display_name: "tom"
                }
            ]
        }

- 获取绑定老师的学生信息

        POST /api/teacher/student
      
        { "user": "bj483726" }

    **说明**
    
    - 只有老师可以调用该接口
    - 返回所有学生信息， 其中 `class_id` 为 0 表示没有加入任何班级

    **应答**

        {
            "id": 69
            "name": "tj859583"
            "students": [
                { id: 212, name: "...", display_name: "...", class_id: 8 },
                { id: 219, name: "...", display_name: "...", class_id: 0 }
            ]
        }

- 获取班级列表

        POST /api/teacher/class
      
        { "user": "tj859583" }

    **说明**
    
    - 只有老师有权限调用该接口， `user` 对应调用者ID
    -  todo

    **应答**

        {
            id: 12,
            name: "js962981",
            classes: [
                { id: 1, name: "className", bookset_id: 2 },
                { id: 2, name: "className2", bookset_id: 3 },
            ]
        }

- 创建班级

        POST /api/class/create
      
        { "user": "tj859583", "name": "三年一班", "bookset_id": 3 }

    **说明**
    
    - 只有老师有权限调用该接口， `user` 对应调用者ID
    -  `bookset_id` 对应年级（即业务逻辑中得套餐ID）

    **应答**

        { "result": "OK" }

- 加入班级

        POST /api/class/join
      
        { "user": "tj859583", "class_id": 518, "user_ids": "35, 44, 103" }

    **说明**
    
    - 将一个或多个 user （即学生）加入一个班级
    - 只有老师有权限调用该接口， `user` 对应调用者ID
    - 应答中的 `created_by` 为该班级的老师（创建者）
    - 应答中的 `bookset_id` 对应年级（即业务逻辑中得套餐ID）

    **应答**

        { "result": "OK" }

- 加入班级

        POST /api/class/join
      
        { "user": "tj859583", "class_id": 518, "user_ids": "35, 44, 103" }

    **说明**
    
    - 将一个或多个 user （即学生）加入一个班级
    - 只有老师有权限调用该接口， `user` 对应调用者ID
    - 应答中的 `created_by` 为该班级的老师（创建者）
    - 应答中的 `bookset_id` 对应年级（即业务逻辑中得套餐ID）

    **应答**

        { "result": "OK" }

- 退出班级

        POST /api/class/quit
      
        { "user": "tj859583", "class_id": 518, "user_ids": "35, 44, 103" }

    **说明**
    
    - 将一个或多个 user （即学生）退出一个班级
    - 只有老师有权限调用该接口， `user` 对应调用者ID

    **应答**

        { "result": "OK" }

- 布置作业

        POST /api/task/assign
      
        { "user": "tjs59583", "book_id": 518, "class_id": "2", "bookset_id": "5" }

    **说明**
    
    - 老师布置作业
    - 需要提供有效的 `bookset_id`，`book_id`， `class_id`，并且确保套餐和书本对应，老师和班级对应 

    **应答**

        { "result": "OK" }

- 获取作业列表

        POST /api/task/list
      
        { "user": "tjs59583", "class_id": 18, "bookset_id": "5" }

    **说明**
    
    - 根据班级获取作业列表
    - 需要提供有效的 `bookset_id`， `class_id`，并且确保套餐和老师、班级对应 
    - 返回的 `assigned_books` 即作业对应的书本ID，通过这些ID可以进一步查询/获取与作业相关的学生完成情况，例如分数等

    **应答**

        {
              "teacher": "tjs59583",
              "class_id": "18",
              "bookset_id": "5",
              "assigned_books": [
                "130“,
                "136”,
                "137"
              ]
        }

- 获取一个测试

        POST /api/exam
      
        { "user": "tj859583", "book_id": 518}

    **说明**
    
    - 获取一个测试或作业的信息
    - `book_id` 为对应的书本ID
    - 应答中的 `file_id` 对应书本名
    - 可通过应答中的 `url` 去下载该测试/作业

    **应答**

        {
            id: 1
            name: "gk_u2_drib1"
            book_id: 1
            file_id: "GK_U2_DRIB1"
            url: "http:/exampl.com/testStorage/gk_u2_drib1.zip?t=xxx"
        }

- 提交成绩

        POST /api/exam/score/submit
      
        { "user": "tj859583", "book_id": 518, "test_id": "2", "type": "1" }

    **说明**
    
    - 学生用户提交测试/作业的成绩
    - `type` 表示： 1-测试；2-作业

    **应答**

        { "result": "OK" }

- 获取成绩列表

        POST /api/exam/score/list
      
        { "user": "sjk000103", "book_id": 518, "class_id": "2" }

    **说明**
    
    - 根据一本书，获取一个班的学生分数
    - 仅老师有权限调用该接口
    - 注意一本书对应的既可以是测试，也可以是作业成绩，以实际情况为准
    - 未提交作业的学生成绩部分数据为空

    **应答**

        {
          "teacher": "sjk000103",
          "class_id": "3",
          "scores": [
            {
              "name": "13924758473",
              "display_name": "",
              "user_id": 6,
              "test_id": 1,
              "type": 1,
              "score": 70,
              "created_time": 1437926759
            },
            {
              "name": "13694938574",
              "display_name": "",
              "user_id": 8,
              "test_id": 1,
              "type": 1,
              "score": 90,
              "created_time": 1437929759
            },
            {
              "name": "13923405966",
              "display_name": ""
            }
          ]
        }


## Content API

- 获取书本列表

        POST /api/book
      
        { "ids": "203,204,205" }

    **说明**
    
    - 获取1到n本书的详细信息
    - 只能获取免费或者本人订阅的套餐中已被推送的书本信息
    
    **应答**

        [
          {
            "ID": 203,
            "BOOK_TITLE": "Who Can Help?",
            "BOOK_TITLE_CN": "谁能帮助我",
            "FILE_ID": "GK_U8_DRSB3",
            "GRADE": "0",
            "BOOK_LEVEL": "S"
            ...
          },
          {
            "ID": 204,
            "BOOK_TITLE": "Zip Zippers! Snap Snaps!",
            "BOOK_TITLE_CN": "拉拉链！按扣子！",
            "FILE_ID": "GK_U8_DRSB4",
            "GRADE": "0",
            "BOOK_LEVEL": "S",
            ...
          },
          {
            "ID": 205,
            "BOOK_TITLE": "Doing Our Part",
            "BOOK_TITLE_CN": "各做各事",
            "FILE_ID": "GK_U8_DRSB5",
            "GRADE": "0",
            "BOOK_LEVEL": "S",
            ...
          }
        ]

- 下载书本

        POST /api/book/download

        { "book_id": "205", "file_id": "GK_U8_DRSB5" }

    **说明**
    
    - 本接口不直接返回下载内容，而是以重定向的方式返回下载URL.
    - 需要同时提供 `book_id` 和 `file_id`
    
    **应答**

        HTTP/1.1 302 Moved Temporarily
        Connection: keep-alive
        Content-Length: 0
        Date: Fri, 05 Jun 2015 16:45:27 GMT
        Location: http://example.com/path/file_name.zip?abc=R0tfVTJfRFJJQjQ0MTQzMzUyNjMyNzgMyN5VTJfRF
        Set-Cookie: connect.sid=xxx; Path=/; HttpOnly
        Vary: Accept



## Download API

- 下载

        GET /_download_url_/xxxx


    **说明**
    
    - 本接口用来下载书本文件，请求的 URL 地址为 Content API 中重定向返回的地址
    - `todo` 
    
    **应答**

        HTTP/1.1 200 OK
        Connection: keep-aliveå
        Content-Length: 1034829
        Content-Type: application/x-zip-compressed

- 其它
