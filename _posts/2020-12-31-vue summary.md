---
layout: post
title: "Vue基础"
description: Vue基础
modified: 2020-12-31
category: FrontEnd
tags: [FrontEnd]
---

实践了一下用Vue做项目。官方文档确实很详细。

# 一、使用Vue

使用vue-cli工具可以直接生成

	//安装vue-cli
	npm install --g vue-cli
	//生成一个项目
	vue init webpack my-project-list
	cd my-project-list
	//下载依赖
	npm install
	//开发
	npm run dev
	//生产
	npm run build
	
一个简单的组件结构如下ListItem.vue

	<template>
	  <div class="item">
	    <div v-if="propA === 'hehe'">
            { {computedA} }
	  	</div>
	  	<a v-bind:href="propB" />
            { {propB} }
	  	</a>
	  	<div v-on:click="methodA">
	  	    { {dataA} }
	  	</div>
	  	<Text></Text>
	  </div>
	</template>

	<script>
	  import Text from "./Text.vue";
	  export default {
	    name: 'ListItem',
	    data: function () {
	      return {
	      	dataA: 1
	      }
	    },
	    props: [
	      "propA",
	      "propB"
	    ],
	    computed:{
	      computedA () {
	        return this.dataA + 1;
	      }
	    },
	    components:{
		  Text
	    },
	    methods: {
	      methodA:function(){
	      	this.methodB();
	      },
	      methodB:function(){
	      	console.log("do something");
	      }
	    },
	    created:function(){
	    	console.log("do something when created");
	    }
	  }
	</script>

	<style scoped>
	  .item{
	  }
	</style>

代码结构：组件在src/components下，页面在src/pages下。static目录中放一些直接引用的静态资源，图片，公共的JS和CSS等。

# 二、使用Vue Router

配置文件为src/router/index.js，其中/selected/:query形式为动态匹配，可以带参数

	import Vue from 'vue';
	import Router from 'vue-router';
	import All from '@/pages/All';
	import Selected from "@/pages/Selected";

	Vue.use(Router);

	export default new Router({
	  routes: [
	    {
	      path: '/all',
	      name: 'all',
	      component: All
	    },
	    {
	      path: '/selected/:query',
	      name: 'selected',
	      component: Selected
	    }
	  ]
	});
	
在组件中，分别通过如下的方式实现编程式路由

	router.push({ name: 'all'});
	router.push({ name: 'selected', params: {query: query }});

# 三、使用Vuex

配置文件为src/store/index.js

	import Vue from 'vue';
	import Vuex from 'vuex';
	import navigator from './modules/navigator'

	Vue.use(Vuex);

	export default new Vuex.Store({
	  modules: {
	    navigator
	  },
	})

src/store/modules/navigator.js为navigator这个分模块的配置，每个模块相对独立，分别配置state,actions,mutations

	const state = {
	  show:"0"
	};

	const actions = {
	  changeShowType ({ commit },data) {
	    commit("changeShowType",data);
	  }
	};

	const mutations = {
	  changeShowType(state,payload) {
	    state.show = payload.show;
	  }
	};

	export default {
	  state,
	  actions,
	  mutations
	}

在一个Vue组件或者页面中触发一个action，可以携带数据。

	this.$store.dispatch('changeShowType',{show:"0"});

action会commit一个mutation，mutation又继续改变state，都在navigator.js中很明确。在与这个state相关的组件中，可以通过computed派生出一些值

	selectType () {
	  return this.$store.state.navigator.show;
	}

最后在组件或者页面中使用与state有关的值

    { {selectType} }

# 四、参考

1.[Vue官方文档](https://cn.vuejs.org/v2/guide/index.html)

2.[Vue菜鸟教程](http://www.runoob.com/vue2/vue-tutorial.html)

3.[Vue Router官方文档](https://router.vuejs.org/zh-cn/)

4.[Vuex官方文档](https://vuex.vuejs.org/zh-cn/)