---
layout: post
title: "python统计与绘图"
description: python统计与绘图
modified: 2021-02-22
category: Python
tags: [Python]
---

# 一、基础

1.统计与绘图

    # 均值
    import numpy
    speed = [99,86,87,88,111,86,103,87,94,78,77,85,86]
    x = numpy.mean(speed)
    print(x)
    
    # 中值
    import numpy    
    speed = [99,86,87,88,111,86,103,87,94,78,77,85,86]
    x = numpy.median(speed)
    print(x)
    
    # 众数，输出的是ModeResult(mode=array([86]), count=array([3]))，分别是众数和出现次数
    from scipy import stats
    speed = [99,86,87,88,111,86,103,87,94,78,77,85,86]
    x = stats.mode(speed)
    print(x)
    
    # 方差σ^2
    import numpy
    speed = [32,111,138,28,59,77,97]
    x = numpy.var(speed)
    print(x)

    # 标准差σ
    import numpy
    speed = [32,111,138,28,59,77,97]
    x = numpy.std(speed)
    print(x)

    # 百分位数，即75%的数值小于等于该值
    import numpy
    ages = [5,31,43,48,50,41,7,11,15,39,80,82,32,2,8,6,25,36,27,61,31]
    x = numpy.percentile(ages, 75)
    print(x)
    
    # 生成随机分布数据集并绘制直方图
    import numpy
    import matplotlib.pyplot as plt
    # 生成随机分布数据集，0.0-5.0之前的数，250个数
    x = numpy.random.uniform(0.0, 5.0, 250)
    # 绘制直方图，参数5跟上面的5.0没有关系，是指格子数，即分布划分为5个区间。
    plt.hist(x, 5)
    plt.show()
    
    # 创建正态分布数据集，创建的数组（具有 100000 个值）绘制具有 100 栏的直方图。平均值为5.0，标准差为1.0。
    import numpy
    import matplotlib.pyplot as plt
    x = numpy.random.normal(5.0, 1.0, 100000)
    plt.hist(x, 100)
    plt.show()
    
    # 绘制散点图
    import numpy
    import matplotlib.pyplot as plt
    x = numpy.random.normal(5.0, 1.0, 1000)
    y = numpy.random.normal(10.0, 2.0, 1000)
    plt.scatter(x, y)
    plt.show()
    
    # 绘制线性回归线
    import matplotlib.pyplot as plt
    from scipy import stats
    x = [5,7,8,7,2,17,2,9,4,11,12,9,6]
    y = [99,86,87,88,111,86,103,87,94,78,77,85,86]
    #  拟合度用r平方（r-squared）的值来度量。r平方值的范围是0到1，其中0表示不相关，而1表示100％相关。
    slope, intercept, r, p, std_err = stats.linregress(x, y)
    # myfunc就是线性回归拟合函数
    def myfunc(x):
      return slope * x + intercept  
    mymodel = list(map(myfunc, x))
    plt.scatter(x, y)
    plt.plot(x, mymodel)
    plt.show()
    
    # 多项式回归
    import numpy
    import matplotlib.pyplot as plt
    x = [1,2,3,5,6,7,8,9,10,12,13,14,15,16,18,19,21,22]
    y = [100,90,80,60,60,55,60,65,70,70,75,76,78,79,90,99,99,100]
    # 三次方拟合，polyfit返回各次方的数值组成的数组，poly1d返回拟合函数
    mymodel = numpy.poly1d(numpy.polyfit(x, y, 3))
    # 1-22之间的100个值，横轴
    myline = numpy.linspace(1, 22, 100)
    plt.scatter(x, y)
    plt.plot(myline, mymodel(myline))
    plt.show()
    
    # 多元回归，即z = ax + by
    import pandas
    from sklearn import linear_model
    df = pandas.read_csv("cars.csv")
    x = df[['Weight', 'Volume']]
    y = df['CO2']
    regr = linear_model.LinearRegression()
    regr.fit(x, y)
    # 预测重量为2300kg、排量为1300ccm 的汽车的二氧化碳排放量
    predictedCO2 = regr.predict([[2300, 1300]])
    print(predictedCO2)
    # 获得系数a和b
    print(regr.coef_)
    
    # 特征缩放
    import pandas
    from sklearn import linear_model
    from sklearn.preprocessing import StandardScaler
    scale = StandardScaler()
    df = pandas.read_csv("cars2.csv")
    x = df[['Weight', 'Volume']]
    y = df['CO2']
    # 标准化方法使用以下公式：z = (x - u) / s，其中z是新值，x是原始值，u是平均值，s是标准差。
    scaledX = scale.fit_transform(x)
    regr = linear_model.LinearRegression()
    regr.fit(scaledX, y)
    scaled = scale.transform([[2300, 1.3]])
    predictedCO2 = regr.predict([scaled[0]])
    print(predictedCO2)
    
    # 训练与测试，将数据集分为两组：训练集和测试集。80％用于训练，20％用于测试。
    import numpy
    import matplotlib.pyplot as plt
    from sklearn.metrics import r2_score
    numpy.random.seed(2)
    x = numpy.random.normal(3, 1, 100)
    y = numpy.random.normal(150, 40, 100) / x
    # 训练集
    train_x = x[:80]
    train_y = y[:80]
    # 测试集
    test_x = x[80:]
    test_y = y[80:]
    # 4次方拟合
    mymodel = numpy.poly1d(numpy.polyfit(train_x, train_y, 4))
    myline = numpy.linspace(0, 6, 100)
    plt.scatter(train_x, train_y)
    plt.plot(myline, mymodel(myline))
    plt.show()
    # R2，也称为R平方（R-squared）
    r2 = r2_score(train_y, mymodel(train_x))
    print(r2)
    
    # 决策树
    import pandas
    from sklearn import tree
    from sklearn.tree import DecisionTreeClassifier
    df = pandas.read_csv("shows.csv")
    # 转换为数值
    d = {'UK': 0, 'USA': 1, 'N': 2}
    df['Nationality'] = df['Nationality'].map(d)
    d = {'YES': 1, 'NO': 0}
    df['Go'] = df['Go'].map(d)
    features = ['Age', 'Experience', 'Rank', 'Nationality']
    # 表示4列的值
    X = df[features]
    y = df['Go']
    dtree = DecisionTreeClassifier()
    dtree = dtree.fit(X, y)
    print(dtree.predict([[40, 10, 6, 1]]))
    print("[1] means 'GO'")
    print("[0] means 'NO'")

# 二、参考

1.[w3school机器学习](https://www.w3school.com.cn/python/python_ml_getting_started.asp)