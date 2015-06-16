Demo API 接口说明
====

**说明**: 所有请求必须携带预先申请的参数 `AppKey` 。

## Service API

- 注册

        POST /api/auth/register
      
        { "user": "test", "password": "xxxx", "type": "1", "icode": "zh8Wke1M" }

    **说明**
    
    - `user`
    - `password`
    - 用户类型为老师时，需携带 `icode` 邀请码参数
    
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

- 获取用户信息

        POST /api/user
      
        { "user": "test", "token": "0b4b55e0-0613-11e5-a69d-746573743100" }

    **说明**
    
    - 获取 `display_name`
    - 将来可能会包含用户其它属性
    
    **应答**

        {
            "user": "test", "display_name": "Wang Xiaoming"
        }

- 更新用户信息

        POST /api/user/upadte
      
        { "user": "test", "token": "0b4b55e0-0613-11e5-a69d-746573743100", "display_name": "Wang Ming" }

    **说明**
    
    - 修改 `display_name`
    - 将来可能会包含用户其它属性
    - 会返回所有可见的用户信息
    
    **应答**

        {
            "user": "test", "display_name": "Wang Xiaoming"
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
            "subscribed": false,
            "sub_status": false
          },
          {
            "id": 10008,
            "name": "这是二年级套餐",
            "description": "",
            "books": "101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132",
            "free_books": "101,102,103,104,105",
            "subscribed": true
            "sub_status": true
          },
          {
            "id": 10009,
            "name": "正式版三年级套餐",
            "description": "",
            "books": "201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238",
            "free_books": "201,202,203,204,205,206,207,208",
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
            "FILE_ID": "GK_U8_DRSB3",
            "GRADE": "0",
            "BOOK_LEVEL": "S"
            ...
          },
          {
            "ID": 204,
            "BOOK_TITLE": "Zip Zippers! Snap Snaps!",
            "FILE_ID": "GK_U8_DRSB4",
            "GRADE": "0",
            "BOOK_LEVEL": "S",
            ...
          },
          {
            "ID": 205,
            "BOOK_TITLE": "Doing Our Part",
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
