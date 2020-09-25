---
layout: post
title: "Shell实例"
description: Shell实例
modified: 2020-09-25
category: Linux
tags: [Linux]
---

# 一、连接数据库与数据处理
    
    #! /bin/sh

    ################配置信息################
    MYSQL=/usr/bin/mysql
    ENCODING=utf8
    DIR=/NFSDir
    IP=1.2.3.4
    PORT=8080
    DBNAME=test
    USER=user
    PASSWD=test

    ################初始化目录################
    if [ -d  $DIR/html ]; then
      rm -rf $DIR/html; 
    fi
      mkdir $DIR/html;
      mkdir $DIR/html/fruit;
      chmod -R 777 $DIR/html;

    if [ -d  $DIR/tmp ]; then
       rm -rf $DIR/tmp;
    fi
       mkdir $DIR/tmp;
       mkdir $DIR/tmp/fruit;
       chmod -R 777 $DIR/tmp;

    ################查询数据库并逐行处理，保存文件################
    $MYSQL -h $IP -P $PORT -u$USER -p$PASSWD -N -e "select fruit.id, fruit.name, fruit.description from $DBNAME.tbl_demo_fruit as fruit where fruit.is_show='1' " --default-character-set=$ENCODING | while read line
    do
      id=`echo $line | awk '{print $1}';`
      echo "---id:$id---";
      echo $line > $DIR/tmp/fruit/$id.html.tmp
      cat $DIR/tmp/fruit/$id.html.tmp |sed 's/<script>.*<\/script>//g'| sed "s/<[^>]*>//g"|sed 's/&nbsp;//g' |sed 's/\\n//g'|sed 's/NULL//g' |sed 's/\r//g' >$DIR/html/fruit/fruit_$id.html
      rm  $DIR/tmp/fruit/$id.html.tmp
    done
    
    ################查询数据库并保存文件################
    $MYSQL -h $IP -P $PORT -u$USER -p$PASSWD -N  -e "select 'fruit','^',id,'^',name from $DBNAME.tbl_demo_fruit " --default-character-set=$ENCODING>>$DIR/tmp/id_name.csv.bak
    
    cat $DIR/tmp/id_name.csv.bak | sed "s/<[^>]*>//g" | sed 's/\\n//g' >$DIR/tmp/id_name.csv
    
# 二、从Git库拉取代码并打包

    #!/bin/sh
    if [ $# -lt 1 ]; then
       echo "example: sh package_git.sh project sub_project branch-name";
       exit 1 
    fi
    
    startTs=`date +%s`;
    moduleName=$1; 
    
    if [ -d $HOME/package/$moduleName ];then
       cd $HOME/package/$moduleName
    else
       echo "please get project src with console: git clone http://xxx.com/project.git"
       exit 0
    fi
    
    if [ -d  $HOME/package/target/ ];then
       rm -r $HOME/package/target;
       mkdir $HOME/package/target;
    else
       mkdir $HOME/package/target;
    fi
    
    if [ $moduleName == 'horizon' ];then
    
        tag=$3
        if [ -n "$tag" ];then
            git checkout $tag
            git pull origin $tag
        else
            git checkout master
            git pull origin master
        fi
    
      subModule=$2
    
      if [ -n "$subModule" ];then
        echo "go in $subModule"
      else
        subModule='default_sub_project';
      fi
    
      if [ -d $HOME/package/$moduleName/$subModule ];then
        cd $subModule
        mvn clean package install -Dmaven.test.skip=true
  
        if [ -f $HOME/package/$moduleName/$subModule/target/$subModule.war ];then
          cp $HOME/package/$moduleName/$subModule/target/$subModule.war $HOME/package/target/.
          echo "---$HOME/package/target/$subModule.war is packaged---";
        fi
  
      else
        echo "---$HOME/package/$moduleName/$subModule is not exist."
        exit 1
      fi
    
    fi
    
    endTs=`date +%s`;
    echo "---spend time : $(($endTs-$startTs)) s ---";
  
# 三、参考

1.[菜鸟Shell教程](https://www.runoob.com/linux/linux-shell.html)

2.[Shell简明教程](https://www.jianshu.com/p/1417b6aef0b2)