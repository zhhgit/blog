---
layout: post
title: "React使用小结"
description: React使用小结
modified: 2016-08-09
category: React
tags: [React]
---

# 一、使用React的方式

1.通过react-tools将转化jsx

	npm install -g react-tools
	jsx --watch script/ js/
	
2.通过create-react-app初始化一个结合了Webpack和ES6的React工程。

    npm install -g create-react-app
    create-react-app my-app
    cd my-app
    npm start

# 二、React组件

SimpleComponent.js:

    import React, { Component } from 'react';
    import AnotherComponent from './AnotherComponent.js';

    export default class SimpleComponent extends Component {
        constructor(props) {
            super(props);
            this.state = {
                name: "Zhang Hao",
            };
        }

        componentDidMount(){
        
        }
        
        handleClick = () => {
        
        }

        render() {
            return (
                <div>
                    {this.state.name}
                    <AnotherComponent />
                </div>
            )
        }
    }

# N、参考

1.[React菜鸟教程](http://www.runoob.com/react/react-tutorial.html)

2.[React官网](https://reactjs.org/)

3.[React 入门实例教程](http://www.ruanyifeng.com/blog/2015/03/react.html)

4.[Babel官网](http://babeljs.io/)

5.[React 组件之间如何交流](http://www.tuicool.com/articles/AzQzEbq)

6.[结合ES6+开发React组件](http://www.oschina.net/question/2012764_242688?fromerr=FNP2HGiK)