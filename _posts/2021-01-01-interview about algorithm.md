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

# LeetCode题目思路整理

#### 1.有序数组中找到两个数，和正好为某个值，返回这两个数字的序号。

思路：没有说数组是否有序，所以一定不可能是先sort之后再去找的。那就需要按照原排列顺序，需要记住曾经出现过的数字的位置。HashMap。

#### 2.两个数值从后往前的链表，数值做加法，返回新链表。

思路：用0补齐长度，然后分别计算每一个节点相加的和，注意进位。

#### 3.字符串中最长的不包含重复字符的子字符串，返回该字符串的长度。

思路：碰到曾经出现的字符，就从队列头部开始移除，直至移除相同的字符。

#### 4.两个有序数组，返回所有数字排序后的中位数。

思路：数组长度假设分别为m,n。假设i,j分别为两个数组中的切割点。分别切割为A[0]---A[i-1],A[i]---A[m-1]和B[0]---B[j-1],B[j]---B[n-1];
应该保证j = (m+n+1)/2 - i。此时和为偶数时，两边各一半，为奇数时，左边比右边长1。
目标就是寻找一个合适的i,使得A[i-1] <= B[j],B[j-1] <= A[i]，此时所有两侧正好分成两半，且左侧的都小于右侧的。
特殊情况要考虑，切割点在最左、最右侧时。

#### 5.返回字符串的子字符串中最长的回文字符串。

思路：从字符串的每个位置开始向两侧扩张。两种方式，一种是从同一个位置开始，另一种是从相邻位置开始。

#### 6.原字符转按照蛇形方式重新排序成n行，返回重排后各行拼接而成的字符串。

思路：对字符分组，并找规律，List&lt;List&lt;Character&gt;&gt;。

#### 7.反转整数。

思路：用long类型来转换，考虑溢出的情况。

#### 8.字符串转化为整数。

思路：考虑各种异常情况，开头空白，开头非数字，正负号等。

#### 9.判断一个数字是否为回文数。

思路：回文数都是正整数，long类型先翻转数字顺序，再比较。

#### 10.判断字符串与正则字符串是否匹配。字符.代表任意一位，字符 * 与前一位组合代表0位或者多位。

思路：二维数组，如果s，p新增的最后一位相等，或者p最后一位为.，则matrix[i + 1][j + 1]与左上角相等。
如果p最后一位为 * ，如果p中 * 的前一位与s中当前位相等，或者p中 * 的前一位为.，则a * 或者. * 匹配情况有三种：
matrix[i + 1][j + 1]为matrix[i + 1][j]（情况1：对应于一位），
或为matrix[i + 1][j - 1]（情况2：对应于0位）
或为matrix[i][j+1]（情况3：对应于多位）
否则a * 匹配0位。

#### 11.一个数组中的数代表容器的侧壁高度，选择两个侧壁，求最大容积。

思路：假设左右两边相比，左侧更小，那么右侧向中心移动时，不管右侧的新值比左侧值更小或者更大，都会使总储水量减小。所以此时不应该把右侧向中心移动，应该移动左侧。

#### 12.正整数转化为罗马字母表示的字符串

思路：对于每一位数字的替换应该抽象为one,half,full。要考虑0--9应该分别怎样替换。

#### 13.罗马字母转换为数字。

思路：先遍历一遍累加，再减去双倍的小数位于大数左侧的情况的和。

#### 14.一组字符串，找到共同的前缀。

思路：依次检查每个位置，所有的字符串是否都是相同的char

#### 15.一个数组，找到其中可重复的三个数字和为0，返回这样的三个数的所有解答，即List&lt;List&lt;Integer&gt;&gt;。

思路：对于每个数字，通过left，right来夹逼remain。特别要注意造成超时的几个点：
(1)选取第一个数字时候，如果有重复要跳过；
(2)在每一轮的left,right夹逼remain时候，如果remain小于0，可以直接跳过；
(3)lists.contains(addList)判断是否重复，会造成超时；
(4)nums[left] + nums[right] == remain找到后，先left++,right--，然后要跳过重复；
(5)不等于remain时候，单边的left++或者right--也可以跳过重复（不跳过不会超时）；

