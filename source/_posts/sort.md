---
title: java实现8种排序算法
date: 2019-09-04 10:20:17
tags: 排序算法
categories: 算法
---
## 8种排序算法
```
冒泡排序，选择排序，插入排序，快速排序，希尔排序，归并排序，堆排序，基数排序
```
**源码地址:**
[https://github.com/wonder-code/myWonder/tree/master/basis/src/main/java/com/zl/algorithm/sort](https://github.com/wonder-code/myWonder/tree/master/basis/src/main/java/com/zl/algorithm/sort)
<!--more-->
## 冒泡排序
`时间复杂度 平均:o(n^2),最差:o(n^2)`
`空间复杂度 o(1)`
&emsp;&emsp;冒泡排序核心是将数组中元素两两进行比较，将较大的值与较小的值进行交换，最终数组中最大的值就到达数组的末尾（将较小的值进行交换，最终数组中最小的值就到达末尾），再将剩余的元素继续比较，选择出第二大（或小）的元素，以此类推，直至有序。
{% img bublesort /images/sort/buble.gif %}
冒泡排序的java实现:
```java
public int[] sort(int[] array){
	for(int i=0;i<array.length-1;i++){
		int flag=0;
		for(int j=0;j<array.length-i-1;j++){
			if(array[j]>array[j+1]){
				int temp;
				temp = array[j];
				array[j] = array[j+1];
				array[j+1] = temp;
				flag=1;
			}
		}
		if(flag==0){
			break;
		}
	}
	return array;
}
```
非常明了，其中`flag`作为一个标记，当比较一轮下来没有元素发生交换时，此时数组便已有序，即跳出循环，提高效率。
## 选择排序
`时间复杂度	平均o(n^2),最坏o(n^2)`
`空间复杂度	o(1)`
&emsp;&emsp;选择排序的核心是:利用长度为2的中间数组存储目标数组中最大（或最小）元素的下标和值，将该元素与起始位置元素交换，直至有序。
{% img selectsort /images/sort/select.gif %}
选择排序的java实现:
```java
public int[] sort(int[] array){
	int temp;
	for(int i=0;i<array.length-1;i++){
		int[] tag = new int[]{i,array[i]};	//中间数组
		for(int j=i+1;j<array.length;j++){
			if(tag[1]>array[j]){	//有元素比中间数组中元素小，则改变中间数组
				tag[0] = j;
				tag[1] = array[j];
			}
		}
		if(tag[0]!=i){	//若中间数组不为原数组，则交换位置
			temp = tag[1];
			array[tag[0]] = array[i];
			array[i] = temp;
		}
	}
	return array;
}
```
选择排序类似于冒泡排序，都是依次将数组中最大或最小的元素进行排序，直至有序。
## 插入排序
`时间复杂度	平均o(n^2)，最差o(n^2)`
`空间复杂度	o(1)`
&emsp;&emsp;插入排序的核心是:将数组的第一个元素视为已有序，将后续元素依次与有序的元素比较，并插入到有序数组中使之依然有序。
{% img insertsort /images/sort/insert.gif %}
插入排序的java实现:
```java
public int[] sort(int[] array){
	for(int i=1;i<array.length;i++){
		int temp = array[i];//未排序中进行比较的数字
		int index = i-1;//已排序中进行比较的下标
		while(index>=0 && array[index]>temp){//比较的下标须大于0且比较的数字须大于未排序中比较的数字
			array[index+1] = array[index];//已排序的数字向后移一位
			index--;//比较的下标向左移
		}
		array[index+1] = temp;//比较的数字小于未排序中比较的数字，将该数字插入后一位（空位）
	}
	return array;
}
```
&emsp;&emsp;插入排序在处理数组越无序，需交换元素的次数越多，排序越困难。相反，目标数组越有序，插入排序越容易。在面对数组较无序时，可使用希尔排序代替插入排序。
## 快速排序
`时间复杂度	平均:o(nlogn)，最差o(n^2)`
`空间复杂度	o(logn)`
&emsp;&emsp;快速排序的核心是:(二分法思想)取一个基准数，将小于此基准数的元素放到其左边，将大于此基准数的元素放到其右边，然后对左右两边的元素继续做此操作，直至基准数左右两边的元素个数为0，此时该序列已有序。
{% img quicksort /images/sort/quick.gif %}
快速排序的java实现:
```java
public int[] sort(int[] array){
	return change(array,0,array.length-1);
}
public int[] change(int[] array,int left,int right){

	int temp = array[left];//取一个基准数
	int l = left;
	int r = right;
	while (l<r){//左指针小于右指针
		while (l<r && array[r]>=temp){//从右边扫描，直到数据小于基准数
			r--;//右指针左移
		}
		if (array[r]<temp){//数据小于基准数时
			array[l] = array[r];//把数据填到左指针指向的位置
		}
		while (l<r && array[l]<=temp){//从左边扫描，直到数据大于基准数
			l++;//左指针右移
		}
		if ((array[l]>temp)){//数据大于基准数时
			array[r] = array[l];//把数据填到右指针指向的位置
		}
	}
	array[l] = temp;//跳出循环时，左右指针重合，将基准数填入。左侧的数都小于基准数，右侧的数都大于基准数。
	if(l-1>left){//若重合位置的前一个不是初始左指针的位置
		array = change(array,left,l-1);//将左侧的数据再次排序
	}
	if(r+1<right){//若重合位置的后一个不是初始右指针的位置
		array = change(array,r+1,right);//将右侧的数据再次排序
	}
	return array;
}
```
## 希尔排序
`时间复杂度	平均:o(nlogn),最差:o(nk)k与选取的增量有关`
`空间复杂度	o(1)`
&emsp;&emsp;希尔排序的核心是:希尔排序又称作‘分组插入排序’，选取一个增量k，将目标数组中所有下标差值为k的元素作为一组进行（逻辑上的）插入排序，逻辑上的排序是指在源数组中改变对应元素的位置，并不影响其他元素。
&emsp;&emsp;排序后，将增量减小，继续分组插入排序，直至增量为1，增量为1即正常的插入排序，此时数组已经基本有序，插入排序效率高。
{% img shellsort /images/sort/shell.gif %}
希尔排序的java实现:
```java
public int[] sort(int[] array){
	int number = array.length / 2;
	int i;
	int j;
	int temp;
	while (number >= 1) {
		for (i = number; i < array.length; i++) {
			temp = array[i];
			j = i - number;
			while (j >= 0 && array[j] > temp) { //需要注意的是，这里array[j] > temp将会使数组从小到到排序。
				array[j + number] = array[j];
				j = j - number;
			}
			array[j + number] = temp;
		}
		number = number / 2;
	}
	return array;
}
```
## 归并排序
`时间复杂度	平均:o(nlogn),最差o(nlogn)`
`空间复杂度	o(n)`
&emsp;&emsp;归并排序仍然采用二分法的思想以及递归的思想，先将数组拆分，即‘归’。将拆分后的数组进行排序，然后合并，即‘并’。
原理:
&emsp;&emsp;归：将数组从中间位置分为左数组以及右数组，再将左数组以及右数组从中间位置分别分为两个数组。以此递归，直至数组中只有一个元素。
&emsp;&emsp;并：将拆分的每个左右数组合并为一个数组，并使之有序。合并完所有的子数组后，原数组有序。
{% img mergesort /images/sort/merge.gif %}
归并排序的java实现:
```java
public int[] sort(int[] array){
	return merge(array,0,array.length-1);
}
//array:原始数组
//first:当前数组索引开始值
//last:当前数组索引结束值
private int[] merge(int[] array,int first,int last){
	if(first==last){    //数组只有一个元素
		return new int[]{array[first]};     //返回当前元素
	}
	int mid = first+((last-first)>>1);  //获取中间位置索引，拆分数组
	//归
	int[] leftArray = merge(array,first,mid);   //左数组
	int[] rightArray = merge(array,mid+1,last);     //右数组
	//并
	int[] newArray = new int[leftArray.length+rightArray.length];   //左数组与右数组合并成新数组
	int m=0,i=0,j=0;    //m:新数组中放置元素的位置；i:新数组中放置了左数组中元素的个数；j:新数组中放置了右数组中元素的个数
	while(leftArray.length>i && rightArray.length>j){   //左右数组中都有元素未放置在新数组中时
		newArray[m++] = leftArray[i]<rightArray[j]?leftArray[i++]:rightArray[j++];
	}
	while(leftArray.length>i){  //仅有右数组中有元素未放置在新数组中时
		newArray[m++] = leftArray[i++];
	}
	while(rightArray.length>j){ //仅有左数组中有元素未放置在新数组中时
		newArray[m++] = rightArray[j++];
	}

	return newArray;
}
```
## 堆排序
`时间复杂度 平均o(nlogn),最差o(nlogn)`
`空间复杂度 o(1)`
&emsp;&emsp;堆排序是一种树型选择排序，将数组看成一个完全二叉树的顺序存储结构，其标准是，二叉树的父节点的值需大于其孩子节点的值，左孩子节点的位置下标为父节点的位置×2+1，右孩子节点的位置下标为父节点位置×2+2。
&emsp;&emsp;堆排序的过程为建堆和寻找最大顶堆的过程。
建堆：
&emsp;&emsp;对于每个节点，判断其与左右孩子节点的大小，将最大的节点与父节点进行交换，若存在交换，则将交换位置的节点再次建堆，直至该分支满足父节点值大于孩子节点值的条件。
寻找大顶堆：
&emsp;&emsp;大顶堆即数组中最大的数在该树的根节点。对每个节点进行建堆，则最终根节点即是最大的元素，将根节点的元素与最后一个元素交换位置，再将剩余的元素继续依次建堆，寻找第二大的元素，直至数组有序。
{% img heapsort /images/sort/heap.gif %}
堆排序的java实现:
```java
public int[] sort(int[] array){
	return buildMaxHeap(array,array.length);
}
/**
 * 找大顶堆
 * @param array
 * @param size
 * @return
 */
public int[] buildMaxHeap(int[] array,int size){
	if(size==1){
		return array;
	}
	for(int i=size-1;i>=0;i--){//从堆的底部进行建堆
		array = heap(array,i,size);
	}
	//最大数在堆的顶部
	int temp = array[0];
	array[0] = array[size-1];
	array[size-1] = temp;
	//交换顶部与最底部的值
	array = buildMaxHeap(array,size-1);//去掉最底部的值，对剩下的数组找大顶堆
	return array;
}
/**
 * 建堆
 * @param array
 * @param currentIndex，进行比较的节点位置
 * @param size，需排序的数组长度
 * @return
 */
public int[] heap(int[] array,int currentIndex,int size){
	if(currentIndex>size-1){
		return array;
	}
	int left = 2*currentIndex+1;//该节点的左节点位置
	int right = 2*currentIndex+2;//该节点的右节点位置
	int max = currentIndex;//顶部节点的位置
	if(left<size && array[left]>array[currentIndex]){
		max = left;//若左节点值大于顶部节点，
	}
	if(right<size && array[right]>array[max]){
		max = right;
	}
	if(max!=currentIndex){//左右节点有大于顶部节点的
		int temp = array[currentIndex];
		array[currentIndex] = array[max];
		array[max] = temp;//交换两者位置
		array = heap(array,max,size);//对交换的节点进行建堆
	}
	return array;
}
```
## 基数排序
`时间复杂度	平均:o(n×k),最差:o(n×k)`
`空间复杂度	o(n+k)`
基数排序的时间复杂度与空间复杂度都与基数k有关，k的选取依赖于比较的元素的最高位数。基数排序是典型的用空间换时间的思想，将数组中元素从低位到高位，先按照元素的最低位大小进行排序，将排序后的元素保存在开辟的新的空间中，再将该序列按照第二位大小排序，再将排序后的元素保存在新的空间中，直至比较到最高位大小进行排序，此时该序列已有序。
我们将开辟的新空间成为桶，共有10个桶，分别对应10个位置，每个位置可存放不超过序列长度的元素。
{% img radixsort /images/sort/radix.gif %}
基数排序的java实现:
```java
public int[] sort(int[] array,int n){
	int len = array.length;
	int[][] bucket = new int[10][len];//10个桶,[0,1,2,3,4,5,6,7,8,9]
	int[] count = new int[len];//存储每个桶中元素的个数
	int[] arrayTemp = new int[len];
	int i=1;//当前比较的位数
	while(i<=n){    //从低位比较到高位
		for(int a:array){
			int num = getnum(a,i);//当前位对应的数
			bucket[num][count[num]] = a;//放入桶中
			count[num]++;
		}
		int index=0;
		for(int m=0;m<bucket.length;m++){
			if(count[m]==0){
				continue;
			}
			for(int k=0;k<count[m];k++){
				array[index] = bucket[m][k];
				index++;
			}
		}
		count = new int[len];
		i++;
	}
	return array;
}

public int getnum(int num,int n){
	int number = (int) (num/(Math.pow(10,n-1))%10);
	return number;
}
```
