---
layout: post
title: "量化系列 -- Python Web"
description: 量化系列 -- Python Web
modified: 2026-01-01
category: Quant
tags: [Quant]
---

# 数据库访问

1.例子

    import mysql.connector
    mydb = mysql.connector.connect(
      host="localhost",
      user="yourusername",
      passwd="yourpassword",
      database="mydatabase"
    )
    
    # 新增一条数据
    mycursor = mydb.cursor()
    sql = "INSERT INTO customers (name, address) VALUES (%s, %s)"
    val = ("John", "Highway 21")
    mycursor.execute(sql, val)
    mydb.commit()
    # 获取刚插入的行的id
    print(mycursor.rowcount, "record inserted. ID: ", mycursor.lastrowid)
    
    # 新增多条数据
    mycursor = mydb.cursor()
    sql = "INSERT INTO customers (name, address) VALUES (%s, %s)"
    val = [
      ('Peter', 'Lowstreet 4'),
      ('Amy', 'Apple st 652')
    ]
    mycursor.executemany(sql, val)
    mydb.commit()
    print(mycursor.rowcount, "was inserted.")
    
    # 查询
    import mysql.connector
    mydb = mysql.connector.connect(
      host="localhost",
      user="yourusername",
      passwd="yourpassword",
      database="mydatabase"
    )
    mycursor = mydb.cursor()
    mycursor.execute("SELECT name, address FROM customers")
    # 返回所有行
    myresult = mycursor.fetchall()
    for x in myresult:
      print(x)
      
    # 返回结果的第一行
    myresult = mycursor.fetchone()
    print(myresult)
    
    # 带条件查询
    sql = "SELECT * FROM customers WHERE address = %s"
    adr = ("some address", )
    mycursor.execute(sql, adr)
    myresult = mycursor.fetchall()
    for x in myresult:
      print(x)
      
    # 删除
    sql = "DELETE FROM customers WHERE address = %s"
    adr = ("some address", )
    mycursor.execute(sql, adr)
    mydb.commit()
    print(mycursor.rowcount, "record(s) deleted")
    
    # 修改
    sql = "UPDATE customers SET address = %s WHERE address = %s"
    val = ("Valley 345", "Canyon 123")
    mycursor.execute(sql, val)
    mydb.commit()
    print(mycursor.rowcount, "record(s) affected")

# FastAPI

1.安装

    pip install "fastapi[standard]"

N.参考

(1)[FastAPI官网](https://fastapi.tiangolo.com/zh/learn/#sponsors)

(2)[FastAPI菜鸟教程](https://www.runoob.com/fastapi/fastapi-tutorial.html)