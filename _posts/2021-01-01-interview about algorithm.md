---
layout: post
title: "面试题 -- 算法与数据结构篇"
description: 面试题 -- 算法与数据结构篇
modified: 2021-01-01
category: Interview
tags: [Interview]
---

# 数据结构

1.红黑树

特点：
(1)每个节点非红即黑。
(2)根节点总是黑色的。
(3)如果节点是红色的，则它的子节点必须是黑色的（反之不一定），红黑树不会出现相邻的红色节点。
(4)每个叶子节点都是黑色的空节点（NULL节点）。
(5)从根节点到叶节点或空子节点的每条路径，必须包含相同数目的黑色节点（即相同的黑色高度）。
(6)是平衡二叉树。

自平衡基本操作：
变色：在不违反上述红黑树规则特点情况下，将红黑树某个node节点颜色由红变黑，或者由黑变红。
左旋：逆时针旋转两个节点，让一个节点被其右子节点取代，而该节点成为右子节点的左子节点。
右旋：顺时针旋转两个节点，让一个节点被其左子节点取代，而该节点成为左子节点的右子节点。

2.AVL树

AVL树是最先发明的自平衡二叉查找树。在AVL树中任何节点的两个子树的高度最大差别为1，所以它也被称为高度平衡树。增加和删除可能需要通过一次或多次树旋转来重新平衡这个树。
AVL树得名于它的发明者G. M. Adelson-Velsky和E. M. Landis，名字已拼接AVL树的大名就出来了。
当子树的高度超过1时他会通过自旋的方式重新平衡树，所以这样我们查询数据的时间复杂度就稳定了。

3.数组

数组是固定大小的结构，可以容纳相同数据类型的项目。它可以是整数数组，浮点数数组，字符串数组或什至是数组数组（例如二维数组）。数组已建立索引，这意味着可以进行随机访问。
数组运算：

    遍历：遍历所有元素并进行打印。
    插入：将一个或多个元素插入数组。
    删除：从数组中删除元素
    搜索：在数组中搜索元素。您可以按元素的值或索引搜索元素
    更新：在给定索引处更新现有元素的值

数组的应用

    用作构建其他数据结构的基础，例如数组列表，堆，哈希表，向量和矩阵。
    用于不同的排序算法，例如插入排序，快速排序，冒泡排序和合并排序。

4.链表

链表是一种顺序结构，由相互链接的线性顺序项目序列组成。因此，您必须顺序访问数据，并且无法进行随机访问。链接列表提供了动态集的简单灵活的表示形式。
链表中的元素称为节点。每个节点都包含一个密钥和一个指向其后继节点（称为next）的指针。名为head的属性指向链接列表的第一个元素。 链表的最后一个元素称为尾。
以下是可用的各种类型的链表。

    单链列表—只能沿正向遍历项目。
    双链表-可以在前进和后退方向上遍历项目。节点由一个称为上一个的附加指针组成，指向上一个节点。
    循环链接列表—链接列表，其中头的上一个指针指向尾部，尾号的下一个指针指向头。

链表操作

    搜索：通过简单的线性搜索在给定的链表中找到键为k的第一个元素，并返回指向该元素的指针
    插入：在链接列表中插入一个密钥。插入可以通过3种不同的方式完成；在列表的开头插入，在列表的末尾插入，然后在列表的中间插入。
    删除：从给定的链表中删除元素x。您不能单步删除节点。删除可以通过3种不同方式完成；从列表的开头删除，从列表的末尾删除，然后从列表的中间删除。

链表的应用

    用于编译器设计中的符号表管理。
    用于在使用Alt Tab（使用循环链表实现）的程序之间进行切换。

5.堆栈

堆栈是一种LIFO（后进先出-最后放置的元素可以首先访问）结构，该结构通常在许多编程语言中都可以找到。该结构被称为"堆栈"，因为它类似于真实世界的堆栈-板的堆栈。
堆栈操作：

    Push 推送：在堆栈顶部插入一个元素。
    Pop 弹出：删除最上面的元素并返回。

此外，为堆栈提供了以下附加功能，以检查其状态。

    Peek 窥视：返回堆栈的顶部元素而不删除它。
    isEmpty：检查堆栈是否为空。
    isFull：检查堆栈是否已满。

堆栈的应用

    用于表达式评估（例如：用于解析和评估数学表达式的调车场算法）。
    用于在递归编程中实现函数调用。

6.队列

队列是一种FIFO（先进先出-首先放置的元素可以首先访问）结构，该结构通常在许多编程语言中都可以找到。该结构被称为"队列"，因为它类似于现实世界中的队列-人们在队列中等待。
队列操作

    进队：将元素插入队列的末尾。
    出队：从队列的开头删除元素。

队列的应用

    用于管理多线程中的线程。
    用于实施排队系统（例如：优先级队列）。

7.哈希表

哈希表是一种数据结构，用于存储具有与每个键相关联的键的值。此外，如果我们知道与值关联的键，则它有效地支持查找。因此，无论数据大小如何，插入和搜索都非常有效。
当存储在表中时，直接寻址使用值和键之间的一对一映射。但是，当存在大量键值对时，此方法存在问题。该表将具有很多记录，并且非常庞大，考虑到典型计算机上的可用内存，该表可能不切实际甚至无法存储。为避免此问题，我们使用哈希表。
哈希函数:名为哈希函数（h）的特殊函数用于克服直接寻址中的上述问题。
在直接访问中，带有密钥k的值存储在插槽k中。使用哈希函数，我们可以计算出每个值都指向的表（插槽）的索引。使用给定键的哈希函数计算的值称为哈希值，它表示该值映射到的表的索引。

    h：哈希函数
    k：应确定其哈希值的键
    m：哈希表的大小（可用插槽数）。一个不接近2的精确乘方的素数是m的一个不错的选择。

哈希表的应用

    用于实现数据库索引。
    用于实现关联数组。
    用于实现"设置"数据结构。

8.树

树是一种层次结构，其中数据按层次进行组织并链接在一起。此结构与链接列表不同，而在链接列表中，项目以线性顺序链接。
在过去的几十年中，已经开发出各种类型的树木，以适合某些应用并满足某些限制。一些示例是二叉搜索树，B树，红黑树，展开树，AVL树和n元树。
二叉搜索树:顾名思义，二进制搜索树（BST）是一种二进制树，其中数据以分层结构进行组织。此数据结构按排序顺序存储值。
二叉搜索树中的每个节点都包含以下属性。
    
    key：存储在节点中的值。
    left：指向左孩子的指针。
    right：指向正确孩子的指针。
    p：指向父节点的指针。