#### 16.一个数组，找到其中可重复的三个数字和，与target值最接近，返回最接近的三个数的和。

思路：与15题类似，通过left，right来夹逼remain。

#### 17.电话上2-9的数字分别代表几个字符，给出一个数字字符串，求所有可能对应的字符串。

思路：(1)backTracking，更优。(2)处理每一位时，将list中已经存在的String全部取出处理一遍。

#### 18.一个数组，找到其中可重复的四个数字和为target，返回这样的四个数的所有解答，即List&lt;List&lt;Integer&gt;&gt;。

思路：与15题类似，双层循环，然后最后两个数left，right左右夹逼。

#### 19.一个链表，移除从末尾数第n个节点。

思路：快慢指针，特别要注意移除的是head的情况。

#### 20.判断字符串是否为合法闭合的括号对。

思路：Deque&lt;Character&gt;当做栈使用，注意要保证stack中有元素，否则stack.peek()为null，null再与'(','[','{'在用==比较时，会抛异常。

#### 21.merge两个有序链表。

思路：递归，每次处理一个ListNode。

#### 22.返回所有由n对括号组成的合法闭合的括号对字符串。

思路：递归，左括号个数要比右括号个数多。

#### 23.k个有序链表，merge成一个。

思路：递归，每次处理一个ListNode。

#### 24.链表中的节点每两个一组交换位置。

思路：每两个作为一组，完成位置交换之后递归。

#### 25.链表中的节点每k个一组逆序。

思路：只考虑反转k个节点，后面的递归。

#### 26.有序数组中只留下不重复的数字，且都移动到数组的最前面。

思路：从前往后遍历，符合条件的依次填入相应的位置。

#### 27.有序数组中移除与指定数字相等的值，留下的都移动到数组的最前面。

思路：从前往后遍历，符合条件的依次填入相应的位置。

#### 28.返回子字符串第一次出现的起始位置。

思路：依次比较。

#### 29.不使用乘除法，实现两个整数相除。

思路：多次的位运算左移，要考虑边际的特殊情况。

#### 30.一个字符串和一个字符串数组（数组中所有字符串等长），返回字符串数组中全部字符串拼接得到的字符串，在第一个字符串中的起始位置。

思路：先建立一个HashMap记录words中词应该出现的次数，再遍历字符串，每次截取等长的，如果出现不应该出现的，或者出现的次数超过应该出现的次数，就break，否则验证到最后了就是符合的。

#### 31.求比数组中数字代表的值大的下一个数组排列，对原数组操作。

思路：从末尾开始找到非递增的位置，然后从其后面找到一个比它稍大的数字的位置，并交换。最后重排非递增位置的后续其他位置的数。

#### 32.由小括号组成的字符串，求最长的正确闭合的子字符串的长度。

思路：依次以每个位置为起始，符合闭合的条件是left == right,可以继续循环的条件是left >= right。

#### 33.无重复数字的有序数组以某位置为轴反转，在其中寻找给定的数字，找到返回位置，没找到返回-1。

思路：判断转轴位置，以判断左右两侧哪侧递增。二分查找。

#### 34.有序数组中查找指定数字，返回指定数字的最左、最右位置，没有就返回[-1,-1]。

思路：二分查找，找到后向两边扩展。

#### 35.有序数组中查找指定数字，找到就返回其位置，没找到就返回其应该插入的位置。

思路：按顺序找到与target相等或者大于它的位置。

#### 36.给定9 * 9的不完整的数独二维数组，是否符合数独规则（行、列、3 * 3小区域）。

思路：分别验证行，列，每个区域。

#### 37.给定9 * 9的不完整的数独二维数组，在原二维数组上求解数独。

思路：backTracking依次填充每个空的位置，考虑数独规则哪些数字符合填充条件。要注意退出条件，可能已经找到了结果，但是在后续的循环中又把结果清除了。找到结果要及时退出。

#### 38.用“几个几”的方式读出数字，返回第n个数字。

