---
layout: post
title: "Python系列 -- Sklearn基础"
description: Python系列 -- Sklearn基础
modified: 2022-01-01
category: Python
tags: [Python]
---

# 基本概念

1.sklearn是什么

机器学习是计算机科学的一个分支，研究的是无需人类干预，能够自己学习的算法。 与TensorFlow不同，Scikit-learn(sklearn)的定位是通用机器学习库，而TensorFlow(tf)的定位主要是深度学习库。
SciKit-Learn是SciKit库的一部分，SciKit意思是SciPy Toolkits，名字来源于SciPy库，SciKit基于SciPy库构建，除了SciKit-Learn，还包含其他很多模块，可以打开这个网址查看。SciKit-Learn库是专注于机器学习和数据挖掘的模块。

# 加载数据

1.数据类型可以是NumPy数组、SciPy稀疏矩阵，或者其他可转换为数组的类型，如panda DataFrame等。

例1：

    from sklearn import datasets
    import pandas as pd
    import numpy as np
    
    '''
    SciKit-Learn库中也自带一些数据集，我们可以尝试加载。
    先从sklearn导入数据集模块，然后，可以使用数据集中的load_digits()方法加载数据
    '''
    # 加载 `digits` 数据集
    digits = datasets.load_digits()
    # 打印 `digits` 数据
    print(digits)
    print('\n')
    '''
    查看数据集中包含哪些主要内容：
    data 样本数据
    target 目标值
    target_names 目标名称
    images 图像格式(二维)的样本数据
    DESCR 描述信息
    '''
    print(digits.keys())
    # 打印描述
    print(digits.DESCR)
    # 打印数据内容
    print(digits.data)
    # 打印目标值
    print(digits.target)
    # 打印目标名称(标签)
    print(digits.target_names)
    
    '''
    接下来，我们进一步了解数据集中的数据。
    可以看到，数据集中的数据都是numpy数组的格式，可以查看这些数组的数据类型，形状，长度等信息。
    digits.data中，有1797个样本，每个样本有64个特征值(实际上是像素灰度值)。
    digits.target中，包含了上面样本数据对应的目标值（样本标签），同样有1797个目标值，但10个唯一值，即0-9。换句话说，所有1797个目标值都由0到9之间的数字组成，这意味着模型要识别的是从0到9的数字。
    digits.target_names包含了样本标签的名称: 0~9。
    digits.images数组包含3个维度: 有1797个实例，大小为8×8像素。digits.images数据与digits.data内容应该相同，只是格式不同。可以通过以下方式验证两者内容是否相同：
    '''
    # 打印data数组的形状
    print(digits.data.shape) # 输出：(1797, 64)
    # 打印data数组的类型
    print(digits.data.dtype) # 输出：float64
    
    # 打印target数组的形状
    print(digits.target.shape) # 输出：(1797,)
    # 打印target数组的类型
    print(digits.target.dtype) # 输出：int32
    # 打印target数组中包含的唯一值数量
    print(len(np.unique(digits.target))) # 输出：10
    
    # 打印target_names数组的形状
    print(digits.target_names.shape) # 输出：(10,)
    # 打印target_names数组的类型
    print(digits.target_names.dtype) # 输出：int32
    
    # 打印images数组的形状
    print(digits.images.shape) # 输出：(1797, 8, 8)
    # 打印images数组的类型
    print(digits.images.dtype) # 输出：float64
    
    # 把digits.images改变形状为(1797, 64)，与digits.data比较，两者相等。numpy方法all()可以检测所有数组元素的值是否为True。
    print(np.all(digits.images.reshape((1797, 64)) == digits.data)) # 输出：true

    
    # 另一种方式加载数据集， 使用 `read_csv()` 加载数据集
    # 文件后缀是.tra，表示是训练(train)数据集，在这个页面内还可以看到.tes文件，表示是测试(test)数据集，所以上面加载的数据集，是已经分割好训练数据集和测试数据集的。示例中，只加载了训练数据集。
    # 注意：如果使用read_csv()导入数据集，数据集已经分割好，导入的数据集中可能没有描述字段，但是你可以使用head()或tail()来检查数据。在这种情况下，最好仔细查看数据描述文件夹!
    digits = pd.read_csv("http://archive.ics.uci.edu/ml/machine-learning-databases/optdigits/optdigits.tra", header=None)
    # 打印 `digits` 数据
    print(digits)

