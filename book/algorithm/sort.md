# 排序算法

### 排序算法分类

十种常见排序算法可以分为两大类：

- **比较类排序：** 通过比较来决定元素间的相对次序，由于其时间复杂度不能突破O(nlogn)，因此也称为非线性时间比较类排序。
- **非比较类排序：** 不通过比较来决定元素间的相对次序，它可以突破基于比较排序的时间下界，以线性时间运行，因此也称为线性时间非比较类排序。


![排序算法分类](../assets/sort.png)

### 算法复杂度

![算法复杂度](../assets/sort2.png)

### 相关概念

- **稳定：** 如果a原本在b前面，而a=b，排序之后a仍然在b的前面。
- **不稳定：** 如果a原本在b的前面，而a=b，排序之后 a 可能会出现在 b 的后面。
- **时间复杂度：** 对排序数据的总的操作次数。反映当n变化时，操作次数呈现什么规律。
- **空间复杂度：** 是指算法在计算机


### 快速排序（Quick Sort）

快速排序的基本思想：通过一趟排序将待排记录分隔成独立的两部分，其中一部分记录的关键字均比另一部分的关键字小，则可分别对这两部分记录继续进行排序，以达到整个序列有序。

快速排序使用分治法来把一个串（list）分为两个子串（sub-lists）。具体算法描述如下：

- 从数列中挑出一个元素，称为 “基准”（pivot）；
- 重新排序数列，所有元素比基准值小的摆放在基准前面，所有元素比基准值大的摆在基准的后面（相同的数可以到任一边）。在这个分区退出之后，该基准就处于数列的中间位置。这个称为分区（partition）操作；
- 递归地（recursive）把小于基准值元素的子数列和大于基准值元素的子数列排序。

```js
function quickSort(arr) {
	if(!Array.isArray(arr)) {
		throw new TypeError('not a array!');
	}

	if (arr.length <= 1) {
		return arr;
	}

	const left = [];
	const right = [];
	const pivot = arr.shift();

	for(let i = 0; i < arr.length; i++) {
		if(arr[i] < pivot) {
			left.push(arr[i]);
		} else {
			right.push(arr[i]);
		}
	}
	return quickSort(left).concat([pivot], quickSort(right));
}

quickSort([3,5,8,1,6]);
```
### 冒泡排序（Bubble Sort）

冒泡排序是一种简单的排序算法。它重复地走访过要排序的数列，一次比较两个元素，如果它们的顺序错误就把它们交换过来。走访数列的工作是重复地进行直到没有再需要交换，也就是说该数列已经排序完成。这个算法的名字由来是因为越小的元素会经由交换慢慢“浮”到数列的顶端。

- 比较相邻的元素。如果第一个比第二个大，就交换它们两个；
- 对每一对相邻元素作同样的工作，从开始第一对到结尾的最后一对，这样在最后的元素应该会是最大的数；
- 针对所有的元素重复以上的步骤，除了最后一个；
- 重复步骤1~3，直到排序完成。
- 1.2 动图演示

```js
function BubbleSort(arr) {
	const len = arr.length;

	for (let j = 1; j < len; j++) {
		for (let i = 1; i < len; i++) {
			let current = arr[i];
			if (arr[i-1] > arr[i]) {
				arr[i] = arr[i-1];
				arr[i-1] = current;
			}
		}
	}

	return arr;
}
```


### 插入排序（Insertion Sort）

插入排序（Insertion-Sort）的算法描述是一种简单直观的排序算法。它的工作原理是通过构建有序序列，对于未排序数据，在已排序序列中从后向前扫描，找到相应位置并插入。

一般来说，插入排序都采用in-place在数组上实现。具体算法描述如下：

- 从第一个元素开始，该元素可以认为已经被排序；
- 取出下一个元素，在已经排序的元素序列中从后向前扫描；
- 如果该元素（已排序）大于新元素，将该元素移到下一位置；
- 重复步骤3，直到找到已排序的元素小于或者等于新元素的位置；
- 将新元素插入到该位置后；
- 重复步骤2~5。

```js
function InsertionSort(arr) {
	let len = arr.length;
	let preIndex;
	let current;

	for (let i = 1; i < arr.length; i++) {
		preIndex = i;
		current = arr[i];
		while (arr[preIndex] < arr[preIndex-1] && preIndex > 0) {
			arr[preIndex] = arr[preIndex-1];
			arr[preIndex-1] = current;
			preIndex--;
		}
	}

	return arr;
}
```
