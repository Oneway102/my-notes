Demo API 接口说明
====

**说明**: 所有请求必须携带预先申请的参数 `AppKey`。

## Service API

- 注册

        POST /api/auth/register
      
        { "name": "test", "password": "xxxx" }

    **Description**
    
    - `name`
    - `password`
    
    Example:

        {
            "result": "OK"
        }

- 登录

        POST /api/auth/login
      
        { "name": "test", "password": "xxxx" }

    **说明**
    
    - `todo`

    **应答**

        {
            name: "test"
            token: "0b4b55e0-0613-11e5-a69d-746573743100"
        }

- 退出登录

        POST /api/auth/logout
      
        { "name": "test", "password": "xxxx" }

    **Description**
    
    - 可能只有 Web 版本需要使用
    - `TODO`
    
    **Example**

        {
            "result": "OK"
        }

- 获取套餐

        POST /api/bookset/all
      
        {  }

    **Description**
    
    - 获取产品的所有可选套餐，其中包括已订阅和未订阅项
    - 套餐项内包含所有免费得和已推送的书本 **ID** 列表
    
    **Example**

        [
          {
            "id": 10007,
            "name": "一年级",
            "description": "",
            "books": "1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20",
            "free_books": "1,2,3,4,5",
            "subscribed": false
          },
          {
            "id": 10008,
            "name": "这是二年级套餐",
            "description": "",
            "books": "101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132",
            "free_books": "101,102,103,104,105",
            "subscribed": true
          },
          {
            "id": 10009,
            "name": "正式版三年级套餐",
            "description": "",
            "books": "201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238",
            "free_books": "201,202,203,204,205,206,207,208",
            "subscribed": false
          }
        ]

- 创建套餐

        POST /api/bookset/all
      
        { "name": "bookset_name", "books": "book_id_1,book_id_2,book_id_3", "free": "book_id_1,book_id_2" }

    **Description**
    
    - Admin API
    - `free` 是免费书本列表，不论它是否列在 `books` 中，缺省都会将其记做 `books` 的一部分
    
    Example:

        {
            "token": "0b4b55e0-0613-11e5-a69d-746573743100"
        }

- 订阅套餐

        POST /api/bookset/subscribe
      
        { "bookset_id": "bookset_id_1", "months": "4" }

    **Description**
    
    - `months` 为订阅时间.
    - 订阅新套餐后，之前订阅的套餐自动截止。
    
    Examples:

        {
            "token": "0b4b55e0-0613-11e5-a69d-746573743100"
        }



## Content API

- 获取书本列表

        POST /api/book
      
        { "ids": "book_id_1,book_id_2,book_id_3,book_id_4" }

    **Description**
    
    - 可以以1本到n本为单位来获取.
    - `ids`
    
    Example:

        {
            "token": "0b4b55e0-0613-11e5-a69d-746573743100"
        }

- 下载书本

        POST /api/book/download
      
        { "book_id": "book_id_1" }

    **Description**
    
    - 本接口不直接返回下载内容，而是以重定向的方式返回下载URL.
    - `id`
    
    Example:

        {
            "token": "0b4b55e0-0613-11e5-a69d-746573743100"
        }



## Download API

- 下载
- a