思路：递归，第n个数字取决于第n-1个数字的读法。

#### 39.给定不包含重复数字的数组及目标和，返回所有和为目标和的数组，每个数字可以使用多次。

思路：backTracking。

#### 40.给定可能包含重复数字的数组及目标和，返回所有和为目标和的数组，每个数字可以使用一次。

思路：backTracking，注意与39题目调用backTracking函数时传参数的差异。

#### 41.给定无序数组，返回第一个缺失的正整数

思路：排序后，跳过非正数，然后找到第一个不满足等于或与前一个数差1的数字，就是缺数的位置 。

#### 42.非负数组表示坐标轴上每个位置的柱高度，返回最多储水量。

思路：对于每一个位置的值，应该分别找到左右两侧最大的值，找到其中较小的，减去当前位置的值，就是这个位置能够储水的量。

#### 43.两个非负字符串数字的乘法。

思路：考虑每一位数字计算的过程。

#### 44.通配符匹配字符串，s表示字符串，p表示正则表达式，?匹配一个字符， * 匹配空字符或任意长字符，返回是否匹配。

思路：两个指针分别移动，遇到？或者字符相等就都移动，遇到 * 记录最后一个 * 的位置和该 * 对应的最后一个字符位置，
都不符合如果曾出现 * ，有可能是 * 对应的很长。p指针回到 * 的下一个，加长 * 对应的字符串长度，移动match。s到最后时，如果p后面是 * 就移动。

#### 45.数组中数字表示在该位置可以跳的最远的步数，返回到达数组末尾所需的最少跳跃次数。

思路：curFarthest：当前位置能达到的最远位置，curEnd：当前步数能达到的最远位置，jumps：步数。
如果当前位置达到了当前步数能到达的最远位置，必须再跳一步，并将当前步数最远距离更新为当前位置最远距离。

#### 46.给定不包含重复数字的数组，返回数组中数字的所有排列组合。

思路：backTracking。

#### 47.给定可能包含重复数字的数组，返回数组中数字的所有排列组合。

思路：backTracking。

#### 48.n * n的二维数组，整体向右旋转，返回旋转后的二维数组。

思路：一圈圈依次交换每条边。

#### 49.字符串数组，将字符串归组，每组中的字符串都是相同字符重排后组成。

思路：重排序后的字符串作为key，value是对应的List&lt;String&gt;。

#### 50.求x的n次方，x为浮点数，n为整数。

思路：递归。考虑n为0，1，Integer.MIN_VALUE,x为0.0等异常情况。n分奇偶数考虑。

#### 51.给定n，返回所有的二维数组，使得n个Q，不在同一行、同一列、同一斜线上。

思路：backTracking。

#### 52.给定n，返回所有解的个数，使得n个Q，不在同一行、同一列、同一斜线上。

思路：同51题。

#### 53.给定数组，返回连续子数组和的最大值。

思路：retMax为全局的最大值。sums是和nums等长的数组，sums[i]表示以i结尾数组中最大的值。对于新添加的num[i]，有两种选择，要么已自己为头新开一个数组，要么跟前面的数组结合组成新数组。

#### 54.m * n的二维数组，返回按照顺时针顺序打印的数组。

思路：考虑什么情况下会有中间留下一行或者一列。依次访问每一圈的数字。

#### 55.数组中数字表示在该位置可以跳的最远的步数，返回是否可以跳到数组的末尾。

思路：每次计算最远到达的位置。

#### 56.给定一组区间，返回区间merge之后的结果。

思路：先依据interval.start排序，然后每次确定是否可以和下一个merge，能merge就merge，不能就把前一个放入结果的List&lt;Interval&gt;。

#### 57.给定一组区间，和一个待插入的区间，返回插入并merge之后的结果。

思路：判断能否merge，不能merge就放入结果list，否则再判断与下一个。

#### 58.返回包含空格的字符串中最后一个单词的。

思路：从末尾开始，考虑特殊情况。

#### 59.给定正整数n，返回以顺时针顺序旋入的n * n的二维数组。

思路：看有多少圈，然后处理每一圈的数字。