例2：

    from sklearn import datasets
    import matplotlib.pyplot as plt
    
    # 加载 `digits` 数据集
    digits = datasets.load_digits()
    # 设置图形大小(宽、高)以英寸为单位
    fig = plt.figure(figsize=(6, 6))
    # 设置子图形布局，如间隔之类...
    fig.subplots_adjust(left=0, right=1, bottom=0, top=1, hspace=0.05, wspace=0.05)
    # 对于64幅图像中的每一幅
    for i in range(64):
        # 初始化子图:在8×8的网格中，在第i+1个位置添加一个子图
        ax = fig.add_subplot(8, 8, i + 1, xticks=[], yticks=[])
        # 在第i个位置显示图像
        ax.imshow(digits.images[i], cmap=plt.cm.binary, interpolation='nearest')
        # 用目标值标记图像
        ax.text(0, 7, str(digits.target[i]))
    
    # 显示图形
    plt.show()
    
    
    # 我们也可以使用digits.target中的目标值标记digits.images图像格式的样本数据，并显示。
    # 加载 `digits` 数据集
    digits = datasets.load_digits()
    # 把图像和目标标签组合成一个列表
    images_and_labels = list(zip(digits.images, digits.target))
    # 对于列表(前8项)中的每个元素
    for index, (image, label) in enumerate(images_and_labels[:8]):
        # 在第i+1个位置初始化一个2X4的子图
        plt.subplot(2, 4, index + 1)
        # 不要画坐标轴
        plt.axis('off')
        # 在所有子图中显示图像
        plt.imshow(image, cmap=plt.cm.gray_r,interpolation='nearest')
        # 为每个子图添加一个标题(目标标签)
        plt.title('Training: ' + str(label))
    
    # 显示图形
    plt.show()

# 预处理

1.常见方法

(1)标准化/Standardization

    from sklearn.preprocessing import StandardScaler
    scaler = StandardScaler().fit(X_train)
    standardized_X = scaler.transform(X_train)
    standardized_X_test = scaler.transform(X_test)

(2)归一化/Normalization

    from sklearn.preprocessing import Normalizer
    scaler = Normalizer().fit(X_train)
    normalized_X = scaler.transform(X_train)
    normalized_X_test = scaler.transform(X_test)

(3)二值化/Binarization

    from sklearn.preprocessing import Binarizer
    binarizer = Binarizer(threshold=0.0).fit(X)
    binary_X = binarizer.transform(X)

(4)类别特征编码

    from sklearn.preprocessing import LabelEncoder
    enc = LabelEncoder()
    y = enc.fit_transform(y)

(5)缺失值估算

    from sklearn.preprocessing import Imputer
    imp = Imputer(missing_values=0, strategy='mean', axis=0)
    imp.fit_transform(X_train)

(6)生成多项式特征

    from sklearn.preprocessing import PolynomialFeatures
    poly = PolynomialFeatures(5)
    oly.fit_transform(X)

(7)训练与测试数据分组

    from sklearn.model_selection import train_test_split
    X_train, X_test, y_train, y_test = train_test_split(X,y,random_state=0)

