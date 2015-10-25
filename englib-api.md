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
    - 目前只有学生用户需要注册
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
    - `account_status` : 1-普通用户；2-VIP用户；3-VIP过期用户

    **应答**

        {
            user: "test",
            token: "0b4b55e0-0613-11e5-a69d-746573743100",
            account_status: 2
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
    
    - `service_type` 表示验证码用途。 1 - 注册；2 - 修改密码；~~3 - 忘记密码~~；4 - 绑定手机
    - 注册或者忘记密码时无需提供 `user` 和 `token` 参数，其它已确定用户名的情况则需要提供该参数
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
    
    - 获取 `display_name` 等信息
    - 同时包含用户其它属性：老师、班级属性
    - 不同类型用户返回的附加信息结构不同
    - `account_status` : 1-普通用户；2-VIP用户；3-VIP过期用户
    
    **应答**

        {
            id: 4,
            name: "13912257904",
            display_name: "xiaoming",
            type: 10,
            phone: "13938373737",
            account_status: 2,
            vip_time: 1441351850,
            teacher: {
                id: 12,
                name: "js962981",
                display_name: "abc"
            },
            class: {
                id: 1,
                name: "className"
                bookset_id: "3",
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
      
        { "user": "test", "token": "0b4b55e0-0613-11e5-a69d-746573743100", "display_name": "Wang Ming", 'gender": "1", "age": "12" }

    **说明**
    
    - 修改 `display_name`，`age`，`gender` 等除了电话号码/电子邮件之外的信息
    - `gender` 性别， 1-男，2-女
    - `display_name` `gender` `age` 等参数至少有一个即可，多值可选
    - 会返回所有可见的基本用户信息
    
    **应答**

        {
            "user": "test", "display_name": "Wang Xiaoming", ...
        }

- 更新用户头像

        POST /api/user/avatar/update
      
        { "user": "13511223344", "avatar": ... }

    **说明**
    
    - 修改用户头像
    - 客户端以 `multipart/form-data` 的格式上传头像文件，头像文件以 '用户名.png/jpg' 形式上传，例如 13511223344.png

    **应答**

        {  result: "OK" }

- 获取用户头像

        GET /api/user/avatar?f=13511223344.png
      
        { ... }

    **说明**
    
    - 获取用户头像
    - 参数：以用户登录ID为文件名
    - token应通过cookie传递
    - ~~客户端需要在接收到内容时分别保存`etag`和`last_modified`，在获取时再分别设置最后获取的时间和ETag（暂时不需要作为HTTP Header参数），以防止服务器重传。（后期转成HTTP Header参数之后，或许可以由Volley来完成缓存功能）~~

    **应答**

        { ... }

- 绑定手机

        POST /api/user/phone/update
      
        { "user": "test", "token": "0b4b55e0-0613-11e5-a69d-746573743100", "phone": "13023456789", "sms_code": "a9Hs", "code_id": 45 }

    **说明**
    
    - 修改 电话号码
    - 用户将来收到手机短信验证码，验证通过才表示修改成功

    **应答**

        {
            result: "OK"
        }

- 忘记密码

        POST /api/user/password/forget
      
        { "password": "jfh958", "phone": "13593847564", "sms_code": "a9Hs", "code_id": 45 }

    **说明**
    
    - 用户忘记密码之后重新设置密码
    - 用户将来收到手机短信验证码，验证通过才表示修改成功

    **应答**

        { result: "OK" }

- 修改密码

        POST /api/user/password/update
      
        { "user": "13529384756", "old_password": "xxxx", "new_password": "cccc" }

    **说明**
    
    - 修改用户密码
    - -

    **应答**

        { result: "OK" }

- 用户通过VIP码付费

        POST /api/order/vip/create
      
        { "user": "13501103333", "vip_code": "abc1284372", "product": "kwb0001" }

    **说明**
    
    - 用户以VIP码的形式付费使用
    - `product` 目前暂为固定值

    **应答**

        { result: "OK" }

- 用户获取订单记录

        POST /api/order/list
      
        { "user": "13501103333", "from_time": "14456789766", "count": "20" }

    **说明**
    
    - 用户获取订单，`from_time`为0时则获取所有订单
    - `type` : 1-支付宝；2-微信支付；3-VIP码支付
    - `quantity`：购买数量，`price`：单价，`total_fee`：总价；`status`：0-未支付；1-已支付

    **应答**

        {
          "user_id": 58,
          "payments": [
            {
              "id": 11,
              "total_fee": "10.00",
              "price": "10.00",
              "type": 1,
              "status": 1,
              "created_time": 1441353852,
              "quantity": 1,
              "subject": "订阅1个月",
              "body": "每月 10.00 元"
            },
            {
              "id": 12,
              "total_fee": "10.00",
              "price": "10.00",
              "type": 1,
              "status": 1,
              "created_time": 1441353991,
              "quantity": 1,
              "subject": "订阅1个月",
              "body": "每月 10.00 元"
            }
          ]
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

- 获取用户当前订阅的套餐

        POST /api/bookset/mine
      
        { "user": "test", "token" :"0b4b55e0-0613-11e5-a69d-746573743100" }

    **说明**
    
    - 获取用户当前订阅的套餐
    - 返回结果只包含套餐ID和名称，具体的套餐书本信息可以通过获取下一个接口获取

    **应答**
        { user: "test", bookset_id: 5, name: "5年级" }

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

- 获取用户的某一个套餐

        POST /api/bookset/one
      
        { "user": "test", "bookset_id" :"1" }

    **说明**
    
    - 获取用户某一个等级的套餐
    - 应答中具体参数可参考获取所有套餐的接口
    
    **应答**
    
        { 略 }

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

- 学生解除绑定老师

        POST /api/teacher/unassociate
      
        { "user": "13828374485" }

    **说明**
    
    - 不需要老师参数
    - 会同时退出相关班级/群组

    **应答**

        { result: "OK" }

- 老师解除绑定一位学生

        POST /api/teacher/s/unassociate
      
        { "user": "tjs000102", "student_id": "13022334455" }

    **说明**
    
    - 注意不要和学生解除绑定的接口混淆
    - 学生会同时退出相关班级/群组

    **应答**

        { result: "OK" }

- 绑定一位教导员

        POST /api/director/associate
      
        { "user": "tjs859583", "director": "tjsd59583" }

    **说明**
    
    - 可以作为老师关联/绑定教导员用，只有老师有权限调用该接口
    -  `director` 为教导员注册ID，非后台数据库ID

    **应答**

        { director: { id: 293, name: "tjsd00102", display_name: "Huang" } }

- 教研员解除一位老师的绑定

        POST /api/director/s/associate
      
        { "user": "tjs859583", "teacher": "tjs59583" }

    **说明**
    
    - 只有教研员有权限调用该接口
    -  `teacher_id` 为老师注册ID，非后台数据库ID

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

- 注销或删除班级

        POST /api/class/delete
      
        { "user": "tj859583", "class_id": 3 }

    **说明**
    
    - 只有老师有权限调用该接口
    - 班级中的所有学生将会自动退出

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

- ~~获取作业列表~~

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

- ~~获取一个测试~~

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

- ~~根据班级获取作业列表~~

        POST /api/exam/class/list
        POST /api/task/class/list
      
        { "user": "tj859583", "class_id": 518, "bookset_id": 34}

    **说明**
    
    - 获取一个班的作业列表
    - `bookset_id` 为班级对应的套餐ID，冗余参数，仅作为参数校验用
    - 应答中的 `book_title` 对应书本题目
    - `TODO` 是否还需要书本ID以及下载URL ?

    **应答**

        {
            "user": "tj859583",
            "class_id": 518,
            "bookset_id": 34,
            "assigned_books": [
            {
              "book_id": 114,
              "class_id": 3,
              "book_title": "Family Time",
              "created_time": 1438960309,
              "exam_id": 2
            },
            {
              "book_id": 130,
              "class_id": 3,
              "book_title": "Hide and Seek!",
              "created_time": 1439015871,
              "exam_id": 3
            }
            ]
        }

- 学生获取作业列表

        POST /api/exam/student/list
        POST /api/task/student/list
      
        { "user": "tj859583", "from":1439389461998, "count": 20 }

    **说明**
    
    - 获取一个学生的作业列表，结果以 `modified_time` 逆序排列
    - 可选参数： `from` 为列表记录开始的时间，返回该时间之前符合条件的数据；`count` 为每次列表数目
    - 应答中的 `created_time` 为作业创建时间， `modified_time` 为最新成绩提交时间
    - 班级/群组的 ID 为0时表示这是系统发布的作业，对应的老师为系统老师

    **应答**

        {
          "id": 6,
          "student": "13924758473",
          "exams": [
            {
              "exam_id": 104,
              "teacher": {
                "id": 0,
                "name": "乐迪老师",
                "display_name": "乐迪老师"
              },
              "class": {
                "id": 0,
                "name": null
              },
              "book": {
                "id": 104,
                "book_title": "I Am Like an Eagle"
              },
              "score": null,
              "created_time": 1439389461998,
              "modified_time": 1439389461998
            },
            {
              "exam_id": 930,
              "teacher": {
                "id": 32,
                "name": "sjk0000103",
                "display_name": "乐迪老师"
              },
              "class": {
                "id": 5,
                "name": "快进班"
              },
              "book": {
                "id": 425,
                "book_title": "We All Work"
              },
              "score": null,
              "created_time": 1439389428071,
              "modified_time": 1439389428071
            },
            {
              "exam_id": 929,
              "teacher": {
                "id": 32,
                "name": "sjk0000103",
                "display_name": "乐迪老师"
              },
              "class": {
                "id": 5,
                "name": "快进班"
              },
              "book": {
                "id": 423,
                "book_title": "A.J.’s New Neighbor"
              },
              "score": null,
              "created_time": 1439389412989,
              "modified_time": 1439389412989
            }
          ]
        }

- 学生获取套餐作业列表

        POST /api/exam/student/bookset/list

        { "user": "13924758473", "bookset_id":5 }

    **说明**
    
    - 获取一个学生的套餐内作业列表
    - 如有需要可返回对应书本ID和title
    
    **说明**

        {
          "user": "13924758473",
          "bookset_id": 5,
          "exams": [
            {
              "exam_id": 929,
              "book_id": 423,
              "score": "A",
              "created_time": 1439389412989
            },
            {
              "exam_id": 930,
              "book_id": 425,
              "score": "0",
              "created_time": 1439389428070
            },
            {
              "exam_id": 425,
              "book_id": 425,
              "score": null,
              "created_time": 1440603671385
            }
          ]
        }

- 提交成绩

        POST /api/exam/score/submit
      
        { "user": "tj859583", "book_id": 518, "exam_id": "2", "score": 80, "from_time": "14412341234", "to_time": "14412341289",  "question_stat": "....." }

    **说明**
    
    - 学生用户提交测试/作业的成绩
    - `from_time` - 开始测试时间，为单位为秒的 Linux 时间戳
    - `question_stat` 为一个JSON数组格式的题目统计信息，包括每道题的知识点、难度、能力点、结果...等原始数据： [{type:1, knowledge:2, ability: 1, difficulty: 3, score: 0/1}]， 其中 score 表示该题是否得分
    - ~~`type` 表示： 1-测试；2-作业~~

    **应答**

        { "result": "OK" }

- 老师获取自己布置的作业列表

        POST /api/exam/teacher/list
      
        { "user": "sjk000103", "from": 518, "count": "20" }

    **说明**
    
    - 获取一位老师布置的作业列表，结果以 `created_time` 逆序排列
    - 可选参数： `from` 为列表记录开始的时间，返回该时间之前符合条件的数据；`count` 为每次列表数目
    - 应答中的 `created_time` 为作业创建时间
    - 仅老师有权限调用该接口

    **应答**

        {
          "id": 32,
          "teacher": "sjk0000103",
          "exams": [
            {
              "exam_id": 931,
              "class": {
                "id": 3,
                "name": "grade2"
              },
              "book": {
                "id": 104,
                "book_title": "I Am Like an Eagle"
              },
              "stat": {
                "total": 1,
                "submitted": 0
              },
              "created_time": 1439389461990
            },
            {
              "exam_id": 930,
              "class": {
                "id": 5,
                "name": "快进班"
              },
              "book": {
                "id": 425,
                "book_title": "We All Work"
              },
              "stat": {
                "total": 2,
                "submitted": 0
              },
              "created_time": 1439389427978
            },
          ]
        }

- 教研员获取相关老师的作业统计信息

        POST /api/exam/director/list
      
        { "user": "sjk000103", "from_time": 1431234567, "to_time": 1431244567 }

    **说明**
    
    - 获取一位教研员相关的老师布置的作业统计信息，主要包括布置作业数目和提交作业的数目
    -  `from_time` 、`to_time`为统计的开始和结束时间，单位为秒，客户端应以周为单位来进行统计和展示
    - 仅教研员有权限调用该接口

    **应答**

        {
          "from_time": xxxxx,
          "to_time": xxxxx,
          "exams": [
            {
              "teacher_id": 32,
              "name": "xxxx",
              "display_name: "xxxxx",
              "total_count": 9,
              "submitted_count": 1
            },
            {
              "teacher_id": "33",
              "name": "xxxx",
              "display_name: "",
              "total_count": 0,
              "submitted_count": 0
            }
          ]
        }

- 根据套餐获取成绩列表

        POST /api/exam/score/user/list
      
        { "user": "13924758473", "book_id": 2 }

    **说明**
    
    - 根据一本书，获取一个学生的测试/作业分数
    - 注意一本书对应的既可以是测试，也可以是作业成绩，以 `type` 标识为准， 1-测试，2-作业。其中测试成绩一般不带有班级和作业ID等信息
    - 未提交的成绩不会列出
    - `score` 成绩类型为字符串，可能是 *0-100* 或者 *A/B/C/D* 的形式

    **应答**

        {
            "user": "13924758473",
            "bookset_id": 2,
            "scores": [
                {
                    "user_id": 6,
                    "class_id": 3,
                    "book_id": 114,
                    "exam_id": 2,
                    "type": 2,
                    "score": "B",
                    "created_time": 1438961086,
                    "modified_time": 1438962280,
                    "book_title": "Family Time"
                },
                {
                    "user_id": 6,
                    "class_id": null,
                    "book_id": 104,
                    "exam_id": null,
                    "type": 1,
                    "score": "d",
                    "created_time": 1438962369,
                    "modified_time": 1438962596,
                    "book_title": "I Am Like an Eagle"
                },
                {
                    "user_id": 6,
                    "class_id": 0,
                    "book_id": 105,
                    "exam_id": 0,
                    "type": 1,
                    "score": "d",
                    "created_time": 1438962608,
                    "modified_time": 1438962608,
                    "book_title": "Jed Learns to Tie His Shoes"
                }
            ]
        }

- 根据某个测试获取学生成绩列表

        POST /api/exam/score/list
      
        { "user": "sjk000103", "exam_id": 5 }

    **说明**
    
    - 老师获取某个作业/书本的所有学生的测试分数
    - 因为书本ID不能唯一确定一个作业，因此以 `exam_id` 作为输入参数
    - 未提交的成绩为 null
    - 结果以 `modified_time` 即学生提交作业的时间为逆序排列
    - `score` 成绩类型为字符串，可能是 *0-100* 或者 *A/B/C/D* 的形式

    **应答**
    
        {
          "id": 32,
          "teacher": "sjk0000103",
          "exam_id": 929,
          "students": [
            {
              "user_id": 6,
              "book_id": 423,
              "score": "A",
              "created_time": 1439389412989,
              "modified_time": 1439389461999,
              "name": "13924758473",
              "display_name": ""
            },
            {
              "user_id": 8,
              "book_id": 423,
              "score": null,
              "created_time": 1439389412991,
              "modified_time": 1439389412991,
              "name": "13694938574",
              "display_name": ""
            }
          ]
        }

- 根据班级获取成绩列表

        POST /api/exam/score/class/list
      
        { "user": "sjk000103", "class_id": 5 }

    **说明**
    
    - 获取一个班级中的学生的测试/作业分数
    - 注意一本书对应的既可以是测试，也可以是作业成绩，以 `type` 标识为准， 1-测试，2-作业。其中测试成绩一般不带有班级和作业ID等信息
    - 未提交的成绩不会列出
    - `score` 成绩类型为字符串，可能是 *0-100* 或者 *A/B/C/D* 的形式

    **应答**

        {
            "user": "sjk0000103",
            "class_id": "3",
            "scores": [
                {
                  "user_id": 6,
                  "name": "13924758473",
                  "display_name": "",
                  "book_id": 114,
                  "exam_id": 2,
                  "type": 2,
                  "score": "B",
                  "assigned_time": 1438961086,
                  "submit_time": 1438962280
                },
                {
                  "user_id": 8,
                  "name": "13694938574",
                  "display_name": "",
                  "book_id": 114,
                  "exam_id": 2,
                  "type": 2,
                  "score": "B",
                  "assigned_time": 1439005980,
                  "submit_time": 1439005980
                },
                {
                  "user_id": 9,
                  "name": "13923405966",
                  "display_name": ""
                }
            ]
        }

- 获取系统消息

        POST /api/msg/list
      
        { "user": "13503948573", "from_time": 13435678953 }

    **说明**
    
    - 获取系统消息
    - `from_time` 为起始时间，单位为秒。如果不填或者为0，则系统自动会返回上次查询时间之后的新消息
    - 应答中的时间戳单位为秒
    - `type` 表示消息类型，客户端可以暂时不作处理
    - `user_id` 等于用户ID时表示用户个人消息， 为 0 时表示系统消息

    **应答**

        {
          "name": "13503948573",
          "messages": [
            {
              "id": 1,
              "user_id": 1,
              "type": 3,
              "subject": "",
              "body": "修改了自己的个人信息",
              "created_time": 1441900131
            },
            {
              "id": 2,
              "user_id": 1,
              "type": 2,
              "subject": "",
              "body": "修改手机号码成功",
              "created_time": 1441984355
            }
          ]
        }

- 上报书本阅读进度

        POST /api/stat/book/progress/update

        { "user": "13022334455", "book_id": "205", "progress": "80" }

    **说明**
    
    - 学生上报一本书的阅读进度.
    - `progress` 为 0-100 之间的数字。如果上报的进度小于当前服务器端进度，则保持后者
    - 应答会返回用户的当前进度，注意这个值可能大于用户设置的进度值
    - 当进度为100时，并且该学生没有绑定老师，系统会自动为其布置作业（参考返回应答中的 `exam_assigned` 参数）
    
    **应答**

        { "user": "13022334455", "book_id": "205", "progress": 80, "exam_assigned": false }

- 获取用户书本阅读进度

        POST /api/stat/book/progress

        { "user": "13022334455", "book_id": "205" }

    **说明**
    
    - 包括阅读进度等信息，返回的统计数据会根据需要逐渐增加.
    - `progress` 为 0-100 之间的数字
    
    **应答**

        { user: "13022334455", book_id: 205, progress: 85 }

- 获取用户某个套餐的书本阅读进度

        POST /api/stat/bookset/progress

        { "user": "13022334455", "bookset_id": "5" }

    **说明**
    
    - 包括阅读进度等信息，返回的统计数据会根据需要逐渐增加.
    - `progress` 为 0-100 之间的数字
    - 返回结果中不列出未上报过进度或进度为0的书本
    
    **应答**

        {
          "user": "13924758473",
          "bookset_id": 5,
          "progress": [
            {
              "id": 425,
              "progress": 100,
              "modified_time": 1440602519445
            },
            {
              "id": 426,
              "progress": 80,
              "modified_time": 1440602519445
            }
          ]
        }

- 上报用户读书行为

        POST /api/stat/book/reading/report

        { "user": "13022334455", "book_id": "205", "bookset_id": "2", "from_time": "14412341234", "to_time": "14412341289", "from_page": "3", "to_page": "3", "total_page": "13" }

    **说明**
    
    - 学生上报一本书阅读行为，每次关闭书本时上报.
    - `from_time` - 开始阅读时间，为单位为秒的 Linux 时间戳

    **应答**

        { "result": "OK" }

- 阅读人数统计

        POST /api/stat/reading/student

        { "teacher_id": "32", "bookset_id": "2", "from_time": "14412341234", "to_time": "14412341289" }

    **说明**
    
    - 统计一个老师的学生总数和参数阅读的学生数
    - `from_time` - 开始统计时间，为单位为秒的 Linux 时间戳
    - `teacher_id` 可换为 `director_id`，注意不是 login ID

    **应答**

        {
          "from_time": 14412341234,
          "to_time": 14412341289,
          "student_total": 5,
          "student_read": 2
        }

- 阅读次数统计

        POST /api/stat/reading

        { "teacher_id": "32", "bookset_id": "2", "from_time": "14412341234", "to_time": "14412341289" }

    **说明**
    
    - 统计一个老师的学生阅读次数
    - `from_time` - 开始统计时间，为单位为秒的 Linux 时间戳
    - `teacher_id` 可换为 `director_id`，注意不是 login ID

    **应答**

        {
          "from_time": 14412341234,
          "to_time": 14412341289,
          "reading_count": 289,
          "book_count": 30
        }

- 阅读时长统计

        POST /api/stat/reading/time

        { "teacher_id": "32", "bookset_id": "2", "from_time": "14412341234", "to_time": "14412341289" }

    **说明**
    
    - 统计一个老师的学生阅读时长（单位为秒）
    - `from_time` - 开始统计时间，为单位为秒的 Linux 时间戳
    - `teacher_id` 可换为 `director_id`，注意不是 login ID

    **应答**

        {
          "from_time": 14412341234,
          "to_time": 14412341289,
          "reading_time": 294589
        }

- 阅读排行统计

        POST /api/stat/reading/rank

        { "teacher_id": "32", "bookset_id": "2", "from_time": "14412341234", "to_time": "14412341289" }

    **说明**
    
    - 统计一个老师的学生阅读书本前10名
    - `from_time` - 开始统计时间，为单位为秒的 Linux 时间戳。暂时不用
    - `teacher_id` 可换为 `director_id`，参数为空时表示查询全局统计(注意不是 login ID)

    **应答**

        {
          "from_time": 14412341234,
          "to_time": 14412341289,
          "books": [
            { book_title: "xxx", book_title_cn: "xxx", file_id: "xxx", id: "1", count: 122 },
            { book_title: "xxx", book_title_cn: "xxx", file_id: "xxx", id: "2", count: 89 },
          ]
        }

- 作业知识点统计

        POST /api/stat/book/exam/knowledge

        { "teacher_id": "32", "bookset_id": "2", "from_time": "14412341234", "to_time": "14412341289" }

    **说明**
    
    - 统计一个老师的学生作业的各个知识点正确率
    - `from_time` - 开始统计时间，为单位为秒的 Linux 时间戳
    - `teacher_id` 可换为 `director_id`，注意不是 login ID
    - 返回结果中，`total`表示总题数，`correct`表示正确答对的题数

    **应答**

        {
          "from_time": 99,
          "to_time": 1543166696,
          "knowledge_stat": [
            {
              "knowledge": "1",
              "total": 93,
              "correct": 76,
              "tag": "名称"
            },
            {
              "knowledge": "11",
              "total": 4,
              "correct": 2,
              "tag": "惯用短语和句型"
            },
            {
              "knowledge": "23",
              "total": 13,
              "correct": 10,
              "tag": "主旨大意"
            },
            {
              "knowledge": "24",
              "total": 21,
              "correct": 16,
              "tag": "推理判断"
            }
          ]
        }

- 作业题目认知能力统计

        POST /api/stat/book/exam/ability

        { "teacher_id": "32", "bookset_id": "2", "from_time": "14412341234", "to_time": "14412341289" }

    **说明**
    
    - 统计一个老师的学生作业的各个知识点正确率
    - `from_time` - 开始统计时间，为单位为秒的 Linux 时间戳
    - `teacher_id` 可换为 `director_id`，注意不是 login ID
    - 返回结果中，`total`表示总题数，`correct`表示正确答对的题数

    **应答**

        {
          "from_time": 99,
          "to_time": 1543166696,
          "ability_stat": [
            {
              "ability": "1",
              "total": 77,
              "correct": 64,
              "tag": "识记"
            },
            {
              "ability": "2",
              "total": 20,
              "correct": 14,
              "tag": "理解"
            },
            {
              "ability": "3",
              "total": 19,
              "correct": 14,
              "tag": "简单运用"
            },
            {
              "ability": "4",
              "total": 15,
              "correct": 12,
              "tag": "综合应用"
            }
          ]
        }

- 查询系统最新版本

        POST /api/config/check/update
        { client: "android", current_version: 16, branch: "stable" }

    **说明**
    
    - 本接口用来查询客户端最新版本
    - `client`：android 或者 ios
    - `branch`: stable 表示发布版本，dev 表示开发版本
    - 如果最新版本与客户端当前版本一致或暂无更新，应答中可能不会携带 `version_name` 和 `url`
    
    **应答**

        {
            latest_version: 18,
            version_name: “”0.13.1,
            url: “xxx"
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