#### 60.给定n，返回1--n组成的数字排列中第k个排列。

思路：首先获得阶乘数组，计算每一位的值，除以的是(n-1)!，然后取pos个没有使用过的数值。

#### 61.链表向右反转k个位置。

思路：考虑k可能比链表长度要大，找到翻转的点。

#### 62.m * n的二维矩阵，原来位于左上角，目标位置位于右下角，只能向下向右走，问有多少总走法。

思路：用二维数组存储每个i,j位置的可能的种数。

#### 63.m * n的二维矩阵，二维矩阵中1表示障碍，原来位于左上角，目标位置位于右下角，只能向下向右走，问有多少总走法。

思路：用二维数组存储每个i,j位置的可能的种数，有障碍的地方置为0。

#### 64.m * n的二维数组，求从左上角到右下角的最小路径和。

思路：考虑边沿，比较上面和左侧的最小值。

#### 65.判断一个字符串是否为一个合法的十进制小数。

思路：考虑空格,'.','e',正负号在不同位置的特殊情况。定义seenNum、seenE、seenD分别表示是否见到数字、e、点号。
出现'.'，如果曾出现过点，或者曾出现e，就是非法的。
出现'e'，如果e曾经出现过，或者没有出现过数字，就是非法的。
正负号，如果不在首位，也不在e后面，就是非法的。
其他特殊字符，就是非法的。最后返回seenNum。

#### 66.返回数组中数组成的数字加1。

思路：考虑进位。

#### 67.两个非空二进制字符串相加。

思路：补成相同长度，考虑进位分别相加。

#### 68.给定字符串数组和最大宽度maxWidth，校正为等间距排列的字符串，返回校正后的每一行的字符串。

思路：确定哪些字符串可以放在一行后，按照要求的规律摆放，特别注意最后一行。

#### 69.开方取整。

思路：二分查找。

#### 70.n级楼梯，每次可爬1级或2级，返回爬的种数。

思路：递归，f(n) = f(n-1) + f(n-2)。

#### 71.字符串表示路径，返回简化后的路径。

思路：split方法分割字符串为字符串数组，然后再按照逻辑拼接。

#### 72.给定两个字符串，通过插入、移除、替换三种操作，返回从第一个字符串编辑为第二个字符串需要的步数。

思路：二维数组动态规划。word1.charAt(i) == word2.charAt(j)时，dp[i+1][j+1] = dp[i][j]，否则为1 + min{上、左、左上}，分别对应于删除、插入、替换。

#### 73.m * n的二维数组，将存在0的行和列都置为0。

思路：先遍历一遍，记录需要置为0的行和列。

#### 74.在m * n的二维数组中查找某个数，每一行中数字递增，每一行最小数字大于上一行最大数字。

思路：先找在哪一行，再在某一行中二分查找。

#### 75.由0,1,2组成数组，经过一轮遍历进行排序。

思路：zeroPos保存可供交换为0的最前面的位置，该位置之前的都已经置为0保存。
twoPos可供交换为2的最后面的位置，该位置之后的都已经置为2。
依次找0和2，0与zeroPos交换，2与twoPos交换。

#### 76.两个字符串S和T，在S中寻找最短子字符串，使得子字符串包含T中所有出现的字符。

思路：先用数组记录t字符串的特征。两个指针，伸缩尺的思想。
lo,hi分别为字符串低指针，高指针，hi-lo+1为字符串长度，初始时hi和lo都在最左侧。
hi先移动，如果碰到是t中字符，就cnt++。
当cnt与t长度相等，说明所有字符都已经出现，再去改变lo指针位置，缩短长度，直到不满足cnt == t.length()。

#### 77.给定n和k，返回所有1--n数字中k个数的列表。

思路：backTracking。

#### 78.不包含重复数字的数组，返回所有子集。

思路：backTracking。

#### 79.在二维字符数组中查找给定的单词，返回是否存在。

思路：以每个位置为起始去backTracking。

#### 80.在有序数组中移除重复的数字，每个数最多出现两次，末尾剩余的数字无关紧要。