2.例子

    '''
    数据标准化：
    大型数据分析项目中，数据来源不同，量纲及量纲单位不同，为了让它们具备可比性，需要采用标准化方法消除由此带来的偏差。原始数据经过数据标准化处理后，各指标处于同一数量级，适合进行综合对比评价。这就是数据标准化。
    对单个指标进行比较，假设对3名新生婴儿体重（5，6，7）和3名成年人的体重（150，151，152）差异的大小进行对比分析，从表面上看，两组人员的平均差异均为1斤，由此便得出两组人员的体重差异程度相同显然是不合适，因为两者的体重水平不在同一等级上，即量纲不同；
    对多个指标进行综合分析，假设对商品的运营指标销售量、销售额、浏览量进行综合评价或聚类分析，由于各指标间的水平相差很大，如果直接进行分析会突出数值较高的指标在综合分析中的作用，从而使各个指标以不等权参与运算。
    因此，常常需要先对数据进行标准化，对各统计指标进行无量纲化处理，消除量纲影响和变量自身变异大小和数值大小的影响。
    
    数据标准化的常用方法：
    
    (1)Max-Min标准化/离差标准化
    也称为离散标准化，是对原始数据的线性变换，将数据值映射到[0, 1]之间。转换公式为：x'=(x-min)/(max-min)，其中max为样本的最大值，min为样本的最小值。
    离差标准化保留了原来数据中存在的关系，是消除量纲和数据取值范围影响的最简单方法。其缺陷是当有新数据加入时，可能导致max或min的变化，转换函数需要重新定义。
    
    (2)Z-score标准化/标准差标准化/零均值标准化
    Z-score也称标准差标准化，经过处理的数据的均值为0，标准差为1。
    转化公式为：x'=(x-μ)/σ，其中μ为所有样本数据的均值，σ为所有样本数据的标准差。
    该方法对离群点不敏感，当原始数据的最大值、最小值未知或离群点左右了Max-Min标准化时非常有用，Z-Score标准化目前使用最为广泛的标准化方法。
    
    (3)log函数转换
    通过以10为底的log函数转换的方法同样可以实现归一化，数据都要大于等于1
    转化公式为：x'=log10(x)/log10(max)，其中max为样本数据最大值
    
    (4)sklearn数据标准化
    sklearn.preprocessing提供了许多方便的用于做数据预处理工具，在数据标准化方面，sklearn.preprocessing提供了几种scaler进行不同种类的数据标准化操作。
    '''
    
    from sklearn import datasets
    from sklearn.preprocessing import scale
    import numpy as np
    from sklearn.model_selection import train_test_split
    
    # 加载 `digits` 数据集
    digits = datasets.load_digits()
    # 对`digits.data`数据进行标准化处理
    # 数据进行标准化处理后，可以将每个属性的分布改为均值为零，标准差为1(单位方差)。
    data = scale(digits.data)
    print(data)
    
    '''
    将数据分解为训练子集和测试子集
    数据集除了用来训练模型，还需用来评估模型。需要将数据集分为两部分:训练子集和测试子集。
    实践中，训练子集和测试子集是不互相覆盖的: 最常见的分割选择是将原始数据集的2/3作为训练集，剩下的1/3将组成测试集。
    可以使用train_test_split函数将数据集随机划分为训练子集和测试子集，并返回划分好的训练集测试集样本和训练集测试集标签。
    '''
    # 将数据集随机划分为训练子集和测试子集。
    # 数据分成训练集和测试集
    # `test_size`：如果是浮点数，在0-1之间，表示测试子集占比；如果是整数的话就是测试子集的样本数量，`random_state`：是随机数的种子
    # 随机数的产生取决于种子，随机数和种子之间的关系遵从以下两个规则：种子不同，产生不同的随机数；种子相同，即使实例不同也产生相同的随机数。
    # 随机数种子，其实就是该组随机数的编号，在需要重复试验的时候，种子相同可以保证得到一组一样的随机数。比如你每次都填1，其他参数一样的情况下，得到的随机数组是一样的，但填0或不填，则每次都会不一样。
    # 可以看到，训练集X_train现在包含1203个样本，正好是原始数据集的2/3，每个样本有64个特征值。y_train训练集也包含原始数据集标签的2/3，测试集X_test和y_test各自包含剩下的594个样本。
    X_train, X_test, y_train, y_test, images_train, images_test = train_test_split(data, digits.target, digits.images, test_size=0.33, random_state=42)
    # 训练集样本数量和特征值数量
    n_samples, n_features = X_train.shape
    # 输出样本数量`n_samples`
    print(n_samples)
    # 输出特征值数量 `n_features`
    print(n_features)
    # 训练集标签数量
    n_digits = len(np.unique(y_train))
    # 打印`y_train`
    print(len(y_train))

# 模型

1.创建模型

有监督学习模型：

(1)线性回归

    from sklearn.linear_model import LinearRegression
    lr = LinearRegression(normalize=True)

(2)支持向量机(SVM)

    from sklearn.svm import SVC
    svc = SVC(kernel='linear')

(3)朴素贝叶斯

    from sklearn.naive_bayes import GaussianNB
    gnb = GaussianNB()

(4)KNN

    from sklearn.neighbors import KNeighborsClassifier
    knn=KNeighborsClassifier()

无监督学习模型：

(1)主成分分析(PCA)

    from sklearn.decomposition import PCA
    pca = PCA(n_components=0.95)