二叉搜索树具有独特的属性，可将其与其他树区分开。此属性称为binary-search-tree属性。

    令x为二叉搜索树中的一个节点。 
    如果y是x左子树中的一个节点，则y.key≤x.key 
    如果y是x的右子树中的节点，则y.key≥x.key

树的应用

    二叉树：用于实现表达式解析器和表达式求解器。
    二进制搜索树：用于许多不断输入和输出数据的搜索应用程序中。
    堆：由JVM（Java虚拟机）用来存储Java对象。
    Trap：用于无线网络。

9.堆

堆是二叉树的一种特殊情况，其中将父节点与其子节点的值进行比较，并对其进行相应排列。
让我们看看如何表示堆。堆可以使用树和数组表示。
堆可以有2种类型。

    最小堆-父项的密钥小于或等于子项的密钥。这称为min-heap属性。根将包含堆的最小值。
    最大堆数-父项的密钥大于或等于子项的密钥。这称为max-heap属性。根将包含堆的最大值。

堆的应用

    用于实现优先级队列，因为可以根据堆属性对优先级值进行排序。
    可以在O（log n）时间内使用堆来实现队列功能。
    用于查找给定数组中k个最小（或最大）的值。
    用于堆排序算法。

10.图

一个图由一组有限的顶点或节点以及一组连接这些顶点的边组成。
图的顺序是图中的顶点数。图的大小是图中的边数。
如果两个节点通过同一边彼此连接，则称它们为相邻节点。

有向图：如果图形G的所有边缘都具有指示什么是起始顶点和什么是终止顶点的方向，则称该图形为有向图。
我们说（u，v）从顶点u入射或离开顶点u，然后入射到或进入顶点v。
自环：从顶点到自身的边。

无向图： 如果图G的所有边缘均无方向，则称其为无向图。它可以在两个顶点之间以两种方式传播。
如果顶点未连接到图中的任何其他节点，则称该顶点为孤立的。

图的应用

    用于表示社交媒体网络。每个用户都是一个顶点，并且在用户连接时会创建一条边。
    用于表示搜索引擎的网页和链接。互联网上的网页通过超链接相互链接。每页是一个顶点，两页之间的超链接是一条边。用于Google中的页面排名。
    用于表示GPS中的位置和路线。位置是顶点，连接位置的路线是边。用于计算两个位置之间的最短路径。

# 算法——基础

1.时间复杂度
  
常数时间的操作：一个操作如果和数据量没有关系，每次都是固定时间内完成的操作，叫做常数操作。
时间复杂度为一个算法流程中，常数操作数量的指标。常用O来表示。具体来说，在常数操作数量的表达式中，只要高阶项，不要低阶项，也不要高阶项的系数，剩下的部分，如果记为f(N)，那么时间复杂度为O(f(N))。
算法的时间复杂度，用来度量算法的运行时间，记作: O(f(N))。它表示随着输入大小N的增大，算法执行需要的时间的增长速度可以用f(N)来描述。

# 算法——排序

1.直接插入排序

思路：把新的数据插入到已经排好的数据列中。对于每个新插入的数，从最后一个数开始向前循环，如果插入数小于当前数，就将当前数向后移动一位。

    public void insertSort(int[] a){  
        int length=a.length;//数组长度，将这个提取出来是为了提高速度。  
        int insertNum;//要插入的数  
        for(int i=1;i<length;i++){//插入的次数  
            insertNum=a[i];//要插入的数  
            int j=i-1;//已经排序好的末尾元素的序列  
            while(j>=0&&a[j]>insertNum){//序列从后到前循环，将大于insertNum的数向后移动一格  
                a[j+1]=a[j];//元素移动一格  
                j--;  
            }  
            a[j+1]=insertNum;//将需要插入的数放在要插入的位置。  
        }  
    }

2.希尔排序

思路：将数的个数设为n，取gap值k=n/2，将下标差值为k的数分为一组，构成有序序列。再取k=k/2 ，将下标差值为k的数分为一组，构成有序序列。重复第二步，直到k=1执行简单插入排序。

    public void sheelSort(int[] a){  
        int d  = a.length;  
        while (d!=0) {  
            d=d/2;  
            for (int x = 0; x < d; x++) {//分的组数  
                for (int i = x + d; i < a.length; i += d) {//组中的元素，从第二个数开始  
                    // 以下为直接插入排序
                    int j = i - d;//j为有序序列最后一位的位数  
                    int temp = a[i];//要插入的元素  
                    for (; j >= 0 && temp < a[j]; j -= d) {//从后往前遍历。  
                        a[j + d] = a[j];//向后移动d位  
                    }  
                    a[j + d] = temp;  
                }  
            }  
        }  
    }  

3.简单选择排序

思路：遍历整个序列，将最小的数放在最前面。遍历剩下的序列，将最小的数放在最前面。重复第二步，直到只剩下一个数。常用于取序列中最大最小的几个数时。

    public void selectSort(int[] a) {  
        int length = a.length;  
        for (int i = 0; i < length; i++) {//循环次数
            // 下面两个值每轮循环保存该轮最小值及位置
            int key = a[i];  
            int position=i;  
            for (int j = i + 1; j < length; j++) {//选出最小的值和位置  
                if (a[j] < key) {  
                    key = a[j];  
                    position = j;  
                }  
            }
            //交换位置
            a[position]=a[i];  
            a[i]=key;  
        }  
    }

4.堆排序

