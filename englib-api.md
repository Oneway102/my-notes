API
====

**Note**: Example data can be tested using the _PostMan_ plugin for Chrome. All requests should carry the param @AppKey.

## Service API

####  Authentication
  
- Register

        POST /api/auth/register
      
        { "name": "test", "password": "xxxx" }

    **Description**
    
    - `name`
    - `password`
    
    Example:

        {
            "token": "0b4b55e0-0613-11e5-a69d-746573743100"
        }

- Login

        POST /api/auth/login
      
        { "name": "test", "password": "xxxx" }

    **Description**
    
    - `todo`

    Examples:

        {
            "token": "0b4b55e0-0613-11e5-a69d-746573743100"
        }

- Logout

        POST /api/auth/logout
      
        { "name": "test", "password": "xxxx" }

    **Description**
    
    - TODO
    - TODO
    
    **Example**

        {
            "token": "0b4b55e0-0613-11e5-a69d-746573743100"
        }

- 获取套餐

        POST /api/bookset/all
      
        {  }

    **Description**
    
    - 获取产品的所有可选套餐，其中包括已订阅和未订阅项
    - 套餐项内包含所有免费得和已推送的书本 **ID** 列表
    
    **Example**

        {
            "token": "0b4b55e0-0613-11e5-a69d-746573743100"
        }

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