(2)k均值/K Means

    from sklearn.cluster import KMeans
    k_means = KMeans(n_clusters=3, random_state=0)

2.模型拟合

有监督学习：

    lr.fit(X, y)
    svc.fit(X_train, y_train)
    knn.fit(X_train, y_train)

无监督学习：

    k_means.fit(X_train)
    pca_model = pca.fit_transform(X_train)

3.模型预测

有监督学习：

    y_pred = lr.predict(X_test)
    y_pred = svc.predict(np.random.random((2,5)))
    y_pred = knn.predict_proba(X_test))

无监督学习：

    y_pred = k_means.predict(X_test)

4.评估模型性能

(1)分类指标

a.准确度

    knn.score(X_test, y_test)
    from sklearn.metrics import accuracy_score
    accuracy_score(y_test, y_pred)

b.分类报告

    from sklearn.metrics import classification_report
    print(classification_report(y_test, y_pred)))
    
c.混淆矩阵

    from sklearn.metrics import confusion_matrix
    print(confusion_matrix(y_test, y_pred)))

(2)回归指标

a.平均绝对误差

    from sklearn.metrics import mean_absolute_error
    y_true = [3, -0.5, 2])
    mean_absolute_error(y_true, y_pred))

b.均方差

    from sklearn.metrics import mean_squared_error
    mean_squared_error(y_test, y_pred))

c.R^2分数

    from sklearn.metrics import r2_score
    r2_score(y_true, y_pred))

(3)聚类指标

a.调整兰德系数

    from sklearn.metrics import adjusted_rand_score
    adjusted_rand_score(y_true, y_pred))

b.同质性/Homogeneity

    from sklearn.metrics import homogeneity_score
    homogeneity_score(y_true, y_pred))

c.调和平均指标/V-measure

    from sklearn.metrics import v_measure_score
    metrics.v_measure_score(y_true, y_pred))

d.交叉验证

    print(cross_val_score(knn, X_train, y_train, cv=4))
    print(cross_val_score(lr, X, y, cv=2))

5.模型调优

(1)网格搜索

    from sklearn.grid_search import GridSearchCV
    params = {"n_neighbors": np.arange(1,3), "metric": ["euclidean", "cityblock"]}
    grid = GridSearchCV(estimator=knn,param_grid=params)
    grid.fit(X_train, y_train)
    print(grid.best_score_)
    print(grid.best_estimator_.n_neighbors)

(2)随机参数优化

    from sklearn.grid_search import RandomizedSearchCV
    params = {"n_neighbors": range(1,5), "weights": ["uniform", "distance"]}
    rsearch = RandomizedSearchCV(estimator=knn, param_distributions=params, cv=4, n_iter=8, random_state=5)
    rsearch.fit(X_train, y_train)
    print(rsearch.best_score_)

6.例子