将序列构建成大顶堆(任意节点大于其子节点)。将根节点与最后一个节点交换，然后断开最后一个节点。重复第一、二步，直到所有节点断开。

    public  void heapSort(int[] a){  
        System.out.println("开始排序");  
        int arrayLength=a.length;  
        //循环建堆    
        for(int i=0;i<arrayLength-1;i++){  
            //建堆    
            buildMaxHeap(a,arrayLength-1-i);  
            //交换堆顶和最后一个元素    
            swap(a,0,arrayLength-1-i);  
            System.out.println(Arrays.toString(a));  
        }  
    }  
    private  void swap(int[] data, int i, int j) {  
        // TODO Auto-generated method stub    
        int tmp=data[i];  
        data[i]=data[j];  
        data[j]=tmp;  
    }  
    //对data数组从0到lastIndex建大顶堆    
    private void buildMaxHeap(int[] data, int lastIndex) {  
        // TODO Auto-generated method stub    
        //从lastIndex处节点（最后一个节点）的父节点开始    
        for(int i=(lastIndex-1)/2;i>=0;i--){  
            //k保存正在判断的节点    
            int k=i;  
            //如果当前k节点的子节点存在    
            while(k*2+1<=lastIndex){  
                //k节点的左子节点的索引    
                int biggerIndex=2*k+1;  
                //如果biggerIndex小于lastIndex，即biggerIndex+1代表的k节点的右子节点存在    
                if(biggerIndex<lastIndex){  
                    // 如果右子节点的值较大    
                    if(data[biggerIndex]<data[biggerIndex+1]){  
                        //biggerIndex总是记录较大子节点的索引    
                        biggerIndex++;  
                    }  
                }  
                //如果k节点的值小于其较大的子节点的值    
                if(data[k]<data[biggerIndex]){  
                    //交换他们    
                    swap(data,k,biggerIndex);  
                    //将biggerIndex赋予k，开始while循环的下一次循环，重新保证k节点的值大于其左右子节点的值    
                    k=biggerIndex;  
                }else{  
                    break;  
                }  
            }  
        }  
    } 

5.冒泡排序

思路：一般不用。将序列中所有元素两两比较，将最大的放在最后面。将剩余序列中所有元素两两比较，将最大的放在最后面。重复第二步，直到只剩下一个数。

    public void bubbleSort(int[] a){  
        int length=a.length;  
        int temp;  
        for(int i=0;i<a.length;i++){  
            for(int j=0;j<a.length-i-1;j++){  
                if(a[j]>a[j+1]){  
                    temp=a[j];  
                    a[j]=a[j+1];  
                    a[j+1]=temp;  
                }  
            }  
        }  
    }

6.快速排序

思路：要求时间最快时。选择第一个数为p，小于p的数放在左边，大于p的数放在右边。递归地将p左边和右边的数都按照第一步进行，直到不能递归。
算法分析：最佳情况T(n) = O(nlogn)，最差情况：T(n) = O(n^2) 平均情况：T(n) = O(nlogn)。

    public static void quickSort(int[] numbers, int start, int end) {     
        if (start < end) {     
            int base = numbers[start]; // 选定的基准值（第一个数值作为基准值）     
            int temp; // 记录临时中间值     
            int i = start, j = end;     
            do {     
                while ((numbers[i] < base) && (i < end))     
                    i++;     
                while ((numbers[j] > base) && (j > start))     
                    j--;     
                if (i <= j) {     
                    temp = numbers[i];  
                    numbers[i] = numbers[j];     
                    numbers[j] = temp;  
                    i++;     
                    j--;     
                }     
            } while (i <= j);
            if (start < j)     
                quickSort(numbers, start, j);     
            if (end > i)     
                quickSort(numbers, i, end);     
        }
    }

7.归并排序

思路：速度仅次于快排。分治法，将数组前后两段递归分别排序，再拼合为有序序列。

    public static void sort(int []arr){
      int []temp = new int[arr.length];
      //在排序前，先建好一个长度等于原数组长度的临时数组，
      //避免递归中频繁开辟空间
      sort(arr,0,arr.length-1,temp);
    }
    private static void sort(int[] arr,int left,int right,int []temp){
      if(left<right){
          int mid = (left+right)/2;
          sort(arr,left,mid,temp);
          //左边归并排序，使得左子序列有序
          sort(arr,mid+1,right,temp);
          //右边归并排序，使得右子序列有序
          merge(arr,left,mid,right,temp);
          //将两个有序子数组合并操作
      }
    }
    private static void merge(int[] arr,int left,int mid,int right,int[] temp){
      int i = left;//左序列指针
      int j = mid+1;//右序列指针
      int t = 0;//临时数组指针
      while (i<=mid && j<=right){
          if(arr[i]<=arr[j]){
              temp[t++] = arr[i++];
          }else {
              temp[t++] = arr[j++];
          }
      }
      while(i<=mid){//将左边剩余元素填充进temp中
          temp[t++] = arr[i++];
      }
      while(j<=right){//将右序列剩余元素填充进temp中
          temp[t++] = arr[j++];
      }
      t = 0;
      //将temp中的元素全部拷贝到原数组中
      while(left <= right){
          arr[left++] = temp[t++];
      }
    }

8.基数排序

思路：用于大量数，很长的数进行排序时。将所有的数的个位数取出，按照个位数进行排序，构成一个序列。将新构成的所有的数的十位数取出，按照十位数进行排序，构成一个序列。依次对各位数操作。

    public void sort(int[] array) {  
        //首先确定排序的趟数;       
        int max = array[0];  
        for (int i = 1; i < array.length; i++) {  
            if (array[i] > max) {  
                max = array[i];  
            }  
        }  
        int time = 0;  
        //判断位数;       
        while (max > 0) {  
            max /= 10;  
            time++;  
        }  
        //建立10个队列;       
        List<ArrayList> queue = new ArrayList<ArrayList>();  
        for (int i = 0; i < 10; i++) {  
            ArrayList<Integer> queue1 = new ArrayList<Integer>();  
            queue.add(queue1);  
        }  
        //进行time次分配和收集;       
        for (int i = 0; i < time; i++) {  
            //分配数组元素;       
            for (int j = 0; j < array.length; j++) {  
                //得到数字的第time+1位数;     
                int x = array[j] % (int) Math.pow(10, i + 1) / (int) Math.pow(10, i);  
                ArrayList<Integer> queue2 = queue.get(x);  
                queue2.add(array[j]);  
                queue.set(x, queue2);  
            }  
            int count = 0;//元素计数器;       
            //收集队列元素;       
            for (int k = 0; k < 10; k++) {  
                while (queue.get(k).size() > 0) {  
                    ArrayList<Integer> queue3 = queue.get(k);  
                    array[count] = queue3.get(0);  
                    queue3.remove(0);  
                    count++;  
                }  
            }  
        }  
    }
N.参考
 
