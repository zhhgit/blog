---
layout: post
title: "Eclipses设置与操作"
description: Eclipses设置与操作
modified: 2018-03-08
category: Tools
tags: [Tools]
---

1.代码提示： window-reference-java-editor-content assist-auto activation triggers for java，值为"abcdefghijklmnopqrstuvwxyz."。

2.编辑器编码格式：window-reference-general-workspace-text file encoding，选为other-UTF-8。

3.字号：window-reference-general-appearance-colors and fonts-basic-text font-edit。

4.project facets：尤其是import的项目，右键-properties-project facets，注意检查java版本与JDK版本一致。

5.编译器级别：window-reference-java-compiler-compiler compliance level,选择与JDK一致的版本。对于import的项目，右键-properties，java compiler设置的版本与project facets一致。

6.java build path：import的项目，右键-properties-java build path，libraries中JRE版本与project facets一致。

7.项目-右键-run as-run configurations，JRE版本与project facets一致。

8.查找：search-search-file search

9.maven：window-reference-maven-installations可以添加自己下载的maven，maven-user settings，设置user settings为C:\Users\zhanghao1\.m2\settings.xml，设置local repository为C:\Users\zhanghao1\.m2\repository

10.server：window-reference-server-runtime environments，添加Apache Tomcat。

11.validation：工程validation时间过长，取消所有build列中的勾选。

12.导入工程时，可以先取消勾选project-build automatically，导入之后再勾选。

13.Eclipse插件：help-eclipse marketplace，搜索需要的插件然后install。