思路：真实index递增，并用HashMap记录出现的次数。

#### 81.可能有重复数字的有序数组以某位置为轴反转，在其中寻找给定的数字，返回是否找到。

思路：判断转轴位置，以判断左右两侧哪侧递增。二分查找。
特别注意转轴在重复的数字的情况，这时候right或者left（看跟哪个比的）不应该由mid决定。而应该是left++或者right--。

#### 82.从有序链表中移除重复数字，重复数字全部移除。

思路：fakeHead，找到开始重复的节点后一直向后。

#### 83.从有序链表中移除重复数字，只保留一个数字。

思路：删除重复的ListNode，要注意当不重复可以直接curr = curr.next，但是删除重复的节点后，不能直接curr = curr.next。

#### 84.数组表示的柱状图，求最大矩形面积。

思路：从每个位置向两侧扩展，扩展的条件是高度大于等于目前位置高度。

#### 85.由0和1组成的二维数组，返回全部由1组成的最大矩形面积。

思路：遍历每一行，每一行都可以得到height[],left[],right[]三个数组
height[i],在当前行的当前列位置，连续的1的高度
left[i],从当前列往左看，如果出现0，则为0，如果出现1，则为向左扩展的最大值。currLeft为从当前位置看，本行最左边的1的列号。
right[i],从当前列往右看，如果出现0，则为n，如果出现1，则为向右扩展的最小值。currRight为从当前位置看，本行最右侧的i的列号

#### 86.给定一个链表和一个数x，重新排列节点使得比x小的节点为左半部分，大于等于x的节点为右半部分，且左右两部分都保持原数字的相对顺序。

思路：依次处理每个节点，链接到left和right上。

#### 87.给定两个字符串s1和s2，判断s2是否为s1的scramble字符串。scramble的定义是将字符串拆分为二叉树，并交换左右子树。

思路：递归。两个字符串符合scramble的条件是，字符都相同，并且可以在字符的某个位置切割后，左右两侧的字符串分别都符合scramble的条件递归，左对左右对右，左对右右对左两种情况。

#### 88.merge两个有序数组到数组1中，数组1中有足够空间。

思路：从右往左遍历，从大到小赋值。

#### 89.给定n，返回n位格雷码（相邻的只相差一比特位）代表的数组。

思路：反向取出上一轮的，再添加第一位数字。

#### 90.可能包含重复数字的数组，返回所有子集。

思路：backTracking。

#### 91.A--Z分别代表1--26，给定非空数字字符串，返回解码为字母的种数。

思路：s长度为1和2时，应该特殊去考虑。其他情况下，认为s的长度从后往前依次扩展，增加一位。该位置可能的解码种数为i+1,i+2位置之和。

#### 92.给定链表和1<=m<=n<=长度，将m与n之间位置的节点反转。

思路：翻转中间的，保存两侧的。

#### 93.给定数字字符串，返回所有可能的IP地址字符串。

思路：backTracking，注意考虑特殊情况，以0开头的数字，超长的字符串。

#### 94.给定二叉树，返回中序遍历得到的数组。

思路：使用Deque当做Stack使用。每个节点如果存在left子节点，就把父节点放入stack，应该后续还要使用父节点，所以一定是stack的结构。
当从stack中取出一个节点后，把它的值放入list。然后如果其有右子节点，则curr为右子节点。右子节点也符合如果有left，依次放入stack的规律。

#### 95.给定整数n，返回所有不同的二叉搜索树。

思路：有范围地(start,end)建立树。

#### 96.给定整数n，返回所有不同的二叉搜索树的种数。

思路：找规律f(0)=1，f(1)=1，f(2)=2=f(1)*f(0) + f(1)*f(0)，f(3)=5=f(2)*f(0) + f(1)*f(1) + f(0)*f(2)

#### 97.给定三个字符串s1，s2，s3，判断是否s3是s1和s2交织而成。

思路：matrix[i][j]代表了使用s1前i位，和s2前j位，能否组成s3的前i + j位置

#### 98.判断给定的二叉树是否为二叉搜索树。