(1)[【228期】面试高频：Java常用的八大排序算法一网打尽！](https://mp.weixin.qq.com/s/4_huQurXRYuR6Kq2pERqIQ)

# 算法——二分查找

1.有序数组中查找指定数字，找到就返回其位置，没找到就返回其应该插入的位置。

    public int[] searchRange(int[] nums, int target) {
        int len = nums.length;
        int left = 0,right = len-1;
        int[] ret = {-1,-1};
        if((len==0) || (target<nums[0]) || (target> nums[right])){
            return ret;
        }
        else if(len==1){
            if(target==nums[0]){
                ret[0] = 0;
                ret[1] = 0;
            }
            return ret;
        }
        else{
            while(left<right){
                int middle = (left+right)/2;
                if(nums[middle]<target){
                    left=middle+1;
                }
                else{
                    right=middle;
                }
            }
            if(nums[left]!=target){
                return ret;
            }
            else{
                ret[0] = left;
            }
            left=0;
            right=len-1;
            while(left<right){
                int middle = (left+right+1)/2;
                if(nums[middle]>target){
                    right=middle-1;
                }
                else{
                    left=middle;
                }
            }
            ret[1] = right;
            return ret;
        }
    }
    
2.无重复数字的有序数组以某位置为轴反转，在其中寻找给定的数字，找到返回位置，没找到返回-1

    public int search(int[] nums, int target) {
        int left = 0;
        int right = nums.length-1;
        int mid;
        while(left<right){
            mid = (left+right+1)/2;
            if(target == nums[mid]){
                return mid;
            }
            if(nums[mid]>nums[right]){
                if(target<nums[mid] && target>=nums[left]){
                    right = mid;
                }
                else{
                    left = mid;
                }
            }
            else if(nums[mid]<nums[right]){
                if(target<=nums[right] && target>nums[mid]){
                    left = mid;
                }
                else{
                    right = mid;
                }
            }
            else{
                right--;
            }
        }
        if(left==right && nums[left]==target){
            return left;
        }
        else{
            return -1;
        }
    }

# 算法——链表

1.整个链表翻转

    public ListNode reverseList(ListNode head) {
        if(head == null){
            return null;
        }
        ListNode prev = null;
        ListNode curr = head;
        while (curr != null){
            ListNode nextNode = curr.next;
            curr.next = prev;
            prev = curr;
            curr = nextNode;
        }
        return prev;
    }
    
2.反转链表中的第m至第n个节点

    public ListNode reverseBetween(ListNode head, int m, int n) {
        if(head == null){
            return null;
        }
        ListNode fakeHead = new ListNode(0);
        fakeHead.next = head;
        ListNode curr = fakeHead;
        for(int i = 0;i<m-1;i++){
            curr = curr.next;
        }
        ListNode beforeHead = curr;
        curr = fakeHead;
        for(int i = 0;i<n+1;i++){
            curr = curr.next;
        }
        ListNode afterTail = curr;
        beforeHead.next = reverseAll(beforeHead.next,n - m + 1);
        curr = fakeHead;
        while (curr.next!= null){
            curr = curr.next;
        }
        curr.next = afterTail;
        return fakeHead.next;
    }

    public ListNode reverseAll(ListNode head,int num){
        if(head == null){
            return null;
        }
        ListNode prev = null;
        ListNode curr = head;
        for(int i = 0;i<num;i++){
            ListNode nextNode = curr.next;
            curr.next = prev;
            prev = curr;
            curr = nextNode;
        }
        return prev;
    }

3.求两个链表之间的交点

    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        if(headA == null || headB == null){
            return null;
        }
        int len1 = getListLen(headA);
        int len2 = getListLen(headB);
        ListNode pointerA = headA;
        ListNode pointerB = headB;
        //补足长度差别
        int gap = Math.abs(len1 - len2);
        for(int i=0;i<gap;i++){
            if(len1 > len2){
                pointerA = pointerA.next;
            }
            else{
                pointerB = pointerB.next;
            }
        }
        //不同向后移动，直到找到同一个
        while(pointerA != null && pointerB != null && pointerA != pointerB){
            pointerA = pointerA.next;
            pointerB = pointerB.next;
        }
        return pointerA;
    }

    public int getListLen(ListNode head){
        if(head == null){
            return 0;
        }
        int len1 = 0;
        int i = 0;
        ListNode pointerA = head;
        while(pointerA != null){
            pointerA = pointerA.next;
            i++;
        }
        return i;
    }
}

4.判断链表是否有环；如果有环，给出环所在的起始节点及环的长度。

快慢指针位置重遇后，将一个移动到head，再移动相同步就能找到开头位置。

    public ListNode detectCycle(ListNode head) {
        if (head == null || head.next == null){
            return null;
        }
        ListNode fast = head;
        ListNode slow = head;
        while (slow.next != null && fast.next != null && fast.next.next != null){
            slow = slow.next;
            fast = fast.next.next;
            if (slow == fast){
                slow = head;
                while (slow != fast){
                    fast = fast.next;
                    slow = slow.next;
                }
                return fast;
            }
        }
        return null;
    }

5.将链表换分为以某个值x为界限的前后两部分

链表划分依据：链表前面的部门小于x，后面部分大于等于x，且原有节点的相对顺序不变。如：将1->3-7->2->5->3->6-9-2划分为以5为界限的前后两部分：1->3->2->3->2 ->7->5->6->9

    public ListNode partition(ListNode head, int x) {
        ListNode fakeHeadLeft = new ListNode(0);
        ListNode fakeHeadRight = new ListNode(0);
        ListNode left = fakeHeadLeft;
        ListNode right = fakeHeadRight;
        partitionEach(left,right,head,x);
        left = fakeHeadLeft;
        while (left.next!= null){
            left = left.next;
        }
        left.next = fakeHeadRight.next;
        return fakeHeadLeft.next;
    }

    public void partitionEach(ListNode left,ListNode right,ListNode remain,int x){
        if(remain != null){
            ListNode nextNode = remain.next;
            if(remain.val < x){
                left.next = remain;
                left = left.next;
            }
            else{
                right.next = remain;
                right = right.next;
            }
            partitionEach(left,right,nextNode,x);
        }
        else{
            left.next = null;
            right.next = null;
        }
    }

6.合并两个有序链表

    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        if(l1 == null){
            return l2;
        }
        if(l2 == null){
            return l1;
        }
        if(l1.val < l2.val){
            ListNode nextNode = l1.next;
            l1.next = mergeTwoLists(nextNode,l2);
            return l1;
        }
        else {
            ListNode nextNode = l2.next;
            l2.next = mergeTwoLists(l1,nextNode);
            return l2;
        }
    }
    
# 算法——二叉树

