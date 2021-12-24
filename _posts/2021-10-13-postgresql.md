---
layout: post
title: "PostgreSQL基础"
description: PostgreSQL基础
modified: 2021-10-13
category: Database
tags: [Database]
---

# 一、基础

    -- 查看版本
    SELECT version();

    -- 创建用户
    create user tester with password '123456';

    -- 创建数据库，并指定所属者
    create database test owner tester;

    -- 将数据库得权限，全部赋给某个用户
    grant all on database test to tester;

    -- psql连接数据库（Linux）
    psql -h localhost -p 5432 -U tester test

    -- 已连接后，打开某个数据库
    \c test

    -- 查看全部表名
    select * from pg_tables where schemaname = 'test'

    -- 查看关联，表、索引等
    \d test.*

    -- 查看表字段
    \d test.t_training_class_config

    -- 创建模式
    create schema test authorization tester;

    -- 建表
    DROP TABLE IF EXISTS "test"."t_training_class_config";
    CREATE TABLE "test"."t_training_class_config"
    (
    "id"            int4      NOT NULL,
    "class_cd"      varchar(255) DEFAULT NULL,
    "class_name"    varchar(255) DEFAULT NULL,
    "type"          int4      NULL,
    "class_status"  int4      NULL,
    "expire_time"   timestamp NULL,
    "deadline_time" timestamp NULL,
    "operator"      varchar(255) DEFAULT NULL,
    "data_status"   int4      NULL,
    "create_time"   timestamp NULL,
    "update_time"   timestamp NULL
    );
    COMMENT ON TABLE test.t_training_class_config IS '课程表';
    
    COMMENT ON COLUMN "test"."t_training_class_config"."id" IS '主键ID';
    COMMENT ON COLUMN "test"."t_training_class_config"."class_cd" IS '课程编号';
    COMMENT ON COLUMN "test"."t_training_class_config"."class_name" IS '课程名称';
    COMMENT ON COLUMN "test"."t_training_class_config"."type" IS '类型';
    COMMENT ON COLUMN "test"."t_training_class_config"."class_status" IS '课程状态：0有效，1无效';
    COMMENT ON COLUMN "test"."t_training_class_config"."expire_time" IS '失效时间';
    COMMENT ON COLUMN "test"."t_training_class_config"."deadline_time" IS '截止完成时间';
    COMMENT ON COLUMN "test"."t_training_class_config"."operator" IS '操作人';
    COMMENT ON COLUMN "test"."t_training_class_config"."data_status" IS '数据状态：0有效，1删除';
    COMMENT ON COLUMN "test"."t_training_class_config"."create_time" IS '创建时间';
    COMMENT ON COLUMN "test"."t_training_class_config"."update_time" IS '更新时间';
    
    ALTER TABLE "test"."t_training_class_config" ADD CONSTRAINT "t_training_class_config_pkey" PRIMARY KEY ("id");

    -- 新增序列
    DROP SEQUENCE if EXISTS "test"."seq_training_class_config_id";
    CREATE SEQUENCE "test"."seq_training_class_config_id"
    INCREMENT 1
    MINVALUE 1
    MAXVALUE 2147483647
    START 1
    CACHE 1;

    -- 自增主键
    CREATE TABLE COMPANY(
    ID  SERIAL PRIMARY KEY,
    NAME           TEXT      NOT NULL,
    AGE            INT       NOT NULL,
    ADDRESS        CHAR(50),
    SALARY         REAL
    );

    -- 普通查询
    SELECT column1, column2
    FROM table1, table2
    WHERE [ conditions ]
    GROUP BY column1, column2
    HAVING [ conditions ]
    ORDER BY column1, column2
    
    -- 从上到下递归查询
    WITH RECURSIVE obj as (
    SELECT organization_id, rec_status FROM t_xtgl_organization where organization_id = #{orgId,jdbcType=BIGINT}
    UNION ALL
    SELECT c.organization_id, c.rec_status FROM t_xtgl_organization c join obj on c.parent_id =
    obj.organization_id
    )
    select organization_id from obj where
    obj.rec_status = '1'

# 二、参考

1.[为什么“去O”唯有PG](https://dbaplus.cn/news-19-2765-1.html)

2.[【干货总结】:可能是史上最全的MySQL和PGSQL的对比材料](https://www.cnblogs.com/lyhabc/p/11628042.html)

3.[菜鸟PostgreSQL教程](https://www.runoob.com/postgresql/postgresql-tutorial.html)

4.[易百PostgreSQL教程](https://www.yiibai.com/postgresql/)