思路：左右子树都是BST,并且root.val大于左侧最大，小于右侧最小。

#### 99.二叉搜索树中两个节点错误地交换了，恢复为二叉搜索树。

思路：对于BST，inorder traverse会产生从小到大排列的数字。所以需要找到的两个交换的节点就是不符合的位置。

#### 100.判断两棵二叉树是否相同。

思路：递归。

#### 101.判断一棵二叉树是否镜像对称。

思路：把问题转化为两棵树的对称。

#### 102.给定一棵二叉树，返回层级遍历的结果，即List&lt;List&lt;Integer&gt;&gt;。

思路：层级遍历。

#### 103.给定一棵二叉树，返回Z字形层级遍历的结果，即List&lt;List&lt;Integer&gt;&gt;。

思路：每次从右侧开始取prev，然后根据flag决定先取左节点还是右节点。

#### 104.给定一棵二叉树，返回最大深度。

思路：递归，左右两子树最大深度 + 1。

#### 105.给定先序和中序遍历的两个数组，返回二叉树。

思路：转化为有在两个给定数组中起始位置，和树节点数目的递归函数。

#### 106.给定中序和后序遍历的两个数组，返回二叉树。

思路：递归构建左右子树。

#### 107.给定一棵二叉树，返回从叶子节点到根节点反向的层级遍历结果，即List&lt;List&lt;Integer&gt;&gt;。

思路：prev和next两个List&lt;TreeNode&gt;。

#### 108.给定有序数组，返回一棵平衡二叉搜索树。

思路：转化为用数组给定范围内数字构建平衡二叉搜索树的递归问题。

#### 109.给定有序链表，返回一棵平衡二叉搜索树。

思路：递归，通过快慢指针每次找到作为root的点，然后分别建立左右子树。

#### 110.判断一棵二叉树是否为平衡二叉树。

思路：获得左右子树的深度，考虑平衡二叉树的条件。

#### 111.给定一棵二叉树，返回最小深度。

思路：递归。

#### 112.给定一棵二叉树，判断是否存在从根节点到叶子节点的路径和为给定数字。

思路：递归。

#### 113.给定一棵二叉树，返回所有可能的路径List&lt;List&lt;Integer&gt;&gt;，使得从根节点到叶子节点的路径和为给定数字。

思路：backTracking。

#### 114.给定一棵二叉树，使之变平为一个链表。

思路：递归，先移动左子树，后移动右子树。

#### 115.给定字符串S和T，从S中选取一些字符拼接起来正好与T相等，返回选取的种数。

思路：backTracking方法会超时。
matrix[i][j]表示s的前i位，t的前j位，满足条件的种数，当S增加了一位，增加的一位可以参与匹配，也可以不参与匹配。
参与匹配时(必须s.charAt(i-1) == t.charAt(j-1))种数加matrix[i-1][j-1]，不参与匹配时种数加matrix[i-1][j]。

#### 116.给定一棵完整二叉树，每个节点有三个指针分别是left,right,next，将同一层级中的节点通过next指针相连。

思路：prev和next两个list，层级遍历，每次层级建立next指针。

#### 117.给定一棵二叉树，每个节点有三个指针分别是left,right,next，将同一层级中的节点通过next指针相连。

思路：prev和next两个list，层级遍历，每次层级建立next指针。

#### 118.给定n，按照帕斯卡三角形的规则，返回各层级的数字，即List&lt;List&lt;Integer&gt;&gt;。

思路：根据上一层生成下一层数字。

#### 119.按照帕斯卡三角形的规则，返回索引为n的层级的数组成的列表。

思路：prev,next两个List&lt;Integer>&gt;。

#### 120.给定三角形，即List&lt;List&lt;Integer&gt;&gt;，只能走层级间相邻位置，返回从三角形头部到底部的最小路径和。

思路：从底部向上更新三角形。

#### 121.给定一数组，代表每天的价格，只允许最多进行一次交易，买一次卖一次，返回最大利润。

思路：方法1：计算价格变动，然后计算变动累计，累计负值放弃，正值保留。
方法2：参考session2 q121的解法，所谓的买卖一次的最大利润，就是对于某一天，找到在它之前的最小值，计算差。