1.先序遍历

    // 递归
    public List<Integer> preorderTraversal(TreeNode root) {
        List<Integer> ret = new LinkedList<>();
        if (root == null){
            return ret;
        }
        ret.add(root.val);
        List<Integer> left = preorderTraversal(root.left);
        List<Integer> right = preorderTraversal(root.right);
        ret.addAll(left);
        ret.addAll(right);
        return ret;
    }
    
    // 非递归
    public List<Integer> preorderTraversal(TreeNode root) {
        List<Integer> ret = new LinkedList<>();
        if (root == null){
            return ret;
        }
        Deque<TreeNode> stack = new LinkedList<>();
        stack.push(root);
        while (stack.size() > 0){
            TreeNode node = stack.pop();
            ret.add(node.val);
            if (node.right != null){
                stack.push(node.right);
            }
            if (node.left != null){
                stack.push(node.left);
            }
        }
        return ret;
    }

2.中序遍历

    // 递归
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> list = new LinkedList<>();
        if(root == null){
            return list;
        }
        List<Integer> left = inorderTraversal(root.left);
        ret.addAll(left);
        ret.add(root.val);
        List<Integer> right = inorderTraversal(root.right);
        ret.addAll(right);
        return list;
    }
    
    // 非递归
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> list = new LinkedList<>();
        if(root == null){
            return list;
        }
        Deque<TreeNode> stack = new LinkedList<>();
        TreeNode curr = root;
        while (curr != null || stack.size() != 0){
            while (curr != null){
                stack.push(curr);
                curr = curr.left;
            }
            TreeNode tempNode = stack.pop();
            list.add(tempNode.val);
            curr = tempNode.right;
        }
        return list;
    }

3.后序遍历

    public List<Integer> postorderTraversal(TreeNode root) {
        Deque<TreeNode> stack = new LinkedList<>();
        List<Integer> result = new LinkedList<>();
        if (root == null){
            return result;
        }
        stack.push(root);
        while (stack.size() > 0){
            TreeNode node = stack.pop();
            result.add(node.val);
            if (node.left != null){
                stack.push(node.left);
            }
            if (node.right != null){
                stack.push(node.right);
            }
        }
        Collections.reverse(result);
        return result;
    }
    
4.层级遍历

    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> lists = new LinkedList<>();
        if(root == null){
            return lists;
        }
        List<TreeNode> prev = new LinkedList<>();
        prev.add(root);
        while (prev.size() > 0){
            List<TreeNode> next = new LinkedList<>();
            List<Integer> addList = new LinkedList<>();
            int size = prev.size();
            for(int i = 0;i<size;i++){
                TreeNode node = prev.get(i);
                addList.add(node.val);
                if(node.left != null){
                    next.add(node.left);
                }
                if(node.right != null){
                    next.add(node.right);
                }
            }
            lists.add(addList);
            prev = next;
        }
        return lists;
    }

# 加密算法

1.AES，3DES两种对称加密算法

    package cn.zhanghao90.test;

    import org.apache.commons.codec.binary.Base64;
    import javax.crypto.Cipher;
    import javax.crypto.spec.SecretKeySpec;

    public class SymmetricUtil {
    	public static String encrypt3DES(String value, String key) throws Exception {
    		if (null == value || "".equals(value)) {
    			return "";
    		}

    		byte[] valueByte = value.getBytes();
    		byte[] sl = encrypt3DES(valueByte, BytesUtil.hexToBytes(key));
    		String result = Base64.encodeBase64String(sl);

    		return result;
    	}

    	public static byte[] encrypt3DES(byte[] input, byte[] key) throws Exception {
    		Cipher c = Cipher.getInstance("DESede/ECB/PKCS5Padding");
    		c.init(Cipher.ENCRYPT_MODE, new SecretKeySpec(key, "DESede"));
    		return c.doFinal(input);
    	}

    	public static String decrypt3DES(String value, String key) throws Exception {
    		if (null == value || "".equals(value)) {
    			return "";
    		}

    		byte[] valueByte = Base64.decodeBase64(value);
    		byte[] sl = decrypt3DES(valueByte, BytesUtil.hexToBytes(key));
    		String result = new String(sl);
    		return result;
    	}

    	public static byte[] decrypt3DES(byte[] input, byte[] key) throws Exception {
    		Cipher c = Cipher.getInstance("DESede/ECB/PKCS5Padding");
    		c.init(Cipher.DECRYPT_MODE, new SecretKeySpec(key, "DESede"));
    		return c.doFinal(input);
    	}

    	public static String encryptAES(String value, String key) throws Exception {
    		if (null == value || "".equals(value)) {
    			return "";
    		}

    		byte[] valueByte = value.getBytes();
    		byte[] sl = encryptAES(valueByte, BytesUtil.hexToBytes(key));
    		String result = Base64.encodeBase64String(sl);

    		return result;
    	}

    	public static byte[] encryptAES(byte[] input, byte[] key) throws Exception {
    		Cipher c = Cipher.getInstance("AES/ECB/PKCS5Padding");
    		c.init(Cipher.ENCRYPT_MODE, new SecretKeySpec(key, "AES"));
    		return c.doFinal(input);
    	}

    	public static String decryptAES(String value, String key) throws Exception {
    		if (null == value || "".equals(value)) {
    			return "";
    		}

    		byte[] valueByte = Base64.decodeBase64(value);
    		byte[] sl = decryptAES(valueByte, BytesUtil.hexToBytes(key));
    		String result = new String(sl);
    		return result;
    	}

    	public static byte[] decryptAES(byte[] input, byte[] key) throws Exception {
    		Cipher c = Cipher.getInstance("AES/ECB/PKCS5Padding");
    		c.init(Cipher.DECRYPT_MODE, new SecretKeySpec(key, "AES"));
    		return c.doFinal(input);
    	}
    	
    }

