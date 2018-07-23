<!-- MarkdownTOC -->

- [算法](#算法)
    + [2 sum问题](#2-sum问题)
    + [rand5 生成 rand7](#rand5-生成-rand7)
    + [堆排序](#堆排序)
    + [快速排序](#快速排序)
    + [归并排序](#归并排序)

<!-- /MarkdownTOC -->

# 算法

## 2 sum问题

参考博客：https://www.cnblogs.com/TenosDoIt/p/3647244.html

## rand5 生成 rand7

给你一个能生成1到5随机数的函数，用它写一个函数生成1到7的随机数，( 即，使用函数 rand5() 来实现函数 rand7() )

rand5()能生成的数：1, 2, 3, 4, 5

rand7()能生成的数：1, 2, 3, 4, 5, 6, 7

现在要构造一个函数，使其所生成的数中包含 (1, 2, 3, 4, 5, 6, 7)，并且每个数生成的概率相等

**f(x) = 5*(rand5()-1) + rand5()**

实现代码如下：
```
int x = ~(1<<31);//相当于 int x = Integer.MAX_VALUE;
while(x < 7) {
    x = 5*(rand5()-1) + rand5;
}
return x;
```

通用解法：a < b，f(x) = a*(randa() - 1) + randa();

## 堆排序

```
public class Exercise01 {
	/**
	 * (最大)堆的向下调整算法
	 * 注：数组实现的堆中，第N个节点的左孩子的索引值是(2N+1)，右孩子的索引是(2N+2)。 其中，N为数组下标索引值，如数组中第1个数对应的N为0。
	 * @param a 待排序的数组
	 * @param start 被下调节点的起始位置(一般为0，表示从第1个开始)
	 * @param end 截至范围(一般为数组中最后一个元素的索引)
	 */
	public static void maxHeapDown(int[] a, int start, int end) {
		int c = start; // 当前(current)节点的位置
		int l = 2 * c + 1; // 左(left)孩子的位置
		int temp = a[c]; // 当前(current)节点的大小

		for (; l <= end; c = l, l = 2 * l + 1) {
			// "l"是左孩子，"l+1"是右孩子
			if (l < end && a[l] < a[l + 1])
				l++; // 左右两孩子中选择较大者，即m_heap[l+1]
			if (temp >= a[l])
				break; // 调整结束
			else { // 交换值
				a[c] = a[l];
				a[l] = temp;
			}
		}
	}
	
    /**
     * 堆排序
     * @param a 待排序的数组
     * @param n 数组长度
     */
	public static void heapSortAsc(int[] a, int n) {
		

		// 从(n/2-1) --> 0逐次遍历。遍历之后，得到的数组实际上是一个(最大)二叉堆。
		for (int i = n / 2 - 1; i >= 0; i--)
			maxHeapDown(a, i, n - 1);

		// 从最后一个元素开始对序列进行调整，不断的缩小调整的范围直到第一个元素
		for (int i = n - 1; i > 0; i--) {
			// 交换a[0]和a[i]。交换后，a[i]是a[0...i]中最大的
			int temp = a[0];
			a[0] = a[i];
			a[i] = temp;
			// 调整a[0...i-1]，使得a[0...i-1]仍然是一个最大堆
			// 即，保证a[i-1]是a[0...i-1]中的最大值
			maxHeapDown(a, 0, i - 1);
		}
	}
	public static void main(String[] args) {
		int[] a = {7, 9, 8, 2, 5, 0, 3, 4};
		heapSortAsc(a, a.length);
		for(int temp: a) {
			System.out.print(temp+" ");
		}
	}
}
```

## 快速排序

```
public class Exercise03 {
	//快速排序
	public static void quickSort(int[] a, int start, int end) {
		
		if(start > end) {
			return;
		}
		int target = a[start];
		int i = start;
		int j = end;
		while(i != j) {
			while(i < j && a[j] >= target) {
				j--;
			}
			a[i] = a[j];
			while(i < j && a[i] <= target) {
				i++;
			}
			a[j] = a[i];
		}
		a[i] = target;
	        quickSort(a, start, i-1);
		quickSort(a, i+1, end);
	}
	public static void main(String[] args) {
		int[] a = {4, 3, 1, 5, 8, 6, 9, 0, 2, 7};
		quickSort(a, 0, a.length-1);
		for(int i = 0; i < a.length; i++) {
			System.out.print(a[i] + " ");
		}
	}
}
```

## 归并排序

```
/*
 * 归并排序：
 * 先把数组一分为2，数组不一定是有序的，然后不停分解，当各个子数组中只剩一个元素时，就有序了
 * 把相邻的两个有序子数组依次合并
 */
public class Exercise04 {
	//将两个有序数组a[first, ..., mid]和a[mid+1, ..., last]合并
	public static void mergeArray(int[] a, int first, int mid, int last, int[] temp) {
		int i = first;
		int j = mid + 1;
		int k = 0;
		while(i <= mid  && j <= last) {
			if(a[i] <= a[j]) {
				temp[k++] = a[i++];
			} else {
				temp[k++] = a[j++];
			}
		}
		while(i <= mid) {
			temp[k++] = a[i++];
		}
		while(j <= last) {
			temp[k++] = a[j++];
		}
		for(int n = 0; n < k; n++) {
			a[first + n] = temp[n];
		}
	}
	//
	public static void mergeSort(int[] a, int first, int last, int[] temp) {
		if(first < last) {
			int mid = (first + last)/2;
			mergeSort(a, first, mid, temp);//左边有序
			mergeSort(a, mid + 1, last, temp);//右边有序
			mergeArray(a, first, mid, last, temp);//将左右两边合并
		}
	}
	public static void main(String[] args) {
		int[] a = {3, 2, 5, 6, 1, 8};
		int[] temp = new int[a.length];
		mergeSort(a, 0, a.length-1, temp);
		for(int result: a) {
			System.out.print(result + " ");
		}
	}
}
```