#### 122.给定一数组，代表每天的价格，允许进行任意多次交易，返回最大利润。

思路：比较相邻值，如果正值添加到temp中，表示持续增长，如果负值，累加到sum中。

#### 123.给定一数组，代表每天的价格，只允许最多进行两次交易，返回最大利润。

思路：buy1,buy2,sell1,sell2分别记录第一次买，第二次买，第一次卖，第二次卖时的最大值。

#### 124.给定一棵二叉树，返回最大的从某个节点到另一个节点的路径和。

思路：将问题转化为递归函数maxPathDown，该函数返回以输入节点为父节点的单条路径的最大值（不能左右子树的路径都包括，这样就分叉了），在递归过程中不断比较更新全局的最大值maxValue。

#### 125.判断一个字符串是否回文，忽略大小写和符号。

思路：过滤不符合的，左右两个指针依次比较。

#### 126.给定起始字符串，终止字符串，以及字符串列表，每次只能变化一个字符，且变化的字符在字符串列表中，返回所有可能的最短字符串变化过程列表，即List&lt;List&lt;String&gt;&gt;。

思路：方法1：backTracking时间超限。
方法2：先BFS，用neighbours（HashMap&lt;String,List&lt;String&gt;&gt;）保存每个字符串和变化一个字符后可能达到的字符串列表。
distance（HashMap&lt;String, Integer&gt;）保存每个字符串和从起始字符串变化到当前字符串需要的最小步数。
最后再通过DFS得到所有解。

#### 127.给定起始字符串，终止字符串，以及字符串列表，每次只能变化一个字符，且变化的字符在字符串列表中，返回从起始字符串变化到终止字符串需要的最小步数。

思路：方法1：backTracking时间超限。方法2：用队列去层级遍历，queue中的字符串，循环改变每一个字符，如果在可选字符串列表中就用reached记录曾出现过，每完成一个层级的循环level++。

#### 128.给定一个数组，返回连续的数字的最长长度（不同数字个数）。

思路：先排序，在根据与前一个数是否相同，是否相差1决定能否与前面连起来还是作为新的开头。

#### 129.给定二叉树，把从根节点到叶子节点的数字作为一个数，求所有路径的和。

思路：backTracking，每到叶子节点时就把tempList中数字取出来累加。

#### 130.由字符'O'和'X'组成的二维字符数组，将所有被'X'包围的'O'转化为'X'，。

思路：边沿及其连接位置的O都保留，其他位置的要转变。先将边沿及其连接位置的转换成其他字符。

#### 131.给定一字符串，返回所有可能的分割，使得字符串分割后的子字符串都是回文字符串。

思路：backTracking。

#### 132.给定一字符串，返回最少分割次数，使得字符串分割后的子字符串都是回文字符串。

思路：f[len]数组用来存储每个索引位置长度的字符串最小的切割刀数，初始为每个位置都切割。
每个索引位置，考虑奇偶两种情况，向两侧扩展，如果left位置为0了，不需要切割，否则，更新f数组对应的f[right]。

#### 133.一个新的数据结构表示无向图中的一个节点，拷贝并返回该无向图。

思路：Queue层级遍历，用HashMap保存老节点与新节点对应关系，节点都新建后再建立无向图关联。

#### 134.gas和cost分别是两个数组，gas表示可以加油数量，cost表示从当前节点到下一节点耗油情况，节点呈圆环分布，判断是否可以从某节点起步环绕一周，如果可以就返回起始节点索引。

思路：依次考虑从每个位置开始的情况。

#### 135.给定一个数组表示一组孩子的评分，给这些孩子分配糖果，每个人最少一个，且如果一个孩子评分比他左右两侧孩子高，将获得更多的糖果，返回最少需要的糖果总数。

思路：如果某个位置rating比其他邻居小，则得到的candy一定少；如果某个位置rating比其他邻居大，则得到的candy一定多；如果某个位置rating跟其他邻居一样大，则得到的candy无相关性，可大可小。
先都分配1，再正向循环一次确保上升情况，在反向循环一次确保下降情况。