2.RSA非对称算法

    package cn.zhanghao90.test;

    import org.apache.commons.codec.binary.Base64;
    import org.bouncycastle.jce.provider.BouncyCastleProvider;
    import javax.crypto.Cipher;
    import java.io.ByteArrayOutputStream;
    import java.io.UnsupportedEncodingException;
    import java.security.Key;
    import java.security.KeyFactory;
    import java.security.KeyPair;
    import java.security.KeyPairGenerator;
    import java.security.PrivateKey;
    import java.security.PublicKey;
    import java.security.Security;
    import java.security.Signature;
    import java.security.interfaces.RSAPrivateKey;
    import java.security.interfaces.RSAPublicKey;
    import java.security.spec.PKCS8EncodedKeySpec;
    import java.security.spec.X509EncodedKeySpec;
    import java.util.HashMap;
    import java.util.Map;

    public class RSAUtils {

        /**
         * 加密算法RSA
         */
        public static final String RSA_ALGORITHM = "RSA";
        public static final String RSA_ECB_OAEP_PADDING = "RSA/ECB/OAEPPadding";
        public static final String BC_PROVIDER = "BC";

        /**
         * 签名算法
         */
        public static final String SIGNATURE_ALGORITHM = "MD5withRSA";

        /**
         * 获取公钥的key
         */
        public static final String PUBLIC_KEY = "RSAPublicKey";
        /**
         * 获取私钥的key
         */
        public static final String PRIVATE_KEY = "RSAPrivateKey";

        /**
         * 签名长度
         */
        private static final int KEY_SIZE = 2048;
        /**
         * RSA最大加密明文大小
         */
        private static final int MAX_ENCRYPT_BLOCK = 245;
        /**
         * RSA最大解密密文大小
         */
        private static final int MAX_DECRYPT_BLOCK = 256;

        static {
            Security.addProvider(new BouncyCastleProvider());
        }

        /**
         * <P>
         * 私钥解密2048
         * </p>
         *
         * @param encryptedData 已加密数据
         * @param privateKey    私钥(BASE64编码)
         * @return
         * @throws Exception
         */
        public static byte[] decryptByPrivateKey2048(byte[] encryptedData,
                                                     String privateKey) throws Exception {
            byte[] keyBytes = decode64(privateKey);
            PKCS8EncodedKeySpec pkcs8KeySpec = new PKCS8EncodedKeySpec(keyBytes);

            KeyFactory keyFactory = KeyFactory.getInstance(RSA_ALGORITHM,
                    BC_PROVIDER);
            RSAPrivateKey privateK = (RSAPrivateKey) keyFactory
                    .generatePrivate(pkcs8KeySpec);
            Cipher cipher = Cipher.getInstance(RSA_ECB_OAEP_PADDING, BC_PROVIDER);
            cipher.init(Cipher.DECRYPT_MODE, privateK);

            int inputLen = encryptedData.length;
            ByteArrayOutputStream out = new ByteArrayOutputStream();
            int offSet = 0;
            for (int i = 0; i < inputLen - offSet; offSet = i * MAX_DECRYPT_BLOCK) {
                byte[] cache;
                if (inputLen - offSet > MAX_DECRYPT_BLOCK) {
                    cache = cipher
                            .doFinal(encryptedData, offSet, MAX_DECRYPT_BLOCK);
                } else {
                    cache = cipher
                            .doFinal(encryptedData, offSet, inputLen - offSet);
                }
                out.write(cache, 0, cache.length);
                ++i;
            }

            byte[] output = out.toByteArray();
            out.close();
            return output;
        }

        /**
         * <p>
         * 公钥解密2048
         * </p>
         *
         * @param encryptedData 已加密数据
         * @param publicKey     公钥(BASE64编码)
         * @return
         * @throws Exception
         */
        public static byte[] decryptByPublicKey2048(byte[] encryptedData,
                                                    String publicKey) throws Exception {
            byte[] keyBytes = decode64(publicKey);
            X509EncodedKeySpec x509KeySpec = new X509EncodedKeySpec(keyBytes);

            KeyFactory keyFactory = KeyFactory.getInstance(RSA_ALGORITHM,
                    BC_PROVIDER);
            Key publicK = keyFactory.generatePublic(x509KeySpec);
            Cipher cipher = Cipher.getInstance(RSA_ECB_OAEP_PADDING, BC_PROVIDER);
            cipher.init(Cipher.DECRYPT_MODE, publicK);

            int inputLen = encryptedData.length;
            ByteArrayOutputStream out = new ByteArrayOutputStream();
            int offSet = 0;
            byte[] cache;
            int i = 0;
            // 对数据分段解密
            while (inputLen - offSet > 0) {
                if (inputLen - offSet > MAX_DECRYPT_BLOCK) {
                    cache = cipher
                            .doFinal(encryptedData, offSet, MAX_DECRYPT_BLOCK);
                } else {
                    cache = cipher
                            .doFinal(encryptedData, offSet, inputLen - offSet);
                }
                out.write(cache, 0, cache.length);
                i++;
                offSet = i * MAX_DECRYPT_BLOCK;
            }
            byte[] decryptedData = out.toByteArray();
            out.close();
            return decryptedData;
        }

        /**
         * <p>
         * 私钥加密2048
         * </p>
         *
         * @param data       源数据
         * @param privateKey 私钥(BASE64编码)
         * @return
         * @throws Exception
         */
        public static byte[] encryptByPrivateKey2048(byte[] data, String privateKey)
                throws Exception {
            byte[] keyBytes = decode64(privateKey);
            PKCS8EncodedKeySpec pkcs8KeySpec = new PKCS8EncodedKeySpec(keyBytes);

            KeyFactory keyFactory = KeyFactory.getInstance(RSA_ALGORITHM,
                    BC_PROVIDER);
            Key privateK = keyFactory.generatePrivate(pkcs8KeySpec);
            Cipher cipher = Cipher.getInstance(RSA_ECB_OAEP_PADDING, BC_PROVIDER);
            cipher.init(Cipher.ENCRYPT_MODE, privateK);

            int inputLen = data.length;
            ByteArrayOutputStream out = new ByteArrayOutputStream();
            int offSet = 0;
            byte[] cache;
            int i = 0;
            // 对数据分段加密
            while (inputLen - offSet > 0) {
                if (inputLen - offSet > MAX_ENCRYPT_BLOCK) {
                    cache = cipher.doFinal(data, offSet, MAX_ENCRYPT_BLOCK);
                } else {
                    cache = cipher.doFinal(data, offSet, inputLen - offSet);
                }
                out.write(cache, 0, cache.length);
                i++;
                offSet = i * MAX_ENCRYPT_BLOCK;
            }
            byte[] encryptedData = out.toByteArray();
            out.close();
            return encryptedData;
        }

        /**
         * <p>
         * 公钥加密2048
         * </p>
         *
         * @param data      源数据
         * @param publicKey 公钥(BASE64编码)
         * @return
         * @throws Exception
         */
        public static byte[] encryptByPublicKey2048(byte[] data, String publicKey)
                throws Exception {
            byte[] keyBytes = decode64(publicKey);
            X509EncodedKeySpec x509KeySpec = new X509EncodedKeySpec(keyBytes);

            KeyFactory keyFactory = KeyFactory.getInstance(RSA_ALGORITHM,
                    BC_PROVIDER);
            Key publicK = keyFactory.generatePublic(x509KeySpec);
            // 对数据加密
            Cipher cipher = Cipher.getInstance(RSA_ECB_OAEP_PADDING, BC_PROVIDER);
            cipher.init(Cipher.ENCRYPT_MODE, publicK);

            int inputLen = data.length;
            ByteArrayOutputStream out = new ByteArrayOutputStream();
            int offSet = 0;
            byte[] cache;
            int i = 0;
            // 对数据分段加密
            while (inputLen - offSet > 0) {
                if (inputLen - offSet > MAX_ENCRYPT_BLOCK) {
                    cache = cipher.doFinal(data, offSet, MAX_ENCRYPT_BLOCK);
                } else {
                    cache = cipher.doFinal(data, offSet, inputLen - offSet);
                }
                out.write(cache, 0, cache.length);
                i++;
                offSet = i * MAX_ENCRYPT_BLOCK;
            }
            byte[] encryptedData = out.toByteArray();
            out.close();
            return encryptedData;
        }

        /**
         * <p>
         * 用私钥对信息生成数字签名
         * </p>
         *
         * @param data       已加密数据
         * @param privateKey 私钥(BASE64编码)
         * @return
         * @throws Exception
         */
        public static String sign(byte[] data, String privateKey) throws Exception {
            byte[] keyBytes = decode64(privateKey);

            PKCS8EncodedKeySpec pkcs8KeySpec = new PKCS8EncodedKeySpec(keyBytes);
            KeyFactory keyFactory = KeyFactory.getInstance(RSA_ALGORITHM,
                    BC_PROVIDER);

            PrivateKey privateK = keyFactory.generatePrivate(pkcs8KeySpec);
            Signature signature = Signature.getInstance(SIGNATURE_ALGORITHM,
                    BC_PROVIDER);
            signature.initSign(privateK);
            signature.update(data);
            return encode64(signature.sign());
        }

        /**
         * <p>
         * 校验数字签名
         * </p>
         *
         * @param data      已加密数据
         * @param publicKey 公钥(BASE64编码)
         * @param sign      数字签名
         * @return
         * @throws Exception
         */
        public static boolean verify(byte[] data, String publicKey, String sign)
                throws Exception {
            byte[] keyBytes = decode64(publicKey);

            X509EncodedKeySpec keySpec = new X509EncodedKeySpec(keyBytes);
            KeyFactory keyFactory = KeyFactory.getInstance(RSA_ALGORITHM,
                    BC_PROVIDER);
            PublicKey publicK = keyFactory.generatePublic(keySpec);
            Signature signature = Signature.getInstance(SIGNATURE_ALGORITHM,
                    BC_PROVIDER);

            signature.initVerify(publicK);
            signature.update(data);
            return signature.verify(decode64(sign));
        }

        /**
         * Created on 2013-5-31
         * <p>
         * Discription:编码
         * </p>
         *
         * @return String
         */
        public static String encode64(byte[] b) {
            return Base64.encodeBase64String(b);
        }

        /**
         * Created on 2013-5-31
         * <p>
         * Discription:解码
         * </p>
         *
         * @return byte[]
         */
        public static byte[] decode64(String s) {
            return Base64.decodeBase64(s);
        }

        /**
         * <p>
         * 获取私钥
         * </p>
         *
         * @param keyMap 密钥对
         * @return
         * @throws Exception
         */
        public static String getPrivateKey(Map<String, Object> keyMap) {
            Key key = (Key) keyMap.get(PRIVATE_KEY);
            return encode64(key.getEncoded());
        }

        /**
         * <p>
         * 获取公钥
         * </p>
         *
         * @param keyMap 密钥对
         * @return
         * @throws Exception
         */
        public static String getPublicKey(Map<String, Object> keyMap) {
            Key key = (Key) keyMap.get(PUBLIC_KEY);
            return encode64(key.getEncoded());
        }

        /**
         * <p>
         * 生成密钥对(公钥和私钥)
         * </p>
         *
         * @return
         * @throws Exception
         */
        public static Map<String, Object> genKeyPair() throws Exception {
            KeyPairGenerator keyPairGen = KeyPairGenerator
                    .getInstance(RSA_ALGORITHM);
            keyPairGen.initialize(KEY_SIZE);

            KeyPair keyPair = keyPairGen.generateKeyPair();
            RSAPublicKey publicKey = (RSAPublicKey) keyPair.getPublic();
            RSAPrivateKey privateKey = (RSAPrivateKey) keyPair.getPrivate();

            Map<String, Object> keyMap = new HashMap<String, Object>(2);
            keyMap.put(PUBLIC_KEY, publicKey);
            keyMap.put(PRIVATE_KEY, privateKey);
            return keyMap;
        }

        public static void main(String[] args) throws UnsupportedEncodingException {
            Map<String, Object> keyPair;
            try {
                keyPair = genKeyPair();
                String publicStr = RSAUtils.getPublicKey(keyPair);
                String privateStr = RSAUtils.getPrivateKey(keyPair);

                System.out.println("pub: " + publicStr);
                System.out.println("pri: " + privateStr);

                String text = "aaa";
                byte[] encryped = RSAUtils.encryptByPublicKey2048(text.getBytes(),
                        publicStr);
                byte[] decryped = RSAUtils.decryptByPrivateKey2048(encryped,
                        privateStr);
                System.out.println("text: " + text + ", decry: "
                        + new String(decryped));

                String text2 = "bbb";
                byte[] encryped2 = RSAUtils.encryptByPrivateKey2048(
                        text2.getBytes(), privateStr);
                byte[] decryped2 = RSAUtils.decryptByPublicKey2048(encryped2,
                        publicStr);
                System.out.println("text: " + text2 + ", decry: "
                        + new String(decryped2));

            } catch (Exception e) {
                e.printStackTrace();
            }
        }

    }

