---
layout: post
title: "量化系列 -- Python工具"
description: 量化系列 -- Python工具
modified: 2026-01-01
category: Quant
tags: [Quant]
---

# NumPy

N.参考

(1)[W3school Numpy](https://www.w3school.com.cn/python/numpy_intro.asp)

(2)[NumPy官网](http://www.numpy.org.cn/)

(3)[菜鸟NumPy教程](https://www.runoob.com/numpy/numpy-tutorial.html)

# SciPy

1.模块划分

    scipy.cluster	Vector quantization / Kmeans （矢量量化、K均值聚类）
    scipy.constants	物理和数学常数
    scipy.fftpack	傅里叶变换
    scipy.integrate	积分
    scipy.interpolate	插值
    scipy.io	输入输出
    scipy.linalg	线性代数
    scipy.ndimage	多维图像处理
    scipy.odr	Orthogonal(正交) distance regression
    scipy.optimize	优化
    scipy.signal	信号处理
    scipy.sparse	稀疏矩阵
    scipy.spatial	Spatial data structures and algorithms
    scipy.special	特殊函数
    scipy.stats	统计

2.SciPy与NumPy

默认情况下，所有NumPy函数都可以在SciPy(命名空间)中使用。当导入SciPy时，不需要显式地导入NumPy函数。
NumPy的主要对象是n次多维数组ndarray，SciPy构建在ndarray数组之上，ndarray是存储单一数据类型的多维数组。在NumPy中，维度称为轴，坐标轴的数量称为秩。
ndarray是NumPy中最重要的类。标准的Python列表(list)中，元素是对象。如：L = [1, 2, 3]，需要3个指针和三个整数对象，对于数值运算比较浪费资源。与此不同，ndarray中元素直接存储为原始数据，元素的类型由ndarray对象中的属性dtype描述。
当ndarray数组中的元素，通过索引或切片返回时，会根据dtype，从原始数据转换成Python对象，以便外部使用。

3.special，特殊函数模块

scipy.special模块中包含了一些常用的杂项函数，例如经常使用的：立方根函数、指数函数、相对误差指数函数、对数和指数函数、兰伯特函数、排列组合函数

    from scipy.special import cbrt
    # 解立方根
    res = cbrt([1000, 27, 8, 23])
    print (res)

4.cluster，K均值聚类模块

聚类(K-means clustering)是在一组未标记的数据中，将相似的数据（点）归到同一个类别中的方法。聚类与分类的最大不同在于分类的目标事先已知，而聚类则不知道。
K-means是聚类中最常用的方法之一，它是基于点与点的距离来计算最佳类别归属，即靠得比较近的一组点（数据）被归为一类。
K-means的算法原理如下：

    随机选取k个点作为中心点；
    遍历所有点，将每个点划分到最近的中心点，形成k个聚类；
    根据聚类中点之间的距离，重新计算各个聚类的中心点；
    重复2-3步骤，直到这k个中线点不再变化（收敛了），或达到最大迭代次数；
    SciPy中，cluster包已经很好地实现了K-Means算法，我们可以直接使用它。

    from scipy.cluster.vq import kmeans,vq,whiten
    from numpy import vstack,array
    from numpy.random import rand
    
    # 准备样本数据：
    # 具有3个特征值的样本数据生成
    data = vstack((rand(100,3) + array([.5,.5,.5]),rand(100,3)))
    print(data)
    print(data.shape)
    
    # 数据白化预处理是一种常见的数据预处理方法，作用是去除样本数据的冗余信息。
    # 白化数据
    data = whiten(data)
    
    # 分成3个聚类，我们会把样本数据分成3个聚类，使用kmeans()函数计算3个聚类的中心点。
    # 计算 K = 3 时的中心点
    centroids, _ = kmeans(data, 3)
    
    # 打印中心点：
    print(centroids)
    
    # 使用vq函数将样本数据中的每个样本点分配给一个中心点，形成3个聚类。
    # 返回值clx标出了对应索引样本的聚类，dist表示对应索引样本与聚类中心的距离。
    clx, dist = vq(data, centroids)
    
    # 打印聚类
    # 输出值0,1,2分别表示3个聚类，某个位置上的值表示该对应索引数据属于哪个聚类，例如clx[0]=1，表明data[0]样本属于聚类1。
    print(clx)

5.constants，常数模块

    from scipy.constants import pi
    print(pi)

数学常量：

    pi	pi
    golden	黄金比例

物理常数：

    c   真空中的光速
    speed_of_light  真空中的光速
    h   普朗克常数
    Planck  普朗克常量
    G   牛顿的引力常数
    e   基本电荷
    R   摩尔气体常数
    Avogadro    阿伏伽德罗常数
    k   玻耳兹曼常量
    electron_mass或者m_e   电子质量
    proton_mass或者m_p    质子质量
    neutron_mass或者m_n   中子质量

单位：

    milli	0.001
    micro	1e-06
    kilo	1000

其他常量：

    gram	1克
    atomic mass	原子质量常数
    degree	1弧度
    minute	60秒
    day	一天几秒
    inch	一英寸表示为多少米
    micron	一微米表示为多少米
    light_year	一光年表示为多少米
    atm	帕斯卡为单位表示的标准大气压
    acre	一英亩表示为多少平方米
    liter	一升以立方米为单位表示
    gallon	一加仑以立方米为单位表示
    kmh	千米每小以米每秒为单位表示
    degree_Fahrenheit	凯尔文氏以华氏度表示
    eV	1电子伏以焦耳表示
    hp	马力以瓦特表示
    dyn	达因以牛顿表示
    lambda2nu	将波长转换为光学频率

6.fftpack，傅里叶变换模块

SciPy提供了fftpack模块，包含了傅里叶变换的算法实现。
傅里叶变换把信号从时域变换到频域，以便对信号进行处理。傅里叶变换在信号与噪声处理、图像处理、音频信号处理等领域得到了广泛应用。
快速傅里叶变换：计算机只能处理离散信号，使用离散傅里叶变换(DFT) 是计算机分析信号的基本方法。但是离散傅里叶变换的缺点是：计算量大，时间复杂度太高，当采样点数太高的时候，计算缓慢，由此出现了DFT的快速实现，即快速傅里叶变换FFT。
快速傅里叶变换（FFT）是计算量更小的离散傅里叶变换的一种实现方法，其逆变换被称为快速傅里叶逆变换（IFFT）。

    import numpy as np
    from scipy.fftpack import fft,ifft
    
    '''
    先对数据进行fft变换，然后再ifft逆变换。
    从fftpack中导入fft(快速傅里叶变化)和ifft(快速傅里叶逆变换)函数
    可以看到fft，ifft返回的都是复数。ifft返回的结果中，复数的虚部都是0，实部与原始数据x一致。这些点的频率无法计算，因为没有设置这N个点的时间长度。如不理解，不必深究，后面会介绍。
    '''
    # 创建一个随机值数组
    x = np.array([1.0, 2.0, 1.0, -1.0, 1.5])
    
    # 对数组数据进行傅里叶变换
    y = fft(x)
    print('fft: ')
    print(y)
    print(y.shape)
    print('\n')
    
    # 快速傅里叶逆变换
    yinv = ifft(y)
    print('ifft: ')
    print(yinv)
    print('\n')

我们知道，傅里叶变换把时域信号变为频域信号。在离散傅里叶变换中，频域信号由一系列不同频率的谐波（频率成倍数）组成。fft返回值是一个复数数组，每个复数表示一个正弦波。通常一个波形由振幅，相位，频率三个变量确定，可以从fft的返回值里，获取这些信息。
假设a是时域中的周期信号，采样频率为Fs，采样点数为N。如果A[N] = fft(a[N])，返回值A[N]是一个复数数组，其中：

    A[0]表示频率为0hz的信号，即直流分量。
    A[1:N/2]包含正频率项，A[N/2:]包含负频率项。正频率项就是转化后的频域信号，通常我们只需要正频率项，即前面的n/2项，负频率项是计算的中间结果（正频率项的镜像值）。
    每一项的频率计算：假设A[i]为数组中的元素，表示一个波形，该波形的频率 = i * Fs / N
    A[i] = real + j * imag，是一个复数，相位就是复数的辐角，相位 = arg(real/imag)
    类似的，振幅就是复数的模，振幅 = sqrt(real^2+imag^2)。但是fft的返回值的模是放大值，直流分量的振幅放大了N倍，弦波分量的振幅放大了N/2倍。

频率分辨率：离散傅里叶变换(DFT)频域相邻刻度之间的实际频率之差。采样时，数据采样了T秒(T = 采样点数N / 采样频率Fs)，信号的成分中周期最大也就是T秒，最低频率即“基频”就等于1 / T，也就是Fs / N，这就是频率分辨率。
基频 = Fs / N，各个谐波的频率就是 i * Fs / N，这个公式用于计算各个波形的频率。

    '''
    实际示例
    '''
    
    # 采样点数
    N = 4000
    # 采样频率 (根据采样定理，采样频率必须大于信号最高频率的2倍，信号才不会失真)
    Fs = 8000
    
    # x是时间序列，0.5秒内采样4000个点，采样频率8000
    x = np.linspace(0.0, N/Fs, N)
    
    # 时域信号，包含：直流分量振幅1.0，正弦波分量频率100hz/振幅2.0, 正弦波分量频率150Hz/振幅0.5/相位np.pi。两个波叠加。
    y = 1.0 + 2.0 * np.sin(100.0 * 2.0*np.pi*x) + 0.5*np.sin(150.0 * 2.0*np.pi*x + np.pi)
    
    # 进行fft变换
    yf = fft(y)
    
    # 获取振幅，取复数的绝对值，即复数的模
    abs_yf = np.abs(yf)
    
    # 获取相位，取复数的角度
    angle_y=np.angle(yf)
    
    # 直流信号
    print('\n直流信号')
    print('振幅:', abs_yf[0]/N) # 直流分量的振幅放大了N倍
    
    # 100hz信号
    index_100hz = 100 * N // Fs # 波形的频率 = i * Fs / N，倒推计算索引：i = 波形频率 * N / Fs
    print('\n100hz波形')
    print('振幅:', abs_yf[index_100hz] * 2.0/N) # 弦波分量的振幅放大了N/2倍
    print('相位:', angle_y[index_100hz])
    
    # 150hz信号
    index_150hz = 150 * N // Fs # 波形的频率 = i * Fs / N，倒推计算索引：i = 波形频率 * N / Fs
    print('\n150hz波形')
    print('振幅:', abs_yf[index_150hz] * 2.0/N) # 弦波分量的振幅放大了N/2倍
    print('相位:', angle_y[index_150hz])
    print('100hz与150hz相位差:',  angle_y[index_150hz] - angle_y[index_100hz])
    print('\n')

离散余弦变换(DCT)：由于许多要处理的信号都是实信号，在使用FFT时，对于实信号，傅立叶变换的共轭对称性导致在频域中有一半的数据冗余。
离散余弦变换（DCT）是对实信号定义的一种变换，变换后在频域中得到的也是一个实信号，相比离散傅里叶变换DFT而言, DCT可以减少一半以上的计算。
DCT还有一个很重要的性质（能量集中特性）：大多书自然信号（声音、图像）的能量都集中在离散余弦变换后的低频部分，因而DCT在（声音、图像）数据压缩中得到了广泛的使用。由于DCT是从DFT推导出来的另一种变换，因此许多DFT的属性在DCT中仍然是保留下来的。
SciPy.fftpack中，提供了离散余弦变换(DCT)与离散余弦逆变换(IDCT)的实现。

    y = dct(np.array([4., 3., 5., 10., 5., 3.]))
    print(y)
    
    # 离散余弦逆变换(idct)，是离散余弦变换(DCT)的反变换。
    y = idct(np.array([4., 3., 5., 10., 5., 3.]))
    print(y)

7.integrate，积分模块

Scipy中的integrate模块提供了很多数值积分方法，例如，一重积分、二重积分、三重积分、多重积分、高斯积分等等。

(1)一重积分

SciPy积分模块中，quad函数是一个重要函数，用于求一重积分。例如，在给定的a到b范围内，对函数f(x)求一重积分。
quad的一般形式是scipy.integrate.quad(f, a, b)，其中f是求积分的函数名称，a和b分别是下限和上限。
quad函数返回两个值，第一个值是积分的值，第二个值是对积分值的绝对误差估计。

    import scipy.integrate
    from numpy import exp
    
    # 高斯函数的例子，求0到5范围内的积分。使用lambda表达式来完成，然后使用quad方法对其求一重积分。
    f = lambda x:exp(-x**2)
    i = scipy.integrate.quad(f, 0, 5)
    print(i)
    
    # 如果积分的函数f带系数参数，那么a和b可以通过args传入quad函数：
    def f(x, a, b):
        return a * (x ** 2) + b
    ret = scipy.integrate.quad(f, 0, 1, args=(3, 1))
    print (ret)

(2)重积分

要计算二重积分、三重积分、多重积分，可使用dblquad、tplquad和nquad函数。大部分场景quad和dblquad就够用了。

    # 二重积分dblquad的一般形式是scipy.integrate.dblquad(func, a, b, gfun, hfun)，其中，func是待积分函数的名称，a、b是x变量的上下限，gfun、hfun为定义y变量上下限的函数名称。
    # 我们使用lambda表达式定义函数f、g和h。注意，在很多情况下g和h可能是常数，但是即使g和h是常数，也必须被定义为函数。
    # 参考 https://www.qikegu.com/docs/3499
    f = lambda x, y : 19*x*y
    g = lambda x : 0
    h = lambda y : sqrt(1-4*y**2)
    i = scipy.integrate.dblquad(f, 0, 0.5, g, h)
    print (i)

8.interpolate，插值模块

插值，是依据一系列的点(xi,yi)通过一定的算法找到一个合适的函数来包含(逼近)这些点，反应出这些点的走势规律，然后根据走势规律求其他点值的过程。
scipy.interpolate包里有很多类可以实现对一些已知的点进行插值，即找到一个合适的函数，例如，interp1d类，当得到插值函数后便可用这个插值函数计算其他xj对应的的yj值了，这也就是插值的意义所在。

(1)一维插值interp1d

    import numpy as np
    from scipy import interpolate as intp
    import matplotlib.pyplot as plt
    
    # interp1d类可以根据输入的点，创建拟合函数。
    # 首先创建一些点，作为输入：
    x = np.linspace(0, 4, 12)
    y = np.cos(x**2/3 + 4)
    print (x)
    print (y)
    plt.plot(x, y, "o")
    plt.show()
    
    # 创建了两个函数f1和f2。通过这些函数，输入x可以计算y。
    # kind表示插值使用的技术类型，例如：’Linear’, ‘Nearest’, ‘Zero’, ‘Slinear’, ‘Quadratic’, ‘Cubic’等等。
    f1 = intp.interp1d(x, y, kind = 'linear')
    f2 = intp.interp1d(x, y, kind = 'cubic')
    xnew = np.linspace(0, 4, 30)
    plt.plot(x, y, 'o', xnew, f1(xnew), '-', xnew, f2(xnew), '--')
    plt.legend(['data', 'linear', 'cubic','nearest'], loc = 'best')
    plt.show()

(2)单变量样条插值

    # 可以通过interpolate模块中UnivariateSpline类对含有噪声的数据进行插值运算。
    # 使用UnivariateSpline类，输入一组数据点，通过绘制一条平滑曲线来去除噪声。绘制曲线时可以设置平滑参数s，如果参数s=0，将对所有点(包括噪声)进行插值运算，也就是说s=0时不去除噪声。
    # 样条插值中，点是针对使用多项式分段定义的函数拟合的！！！
    x = np.linspace(-3, 3, 50)
    y = np.exp(-x**2) + 0.1 * np.random.randn(50) # 通过random方法添加噪声数据
    plt.plot(x, y, 'ro', ms=5) # 红色点
    
    # 平滑参数使用默认值
    spl = UnivariateSpline(x, y)
    xs = np.linspace(-3, 3, 1000)
    plt.plot(xs, spl(xs), 'b', lw=3) # 蓝色曲线
    
    # 设置平滑参数
    spl.set_smoothing_factor(0.5)
    plt.plot(xs, spl(xs), 'g', lw=3) # 绿色曲线
    
    # 设置平滑参数为0
    spl.set_smoothing_factor(0)
    plt.plot(xs, spl(xs), 'yellow', lw=3) # 黄色曲线
    plt.show()

9.io，输入输出模块

scipy.io(输入和输出)包用于读写各种格式的文件。scipy.io支持的格式很多，如Matlab等。

    from scipy import io
    import numpy as np
    '''
    NumPy提供了 Python 可读格式的数据保存方法。
    SciPy 提供了与 Matlab 的交互的方法。
    SciPy 的 scipy.io 模块提供了很多函数来处理 Matlab 的数组。
    '''
    
    # savemat() 方法可以导出 Matlab 格式的数据。
    # 该方法参数有：
    # filename - 保存数据的文件名。
    # mdict - 包含数据的字典。
    # do_compression - 布尔值，指定结果数据是否压缩。默认为 False。
    # 将数组作为变量 "vec" 导出到 mat 文件：
    arr = np.arange(10)
    io.savemat('arr.mat', {"vec": arr})
    
    # 导入 Matlab 格式数据
    # loadmat() 方法可以导入 Matlab 格式数据。
    # 该方法参数：
    # filename - 保存数据的文件名。
    # 返回一个结构化数组，其键是变量名，对应的值是变量值。
    # 以下实例从 mat 文件中导入数组：
    arr = np.array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9,])
    # 导出
    io.savemat('arr.mat', {"vec": arr})
    # 导入
    mydata = io.loadmat('arr.mat')
    print(mydata)
    print(mydata['vec'])
    # 从结果可以看出数组最初是一维的，但在提取时它增加了一个维度，变成了二维数组。
    # 解决这个问题可以传递一个额外的参数 squeeze_me=True：
    mydata = io.loadmat('arr.mat', squeeze_me=True)
    print(mydata['vec'])

10.linalg，线性代数模块

SciPy线性代数包是使用优化的ATLAS LAPACK和BLAS库构建的，具有高效的线性代数运算能力。
线性代数包里的函数，操作对象都是二维数组。
SciPy.linalg与NumPy.linalg：与NumPy.linalg相比，scipy.linalg除了包含numpy.linalg中的所有函数，还具有numpy.linalg中没有的高级功能。

(1)线性方程组求解

scipy.linalg.solve函数可用于解线性方程。函数接受两个输入，数组a和数组b，数组a表示系数，数组b表示等号右侧值，求出的解将会放在一个数组里返回。

    from scipy import linalg
    import numpy as np
    
    # 线性方程组求解
    '''
    例如联立方程组：
    x+3y+5z=10
    2x+5y+z=8
    2x+3y+8z=3
    '''
    # 声明numpy数组
    a = np.array([[1, 3, 5], [2, 5, 1], [2, 3, 8]])
    b = np.array([10, 8, 3])
    
    # 求解
    x = linalg.solve(a, b)
    
    # 输出解值
    print(x)

(2)计算行列式

矩阵A的行列式表示为|A|，行列式计算是线性代数中的常见运算。
SciPy中，可以使用det()函数计算行列式，它接受一个矩阵作为输入，返回一个标量值，即该矩阵的行列式值。
矩阵行列式百度百科：https://baike.baidu.com/item/%E7%9F%A9%E9%98%B5%E8%A1%8C%E5%88%97%E5%BC%8F/18882017?fr=aladdin

    # 计算行列式
    # 声明numpy数组
    A = np.array([[3,4],[7,8]])
    # 计算行列式
    x = linalg.det(A)
    # 输出结果
    print(x)

(3)求特征值与特征向量

求取矩阵的特征值、特征向量，也是线性代数中的常见计算。通常可以根据下面的关系，求取矩阵(A)的特征值(λ)、特征向量(v)：Av=λv
scipy.linalg.eig 函数可用于计算特征值与特征向量，函数返回特征值和特征向量。
设A是n阶方阵，如果数λ和n维非零列向量x使关系式Ax=λx成立，那么这样的数λ称为矩阵A特征值，非零向量x称为A的对应于特征值λ的特征向量。
式Ax=λx也可写成( A-λE)X=0。这是n个未知数n个方程的齐次线性方程组，它有非零解的充分必要条件是系数行列式| A-λE|=0。

    # 计算特征值与特征向量
    # 声明numpy数组
    A = np.array([[3,4],[7,8]])
    # 求解
    l, v = linalg.eig(A)
    # 打印特征值
    print('特征值')
    print(l)
    # 打印特征向量
    print('特征向量')
    print(v)
    print('\n')

(4)SVD奇异值分解

奇异值分解(SVD)是现在比较常见的算法之一，也是数据挖掘工程师、算法工程师必备的技能之一。 假设A是一个M×N的矩阵，那么通过矩阵分解将会得到U，Σ，V^T三个矩阵。
其中U是一个M×M的方阵，被称为左奇异向量，方阵里面的向量是正交的；
Σ是一个M×N的对角矩阵，除了对角线的元素其他都是0，对角线上的值称为奇异值；
V^T(V的转置)是一个N×N的矩阵，被称为右奇异向量，方阵里面的向量也都是正交的。

    # SVD奇异值分解
    # 声明numpy数组
    a = np.random.randn(3, 2) + 1.j*np.random.randn(3, 2)
    # 输出原矩阵
    print('原矩阵')
    print(a)
    # 求解
    U, s, Vh = linalg.svd(a)
    # 输出结果
    print('奇异值分解')
    print(U, "#U")
    print(Vh, "#Vh")
    print(s, "#s")

11.ndimage，图像处理模块

图像处理和分析通常被看作是对二维值数组的操作。然而，在一些领域中，必须对高维数的图像进行处理分析，例如，医学成像和生物成像。由于对多维特性的良好支持，numpy非常适合这种类型的应用程序。
scipy.ndimage包提供了许多通用的图像处理和分析功能，这些功能支持操作任意维度的数组。
scipy.ndimage中提供了图像矩阵变换、图像滤波、图像卷积等功能。

(1)旋转图片

旋转图片，可以使用ndimage.rotate函数。

    from scipy import ndimage
    import matplotlib.image as mpimg
    import matplotlib.pyplot as plt
    
    # 加载图片
    face = mpimg.imread("./data/face.png")
    # 显示图片
    plt.imshow(face)
    plt.show()
    # 旋转图片
    rotate_face = ndimage.rotate(face, 45)
    plt.imshow(rotate_face)
    plt.show()

(2)图像滤波

图像滤波是一种修改/增强图像的技术。例如，可以图像滤波突出图像的某些特性，弱化或删除图像的另一些特性。滤波有很多种，例如：平滑、锐化、边缘增强等等。

    # 对图像进行高斯滤波。高斯滤波是一种模糊滤波，广泛用于滤除图像噪声。
    # 处理图片，sigma=3表示模糊程度为3，我们可以通过调整sigma值，来比较图像质量的变化。
    face1 = ndimage.gaussian_filter(face, sigma=3)
    # 显示图片
    plt.imshow(face1)
    plt.show()

(3)边缘检测

边缘检测是一种寻找图像中物体边界的图像处理技术。它的原理是通过检测图像中的亮度突变，来识别物体边缘。边缘检测在图像处理、计算机视觉、机器视觉等领域中广泛应用。

    # 图像看起来像一个正方形的色块，我们将检测这些彩色块的边缘。这里使用ndimage的Sobel函数来检测图像边缘，该函数会对图像数组的每个轴分开操作，产生两个矩阵，然后我们使用NumPy中的Hypot函数将这两个矩阵合并为一个矩阵，得到最后结果。
    im = np.zeros((256, 256))
    im[64:-64, 64:-64] = 1
    im[90:-90,90:-90] = 2
    im = ndimage.gaussian_filter(im, 8)
    plt.imshow(im)
    plt.show()
    
    sx = ndimage.sobel(im, axis = 0, mode = 'constant')
    sy = ndimage.sobel(im, axis = 1, mode = 'constant')
    sob = np.hypot(sx, sy)
    plt.imshow(sob)
    plt.show()

12.optimize，最优化模块

最优化是指在某些约束条件下，求解目标函数最优解的过程。机器学习、人工智能中的绝大部分问题都会涉及到求解优化问题。
SciPy的optimize模块提供了许多常用的数值优化算法，一些经典的优化算法包括线性回归、函数极值和根的求解以及确定两函数交点的坐标等。

(1)函数极值求解

    import numpy as np
    from scipy import optimize
    import matplotlib.pyplot as plt
    
    '''
    fmin_bfgs方法
    函数f(x)是一个抛物线，求它的极小值：
    f(x) = x^2 + 2x + 9
    计算该函数最小值的有效方法之一是使用带起点的BFGS算法。该算法从参数给定的起始点计算函数的梯度下降，并输出梯度为零、二阶导数为正的极小值。BFGS算法是由Broyden，Fletcher，Goldfarb，Shanno四个人分别提出的，故称为BFGS校正。
    使用BFGS函数，找出抛物线函数的最小值
    fmin_bfgs有个问题，当函数有局部最小值，该算法会因起始点不同，找到这些局部最小而不是全局最小！！！
    '''
    # 定义函数
    def f(x):
        return x**2 + 2*x + 9
    
    # x取值：-10到10之间，间隔0.1
    x = np.arange(-10, 10, 0.1)
    # 画出函数曲线
    plt.plot(x, f(x))
    # 第一个参数是函数名，第二个参数是梯度下降的起点。返回值是函数最小值的x值(ndarray数组)
    xopt = optimize.fmin_bfgs(f, 0)
    xmin = xopt[0] # x值
    ymin = f(xmin) # y值，即函数最小值
    print('xmin: ', xmin)
    print('ymin: ', ymin)
    # 画出最小值的点, s=20设置点的大小，c='r'设置点的颜色
    plt.scatter(xmin, ymin, s=20, c='r')
    plt.show()
    
    
    '''
    basinhopping方法
    该方法把局部优化方法与起始点随机抽样相结合，求出全局最小值，代价是耗费更多计算资源。
    调用语法：
    optimize.basinhopping(func, x0)
    func 目标函数
    x0 初始值
    似乎找到的并不是全局最小值！！！？？？.
    '''
    # 定义函数
    def g(x):
        return x**2 + 20*np.sin(x)
    plt.plot(x, g(x))
    # 求取最小值
    ret = optimize.basinhopping(g, 7, niter=100)
    print(ret)
    plt.show()
    print('\n')
    
    '''
    fminbound方法
    要求取一定范围之内的函数最小值，可使用fminbound方法。
    调用语法：
    optimize.fminbound(func, x1, x2)
    func 目标函数
    x1, x2 范围边界
    '''
    # 求取-10到-5之间的函数最小值。full_output=True表示返回详细信息。
    # 函数最小值是：35.82273589215206，对应的x值：-7.068891380019064。
    ret = optimize.fminbound(g, -10, -5, full_output=True)
    print(ret)

(2)函数求解

    '''
    解单个方程，对于一个函数f(x)，当f(x) = 0，求取x的值，即为函数求解。这种情况，可以使用fsolve()函数。
    调用语法
    optimize.fsolve(func， x0)
    func 目标函数
    x0 初始值
    '''
    def g(x):
        return x**2 + 20*np.sin(x)
    
    # 函数求解
    ret = optimize.fsolve(g, 2)
    print(ret)
    print('\n')
    
    
    '''
    解方程组
    如果要对如下方程组求解：
    f1(u1,u2,u3) = 0 \\ f2(u1,u2,u3) = 0 \\ f3(u1,u2,u3) = 0
    func可以定义为：
    def func(x):
    u1,u2,u3 = x
    return [f1(u1,u2,u3), f2(u1,u2,u3), f3(u1,u2,u3)]
    
    例如解方程组：
    4y + 9 = 0 \\ 3x^2 – sin(yz) = 0 \\ yz – 2.5 = 0
    '''
    
    def f(x):
        x0 = float(x[0])
        x1 = float(x[1])
        x2 = float(x[2])
        return [
            4*x1 + 9,
            3*x0*x0 - sin(x1*x2),
            x1*x2 - 2.5
        ]
    
    result = optimize.fsolve(f, [1,1,1])
    print (result)
    print('\n')

(3)拟合

    '''
    假设有一批数据样本，要创建这些样本数据的拟合曲线/函数，可以使用Scipy.optimize模块的curve_fit()函数。
    调用形式
    optimize.curve_fit(func, x1, y1)
    func 目标函数
    x1, y1 样本数据
    我们将使用下面的函数来演示曲线拟合：
    f(x) = 50cos(x) + 2
    '''
    
    # 函数模型用于生成数据
    def g(x, a, b):
        return a*np.cos(x) + b
    # 产生含噪声的样本数据
    x_data = np.linspace(-5, 5, 100) # 采样点
    y_data = g(x_data, 50, 2) + 5*np.random.randn(x_data.size) # 加入随机数作为噪声
    # 使用curve_fit()函数来估计a和b的值
    variables, variables_covariance = optimize.curve_fit(g, x_data, y_data)
    # 输出结果
    print('\n求出的系数a, b: ')
    print(variables)
    # variables是给定模型的最优参数，variables_covariance可用于检查拟合情况，其对角线元素值表示每个参数的方差。可以看到我们正确求出了系数值。
    print('\nvariables_covariance: ')
    print(variables_covariance)
    print('\n')
    y = g(x_data, variables[0], variables[1])
    # 绘制采样点
    plt.plot(x_data, y_data, 'o', color="green", label = "Samples")
    # 绘制拟合曲线
    plt.plot(x_data, y, color="red", label = "Fit")
    plt.legend(loc = "best")
    plt.show()
    
    
    '''
    leastsq()函数
    最小二乘法是非常经典的数值优化算法，通过最小化误差的平方和来寻找最符合数据的曲线。
    optimize模块提供了实现最小二乘拟合算法的函数leastsq()，leastsq是least square的简写，即最小二乘法。
    调用形式：
    optimize.leastsq(func, x0, args=())
    func 计算误差的函数
    x0 是计算的初始参数值
    args 是指定func的其他参数
    '''
    # 样本数据
    X = np.array([160,165,158,172,159,176,160,162,171])
    Y = np.array([58,63,57,65,62,66,58,59,62])
    
    # 偏差函数, 计算以p为参数的直线和原始数据之间的误差
    def residuals(p):
        k, b = p
        # 注意这个式子用的是数据X、Y
        return Y-(k*X+b)
    
    # leastsq()使得residuals()的输出数组的平方和最小，参数的初始值为[1, 0]
    ret = optimize.leastsq(residuals, [1, 0])
    k, b = ret[0]
    print("k = ", k, "b = ", b)
    # 画样本点
    # 指定图像比例： 8：6
    plt.figure(figsize=(8, 6))
    plt.scatter(X, Y, color="green", label="Samples", linewidth=2)
    # 画拟合直线
    # #在150-190直接画100个连续点
    x = np.linspace(150, 190, 100)
    # 函数式
    y = k*x + b
    plt.plot(x,y,color="red", label="Fit",linewidth=2)
    # 绘制图例
    plt.legend()
    plt.show()

13.signal，信号处理模块

(1)重新采样

    import numpy as np
    from scipy import signal
    import matplotlib.pyplot as plt
    
    # 重新采样
    # scipy.signal.resample()函数使用FFT将信号重采样成n个点。
    t = np.linspace(0, 5, 100)
    x = np.sin(t)
    x_resampled = signal.resample(x, 25)
    plt.plot(t, x)
    plt.plot(t[::4], x_resampled, 'ko')
    plt.show()

(2)去除趋势

    # scipy.signal.detrend()函数从信号中去除线性趋势。
    t = np.linspace(0, 5, 100)
    x = t + np.random.normal(size=100)
    x_detrended = signal.detrend(x)
    plt.plot(t, x)
    plt.plot(t, x_detrended)
    plt.show()

(3)滤波

对于非线性滤波，scipy.signal模块中提供了中值滤波scipy.signal.medfilt(), 维纳滤波scipy.signal.wiener()。滤波的使用，可参考图像处理章节，不再赘述。

(4)频谱分析

    scipy.signal.spectrogram() 计算连续时间窗上的频谱图
    scipy.signal.welch() 计算功率谱密度(PSD)

14.stats，统计模块

scipy.stats模块包含了统计工具以及概率分析工具。

(1)分布：直方图和概率密度函数

    import numpy as np
    import matplotlib.pyplot as plt
    from scipy import stats
    
    # 给定随机过程的观测值，其直方图是随机过程的概率密度函数PDF的估计量。
    samples = np.random.normal(size=1000)
    bins = np.arange(-4, 5)
    histogram = np.histogram(samples, bins=bins, normed=True)[0]
    bins = 0.5*(bins[1:] + bins[:-1])
    pdf = stats.norm.pdf(bins)  # norm是一个分布对象
    plt.plot(bins, histogram)
    plt.plot(bins, pdf)
    plt.show()
    
    # 如果我们知道随机过程属于一个给定的随机过程家族，比如正态过程，我们就可以对观测值进行最大似然拟合来估计潜在分布的参数。
    # 这里我们将一个正态过程与观察到的数据进行拟合。
    # 分布对象：scipy.stats.norm是一个分布对象: scipy.stats中的每个分布都表示为一个对象。例如：正态分布对象，还有PDF, CDF等等。
    loc, std = stats.norm.fit(samples)
    # 正态分布的平均值与标准差
    print(loc, std)

(2)平均值、中位数和百分位数

    # 均值是样本的平均值
    np.mean(samples)
    # 中位数是样本的中间值
    np.median(samples)
    # 中位数也是百分位数50，因为50%的观察值低于它
    stats.scoreatpercentile(samples, 50)
    # 同样，我们可以计算百分位数90:
    stats.scoreatpercentile(samples, 90)

(3)统计检验

    '''
    统计检验是一种决策指标。例如，如果我们有两组观测值，假设是高斯过程产生的，我们可以用T检验来判断两组观测值的均值是否存在显著差异:
    产生的输出包括:
    T统计值/statistic: 是一个数字，其符号与两个随机过程的差值成正比，其大小与该差值的显著性有关。
    p值/pvalue: 两个过程相同的概率。如果它接近1，这两个过程几乎肯定是相同的。越接近于零，这些过程就越有可能有不同均值。
    '''
    a = np.random.normal(0, 1, size=100)
    b = np.random.normal(1, 1, size=10)
    ret = stats.ttest_ind(a, b)
    print(ret)

N.参考

(1)[菜鸟教程](https://www.runoob.com/scipy/scipy-tutorial.html)

(2)[奇客谷教程](https://www.qikegu.com/docs/3471)

(3)[易百教程](https://www.yiibai.com/scipy/)

(4)[SciPy官方手册英文](https://scipy.github.io/devdocs/tutorial/index.html)


# Sklearn

1.sklearn是什么

机器学习是计算机科学的一个分支，研究的是无需人类干预，能够自己学习的算法。 与TensorFlow不同，Scikit-learn(sklearn)的定位是通用机器学习库，而TensorFlow(tf)的定位主要是深度学习库。
SciKit-Learn是SciKit库的一部分，SciKit意思是SciPy Toolkits，名字来源于SciPy库，SciKit基于SciPy库构建，除了SciKit-Learn，还包含其他很多模块，可以打开这个网址查看。SciKit-Learn库是专注于机器学习和数据挖掘的模块。

2.加载数据

数据类型可以是NumPy数组、SciPy稀疏矩阵，或者其他可转换为数组的类型，如panda DataFrame等。

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

3.预处理

常见方法

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

例子

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

4.模型

创建模型

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

模型拟合

有监督学习：

    lr.fit(X, y)
    svc.fit(X_train, y_train)
    knn.fit(X_train, y_train)

无监督学习：

    k_means.fit(X_train)
    pca_model = pca.fit_transform(X_train)

模型预测

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

模型调优

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

例子

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

N.参考

(1)[奇客谷sklearn教程](https://www.qikegu.com/docs/4065)

(2)[奇客谷sklearn速查](https://www.qikegu.com/docs/4093)

(3)[sklearn官网user guide](https://scikit-learn.org/stable/user_guide.html)

(4)[sklearn中文文档](https://sklearn.apachecn.org/#/)

(5)[openml数据集网站](https://www.openml.org/)

(6)[UCI机器学习库数据集](http://archive.ics.uci.edu/ml/datasets)

(7)[Kaggle](http://www.kaggle.com/)