#### 136.给定无序数组，其中的数字除了一个数，其他都出现两次，返回只出现一次的数。

思路：排序后两两比较。

#### 137.给定无序数组，其中的数字除了一个数，其他都出现三次，返回只出现一次的数。

思路：排序后，依次比较是否三个为一组。

#### 138.给定一链表，链表上的节点都有一random指针，指向某个节点或者null，返回该链表的深拷贝。

思路：用HashMap保存旧节点与新节点的对应关系。

#### 139.给定一字符串和一字符数组，判断该字符串是否可以被分割且每个分割后的子字符串都是字符串数组中的元素。

思路：动态规划，每次考虑增长一个字符，要么自身可以break，要么和前面的字符组合起来可以break。

#### 140.给定一字符串和一字符数组，返回所有分割后字符串组成的句子，使得该字符串被分割且每个分割后的子字符串都是字符串数组中的元素。

思路：方法1：backTracking超时。方法2：深度遍历，如果s以wordDict中某一项开头，则递归剩下的字符串，再把wordDict中某一项与剩下的字符串的结果拼接，作为最终结果。

#### 141.给定一链表，判断是否存在环。

思路：快慢指针。

#### 142.给定一链表，如果存在环，返回环开始的节点，否则返回null。

思路：快慢指针位置重遇后，将一个移动到head，再移动相同步就能找到开头位置。

#### 143.给定链表1->2......n-1->n，以1->n->2->n-1......的顺序重排链表。

思路：找到分界点，翻转后一半，再merge。

#### 144.给定一棵二叉树，返回先序遍历的结果。

思路：Deque&lt;TreeNode&gt;当做stack用。

#### 145.给定一棵二叉树，返回后序遍历的结果。

思路：Stack，遍历后结果reverse。

#### 146.实现最不经常使用缓存的key-value形式的数据结构。get操作返回对应的value，如果不存在就返回-1。put操作插入或更新，超出capacity的会被新插入的取代。

思路：List&lt;Integer&gt;保存key出现的顺序，Map保存对应关系，get操作更新List中出现顺序，put操作根据key是否已经存在做不同操作。

#### 147.使用插入排序算法排序一个给定的链表。

思路：每次从原链表中取出一个，插入到新的链表中，递归操作。

#### 148.排序一个链表，固定的空间复杂度，O(nlogn)的时间复杂度。

思路：快慢指针找到前后两段，对分别排序后的两段merge排序。

#### 149.给定二维坐标系上的点的数组，返回最多有多少个点在同一条直线上。

思路：以某个点为起点分别穿过后续的点作发散线，根据辗转相除处理后的斜率得到该点的最多线条数。
max为以point[i]为起点的发散线，斜率相等的直线条数最大值。
再比较每个点为起点的情况。max + overlap + 1（斜率相等的直线条数最大值 + 重合点 + 发散点本身）。

#### 150.给定字符串数组表示反波兰表示法，返回四则运算的结果。

思路：Deque作为Stack使用。

#### 151.给定一字符串代表一句子，将句子中单词位置反转，但每个单词不变，返回反转后重新组成的句子。

思路：注意特殊情况，字符串前后有空格，多个空格。

#### 152.给定一数组，返回数组中连续数字的积的最大值。

思路：对于每个新的数字有两种选择，要么与前面结合、要么作为新的开头。保存max,min，每增加一个数更新max,min。

#### 153.没有重复数字的有序数组以某位置为轴反转，在其中寻找最小数。

思路：二分法，特别注意就剩下两个，无限循环的情况。

#### 154.可能包含重复数字的有序数组以某位置为轴反转，在其中寻找最小数。

思路：二分法，特别要注意在重复数字处转轴，此时最小数在左、右侧都有可能，只是right--;。

#### 155.实现最小栈数据结构的push,pop,top,getMin函数。

思路：map<Integer,Integer>保存stack的size与最小值的对应关系。因为考虑pop时候要寻回以前的最小值，需要记住以前的情况。