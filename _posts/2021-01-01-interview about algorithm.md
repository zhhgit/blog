---
layout: post
title: "后端开发面试题 -- 算法与数据结构篇"
description: 后端开发面试题 -- 算法与数据结构篇
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
                    //若果右子节点的值较大    
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

N.参考

(1)[【214期】面试官：聊聊常见的加密算法、原理、优缺点、用途](https://mp.weixin.qq.com/s/fwbYobEiRc36WxSQ2aDk8A)