(1)SVM例子

    '''
    前面章节尝试了K均值聚类模型，准确率并不高。接下来我们尝试一种新方法：支持向量机(SVM)。
    支持向量机(support vector machine/SVM)，通俗来讲，它是一种二类分类模型，其基本模型定义为特征空间上的间隔最大的线性分类器，其学习策略便是间隔最大化，最终可转化为一个凸二次规划问题的求解。
    本系列教程聚焦于SciKit-Learn 库的使用介绍，关于支持向量机详细原理，限于篇幅不再赘述，读者可参考相关资料。
    如前所诉，K均值聚类模型是一种无监督学习模型，与此不同，支持向量机是一种有监督学习模型，是很常用的一种机器学习模型。
    '''
    import numpy as np
    from sklearn import datasets
    from sklearn.model_selection import train_test_split
    from sklearn import svm
    import matplotlib.pyplot as plt
    from sklearn import metrics
    from sklearn.manifold import Isomap
    from sklearn.model_selection import GridSearchCV
    
    
    # 加载 `digits` 数据集
    digits = datasets.load_digits()
    # 数据分成训练集和测试集
    # `test_size`：如果是浮点数，在0-1之间，表示测试子集占比；如果是整数的话就是测试子集的样本数量，`random_state`：是随机数的种子
    X_train, X_test, y_train, y_test, images_train, images_test = train_test_split(digits.data, digits.target, digits.images, test_size=0.33, random_state=42)
    # 创建SVC/Support Vector Classification/支持向量机分类器模型
    # 可以看到，我们使用X_train和y_train数据来训练SVC模型(Support Vector Classification/支持向量机分类器模型)，此处用到了标签值y_train，是有监督学习。
    # 另外，我们手动设置了gamma的值，通过使用网格搜索和交叉验证等工具，可以自动找到合适的参数值。
    svc_model = svm.SVC(gamma=0.001, C=100., kernel='linear')
    # 将数据拟合到SVC模型中，此处用到了标签值y_train，是有监督学习
    svc_model.fit(X_train, y_train)
    # 接下来，我们使用测试数据测试模型。
    # 预测“X_test”标签
    print(svc_model.predict(X_test))
    # 打印' y_test '检查结果
    print(y_test)
    
    # 我们也可以使用matplotlib可视化测试数据及其预测标签
    # 将预测值赋给 `predicted`
    predicted = svc_model.predict(X_test)
    # 将images_test和images_prediction中的预测值压缩在一起
    images_and_predictions = list(zip(images_test, predicted))
    # 对于images_and_prediction中的前四个元素
    for index, (image, prediction) in enumerate(images_and_predictions[:4]):
        # 在坐标i+1处初始化一个1×4的网格中的子图
        plt.subplot(1, 4, index + 1)
        # 不显示坐标轴
        plt.axis('off')
        # 在网格中的所有子图中显示图像
        plt.imshow(image, cmap=plt.cm.gray_r, interpolation='nearest')
        # 添加标题
        plt.title('Predicted: ' + str(prediction))
    
    # 显示图形
    plt.show()
    
    # 最后，我们来评估一下模型的性能，看看它准确率怎么样。
    # 打印的分类报告 `y_test` 与 `predicted`
    print(metrics.classification_report(y_test, predicted))
    # 打印“y_test”和“predicted”的混淆矩阵，可以看到，这个模型比前面章节的K均值聚类模型，效果要好得多。
    print(metrics.confusion_matrix(y_test, predicted))
    
    # 我们再看一下预测标签与实际标签的散点图，从图中可以看出，模型的预测结果与实际结果匹配度非常好，模型的准确率很高。
    # 创建一个isomap，并将“digits”数据放入其中。isomap等度量映射，是一种降维方法
    X_iso = Isomap(n_neighbors=10).fit_transform(X_train)
    # 计算聚类中心并预测每个样本的聚类指数
    predicted = svc_model.predict(X_train)
    # 在1X2的网格中创建带有子图的图
    fig, ax = plt.subplots(1, 2, figsize=(8, 4))
    # 调整布局
    fig.subplots_adjust(top=0.85)
    # 将散点图添加到子图中
    ax[0].scatter(X_iso[:, 0], X_iso[:, 1], c=predicted)
    ax[0].set_title('Predicted labels')
    ax[1].scatter(X_iso[:, 0], X_iso[:, 1], c=y_train)
    ax[1].set_title('Actual Labels')
    # 加标题
    fig.suptitle('Predicted versus actual labels', fontsize=14, fontweight='bold')
    # 显示图形
    plt.show()

    
    '''
    前面在创建模型时，手动设置了gamma等参数的值，通过使用网格搜索和交叉验证等工具，可以自动找到合适的参数值。
    尽管这不是本篇教程的重点，本节内容演示如何使用网格搜索和交叉验证等工具，自动找到合适的参数值。
    '''
    # 设置参数候选项
    parameter_candidates = [
        {'C': [1, 10, 100, 1000], 'kernel': ['linear']},
        {'C': [1, 10, 100, 1000], 'gamma': [0.001, 0.0001], 'kernel': ['rbf']},
    ]
    # 使用参数候选项创建分类器
    clf = GridSearchCV(estimator=svm.SVC(), param_grid=parameter_candidates, n_jobs=-1)
    # 根据训练数据训练分类器
    clf.fit(X_train, y_train)
    # 打印结果
    print('训练数据的最佳得分:', clf.best_score_)
    print('最佳惩罚参数C:',clf.best_estimator_.C)
    print('最佳内核类型:',clf.best_estimator_.kernel)
    print('最佳gamma值:',clf.best_estimator_.gamma)
    # 将分类器应用到测试数据上，查看准确率得分
    clf.score(X_test, y_test)
    # 用网格搜索参数训练一个新的分类器，评估得分
    score = svm.SVC(C=10, kernel='rbf', gamma=0.001).fit(X_train, y_train).score(X_test, y_test)
    print(score)

