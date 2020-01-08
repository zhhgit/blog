---
layout: post
title: "Java动态代理"
description: Java动态代理
modified: 2020-01-08
category: Java
tags: [Java]
---

共有两种方式，JDK动态代理和CGLIB动态代理

普通的接口与实现类

	public interface ComputeService {
	     int add(int num1, int num2);
	}

	public class ComputeServiceImpl implements ComputeService {

	    @Override
	    public int add(int num1, int num2) {
	        int sum = num1 + num2;
	        System.out.println("结果：" + sum);
	        return sum;
	    }
	}

# 一、JDK动态代理
    
    public class JDKProxySample implements InvocationHandler {
    
        private Object target;
    
        /**
         * 绑定原实现类
         * @param target 原实现类
         * @return 代理类
         */
        public Object bind(Object target){
            this.target = target;
            return Proxy.newProxyInstance(target.getClass().getClassLoader(),target.getClass().getInterfaces(),this);
        }
    
        /**
         * 定义代理类执行逻辑
         * @param proxy bind方法生成的代理对象
         * @param method 当前调度方法
         * @param args 调度方法的参数
         * @return 方法返回值
         * @throws Throwable
         */
        @Override
        public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
            System.out.println("before method");
            // 这里的ret是方法实际的返回值
            Object ret = method.invoke(target,args);
            System.out.println("after method");
            return ret;
        }
    }
    
    public class Test1 {
    
        public static void main(String[] args){
            ComputeService proxy =(ComputeService) new JDKProxySample().bind(new ComputeServiceImpl());
            proxy.add(1,4);
        }
    }

# 二、CGLIB动态代理

    public class CglibProxySample implements MethodInterceptor {
    
        public Object bind(Object target){
            Enhancer enhancer = new Enhancer();
            // 设置父类
            enhancer.setSuperclass(target.getClass());
            // 设置回调，在调用父类方法时，回调 this.intercept()
            enhancer.setCallback(this);
            // 创建代理对象
            return enhancer.create();
        }
    
        /**
         *
         * @param o 代理对象
         * @param method 方法
         * @param objects 方法参数
         * @param methodProxy 方法代理
         * @return 返回值
         * @throws Throwable
         */
        @Override
        public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
            System.out.println("before method");
            Object ret = methodProxy.invokeSuper(o,objects);
            System.out.println("after method");
            return ret;
        }
    }
    
    public class Test2 {
    
        public static void main(String[] args){
            ComputeService proxy =(ComputeService) new CglibProxySample().bind(new ComputeServiceImpl());
            proxy.add(1,4);
        }
    }