3.性能测试比较

| 解密方式 | 短字符串（长度4）平均用时（ms）   | 长字符串（长度10000）平均用时（ms） |
| ----- | --------- | ----------- |
| RSA | 82 | 2504 | 
| AES  | 31 | 62 | 
| 3DES  | 30 | 48 |
| 不解密  | 29 | 39 |

N.参考

1.[使用Jmeter进行http接口性能测试](https://www.cnblogs.com/star91/p/5059222.html)

# 其他

1.密钥交换IKE（Internet Key Exchange）通常是指双方通过交换密钥来实现数据加密和解密。

DH（Diffie-Hellman）算法是一种密钥交换算法，其既不用于加密，也不产生数字签名。DH算法通过双方共有的参数、私有参数和算法信息来进行加密，然后双方将计算后的结果进行交换，交换完成后再和属于自己私有的参数进行特殊算法，经过双方计算后的结果是相同的，此结果即为密钥。

    A有p和g两个参数，A还有一个属于自己的私有参数x；
    B有p和g两个参数，A还有一个属于自己的私有参数y；
    A和B均使用相同的加密算法计算其对应的值：value_A=p^(x%g)，value_B=p^(y%g)
    随后双方交换计算后的值，然后再分别使用自己的私有参数对去求次方，如：
    A拿到value_B值后，对其求x平方得value_B^x=p^(xy%g)；
    B拿到value_A值后，对其求y平方得value_A^y=p^(xy%g)；
    最终得到的结果是一致的。在整个过程中，第三方人员只能获取p、g两个值，AB双方交换的是计算后的结果，因此这种方式是很安全的。
    
2.LRU算法

整体的设计思路是，可以使用HashMap存储key，这样可以做到save和get key的时间都是O(1)，而HashMap的Value指向双向链表实现的LRU的Node节点。核心操作的步骤：
save(key, value)，首先在HashMap找到Key对应的节点，如果节点存在，更新节点的值，并把这个节点移动队头。如果不存在，需要构造新的节点，并且尝试把节点塞到队头，如果LRU空间不足，则通过tail淘汰掉队尾的节点，同时在HashMap中移除Key。
get(key)，通过HashMap找到LRU链表节点，把节点插入到队头，返回缓存的值。

3.一致性Hash算法

一致性Hash是一种特殊的Hash算法，由于其均衡性、持久性的映射特点，被广泛的应用于负载均衡领域和分布式存储，如nginx和memcached都采用了一致性Hash来作为集群负载均衡的方案。
普通的Hash函数最大的作用是散列，或者说是将一系列在形式上具有相似性质的数据，打散成随机的、均匀分布的数据。不难发现，这样的Hash只要集群的数量N发生变化，之前的所有Hash映射就会全部失效。如果集群中的每个机器提供的服务没有差别，倒不会产生什么影响，但对于分布式缓存这样的系统而言，映射全部失效就意味着之前的缓存全部失效，后果将会是灾难性的。
一致性Hash通过构建环状的Hash空间代替线性Hash空间的方法解决了这个问题。良好的分布式缓存系统中的一致性hash算法应该满足以下几个方面：

    平衡性(Balance)：平衡性是指哈希的结果能够尽可能分布到所有的缓冲中去，这样可以使得所有的缓冲空间都得到利用。很多哈希算法都能够满足这一条件。
    单调性(Monotonicity)：单调性是指如果已经有一些内容通过哈希分派到了相应的缓冲中，又有新的缓冲区加入到系统中，那么哈希的结果应能够保证原有已分配的内容可以被映射到新的缓冲区中去，而不会被映射到旧的缓冲集合中的其他缓冲区。
    分散性(Spread)：在分布式环境中，终端有可能看不到所有的缓冲，而是只能看到其中的一部分。当终端希望通过哈希过程将内容映射到缓冲上时，由于不同终端所见的缓冲范围有可能不同，从而导致哈希的结果不一致，最终的结果是相同的内容被不同的终端映射到不同的缓冲区中。这种情况显然是应该避免的，因为它导致相同内容被存储到不同缓冲中去，降低了系统存储的效率。分散性的定义就是上述情况发生的严重程度。好的哈希算法应能够尽量避免不一致的情况发生，也就是尽量降低分散性。
    负载(Load)：负载问题实际上是从另一个角度看待分散性问题。既然不同的终端可能将相同的内容映射到不同的缓冲区中，那么对于一个特定的缓冲区而言，也可能被不同的用户映射为不同的内容。与分散性一样，这种情况也是应当避免的，因此好的哈希算法应能够尽量降低缓冲的负荷。
    平滑性(Smoothness)：平滑性是指缓存服务器的数目平滑改变和缓存对象的平滑改变是一致的。

一致性Hash算法原理
简单来说，一致性哈希将整个哈希值空间组织成一个虚拟的圆环，如假设某哈希函数H的值空间为0-2^32-1（即哈希值是一个32位无符号整形），整个哈希空间环如下：整个空间按顺时针方向组织。0和2^32-1在零点中方向重合。
下一步将各个服务器使用Hash进行一次哈希，具体可以选择服务器的ip或主机名作为关键字进行哈希，这样每台机器就能确定其在哈希环上的位置，这里假设将上文中四台服务器使用ip地址哈希后在环空间的位置如下：
接下来使用如下算法定位数据访问到相应服务器：将数据key使用相同的函数Hash计算出哈希值，并确定此数据在环上的位置，从此位置沿环顺时针“行走”，第一台遇到的服务器就是其应该定位到的服务器。
下面分析一致性哈希算法的容错性和可扩展性。现假设Node C不幸宕机，可以看到此时对象A、B、D不会受到影响，只有C对象被重定位到Node D。一般的，在一致性哈希算法中，如果一台服务器不可用，则受影响的数据仅仅是此服务器到其环空间中前一台服务器（即沿着逆时针方向行走遇到的第一台服务器）之间数据，其它不会受到影响。
下面考虑另外一种情况，如果在系统中增加一台服务器Node X，此时对象Object A、B、D不受影响，只有对象C需要重定位到新的Node X 。一般的，在一致性哈希算法中，如果增加一台服务器，则受影响的数据仅仅是新服务器到其环空间中前一台服务器（即沿着逆时针方向行走遇到的第一台服务器）之间数据，其它数据也不会受到影响。
综上所述，一致性哈希算法对于节点的增减都只需重定位环空间中的一小部分数据，具有较好的容错性和可扩展性。

N.参考

(1)[【214期】面试官：聊聊常见的加密算法、原理、优缺点、用途](https://mp.weixin.qq.com/s/fwbYobEiRc36WxSQ2aDk8A)