(2)PCA例子

    from sklearn import datasets
    from sklearn.decomposition import PCA
    import matplotlib.pyplot as plt
    
    # 加载 `digits` 数据集
    digits = datasets.load_digits()
    
    
    '''
    主成分分析(PCA)是一种常用于减少大数据集维数的降维方法，把大变量集转换为仍包含大变量集中大部分信息的较小变量集。
    减少数据集的变量数量，自然是以牺牲精度为代价的，降维的好处是以略低的精度换取简便。因为较小的数据集更易于探索和可视化，并且使机器学习算法更容易和更快地分析数据，而不需处理无关变量。
    总而言之，主成分分析(PCA)的概念很简单——减少数据集的变量数量，同时保留尽可能多的信息。
    使用scikit-learn，可以很容易地对数据进行主成分分析
    '''
    
    # 创建一个随机的PCA模型，该模型包含两个组件
    randomized_pca = PCA(n_components=2, svd_solver='randomized')
    # 拟合数据并将其转换为模型
    reduced_data_rpca = randomized_pca.fit_transform(digits.data)
    
    # 创建一个常规的PCA模型
    pca = PCA(n_components=2)
    # 拟合数据并将其转换为模型
    reduced_data_pca = pca.fit_transform(digits.data)
    
    # 检查形状
    print(reduced_data_pca.shape)
    # 打印数据
    print(reduced_data_rpca)
    print(reduced_data_pca)
    
    '''
    随机的PCA模型在维数较多时性能更好。可以比较常规PCA模型与随机PCA模型的结果，看看有什么不同。
    告诉模型保留两个组件，是为了确保有二维数据可用来绘图。
    现在可以绘制一个散点图来可视化数据:
    '''
    colors = ['black', 'blue', 'purple', 'yellow', 'white', 'red', 'lime', 'cyan', 'orange', 'gray']
    # 根据主成分分析结果绘制散点图
    for i in range(len(colors)):
        # 分别获取target值为0~9的数据的两个维度x,y值
        x = reduced_data_rpca[:, 0][digits.target == i]
        y = reduced_data_rpca[:, 1][digits.target == i]
        plt.scatter(x, y, c=colors[i])
    
    # 设置图例，0-9用不同颜色表示
    plt.legend(digits.target_names, bbox_to_anchor=(1.05, 1), loc=2, borderaxespad=0.)
    # 设置坐标标签
    
    plt.xlabel('First Principal Component')
    plt.ylabel('Second Principal Component')
    # 设置标题
    plt.title("PCA Scatter Plot")
    
    # 显示图形
    plt.show()

(3)K-means例子

    '''
    到目前为止，我们已经非常深入地了解了数据集，并且把它分成了训练子集与测试子集。
    接下来，我们将使用聚类方法训练一个模型，然后使用该模型来预测测试子集的标签，最后评估该模型的性能。
    聚类(clustering)是在一组未标记的数据中，将相似的数据（点）归到同一个类别中的方法。聚类与分类的最大不同在于分类的目标事先已知，而聚类则不知道。
    K均值聚类是聚类中的常用方法，它是基于点与点的距离来计算最佳类别归属，即靠得比较近的一组点（数据）被归为一类，每个聚类都有一个中心点。
    我们首先创建聚类模型，对训练子集进行聚类处理，得到聚类中心点。然后使用模型预测测试子集的标签，预测时根据测试子集中的点（数据）到中心点的距离来进行分类。
    '''
    
    import numpy as np
    from sklearn import datasets
    from sklearn.preprocessing import scale
    from sklearn.model_selection import train_test_split
    from sklearn import cluster
    import matplotlib.pyplot as plt
    from sklearn import metrics
    from sklearn.metrics import homogeneity_score, completeness_score, v_measure_score, adjusted_rand_score, adjusted_mutual_info_score, silhouette_score
    
    # 加载 `digits` 数据集
    digits = datasets.load_digits()
    # 对`digits.data`数据进行标准化处理
    data = scale(digits.data)
    # 数据分成训练集和测试集
    # `test_size`：如果是浮点数，在0-1之间，表示测试子集占比；如果是整数的话就是测试子集的样本数量，`random_state`：是随机数的种子
    X_train, X_test, y_train, y_test, images_train, images_test = train_test_split(data, digits.target, digits.images, test_size=0.33, random_state=42)
    
    '''
    创建KMeans模型
    cluster.KMeans的参数说明：
    init='k-means++' – 指定初始化方法
    n_clusters=10 – 聚类数量，分成10个类别
    random_state=42 – 随机值种子
    '''
    clf = cluster.KMeans(init='k-means++', n_clusters=10, random_state=42)
    # 将训练数据' X_train '拟合到模型中，此处没有用到标签数据y_train，K均值聚类一种无监督学习。
    clf.fit(X_train)
    
    
    '''
    我们利用K均值聚类方法创建一个模型后，得到了每个聚类的中心点，测试时，可以根据测试子集中的点（数据）到中心点的距离来进行分类。
    可以使用下面方法显示聚类中心点图像
    '''
    # 图形尺寸(英寸)
    fig = plt.figure(figsize=(8, 3))
    # 添加标题
    fig.suptitle('Cluster Center Images', fontsize=14, fontweight='bold')
    # 对于所有标签(0-9)
    for i in range(10):
        # 在一个2X5的网格中，在第i+1个位置初始化子图
        ax = fig.add_subplot(2, 5, 1 + i)
        # 显示图像
        ax.imshow(clf.cluster_centers_[i].reshape((8, 8)), cmap=plt.cm.binary)
        # 不要显示坐标轴
        plt.axis('off')
    # 显示图形
    plt.show()
    
    '''
    测试模型
    接下来预测测试子集的标签
    在上面的代码块中，预测测试集的标签，结果存储在y_pred中。然后打印出y_pred和y_test的前100个实例。可以看出模型预测的准确率并不高。
    '''
    # 预测“X_test”的标签
    y_pred=clf.predict(X_test)
    # 打印出' y_pred '的前100个实例
    print(y_pred[:100])
    # 打印出' y_test '的前100个实例
    print(y_test[:100])
    
    
    '''
    评估模型
    接下来，我们将进一步对模型的性能进行评估，分析模型预测的正确性。
    让我们打印一个混淆矩阵:
    混淆矩阵也称误差矩阵，是表示精度评价的一种标准格式，用n行n列的矩阵形式来表示，每一列代表预测值，每一行代表实际的类别。
    混淆矩阵对角线上的值表示预测值匹配数，其他位置表示错配的数量。例如第一行第二列是54，表示实际值是分类0，但预测值是分类1的错误预测发生了54次。
    可以看出模型预测的准确率并不高：数字3预测对了49次，数字7预测对了55次，其他的都很低。
    '''
    # 用“confusion_matrix()”打印出混淆矩阵
    print(metrics.confusion_matrix(y_test, y_pred))
    
    
    '''
    让我们继续打印一些常用的评估指标：
    homogeneity_score 同质性指标，每个群集只包含单个类的成员。
    completeness_score 完整性指标，给定类的所有成员都分配给同一个群集。
    v_measure_score 同质性指标与完整性指标的调和平均。
    adjusted_rand_score 调整兰德指数
    adjusted_mutual_info_score 调整互信息
    silhouette_score 轮廓系数
    '''
    print('% 9s' % 'inertia    homo   compl  v-meas     ARI AMI  silhouette')
    print('%i   %.3f   %.3f   %.3f   %.3f   %.3f    %.3f'
        %(clf.inertia_,
        homogeneity_score(y_test, y_pred),
        completeness_score(y_test, y_pred),
        v_measure_score(y_test, y_pred),
        adjusted_rand_score(y_test, y_pred),
        adjusted_mutual_info_score(y_test, y_pred),
        silhouette_score(X_test, y_pred, metric='euclidean')))

# N、参考

1.[奇客谷sklearn教程](https://www.qikegu.com/docs/4065)

2.[奇客谷sklearn速查](https://www.qikegu.com/docs/4093)

3.[sklearn官网user guide](https://scikit-learn.org/stable/user_guide.html)

4.[sklearn中文文档](https://sklearn.apachecn.org/#/)

5.[openml数据集网站](https://www.openml.org/)

6.[UCI机器学习库数据集](http://archive.ics.uci.edu/ml/datasets)

7.[Kaggle](http://www.kaggle.com/)
