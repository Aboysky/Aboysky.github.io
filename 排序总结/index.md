# 排序算法总结


[TOC]

### 排序算法

#### 快速排序

- 思路：

  `先寻找一个坐标，然后将小于坐标的都放到坐标的左边，大于等于坐标的都放到坐标的右边，如此将原数组分成左右两块数组，对每个子数组执行上述的操作，直到子数组长度为1或者为空。（递归）`

- 代码：

  ```Java
  public static void quick_sort(int[] nums,int start,int end) {
      // 递归终止条件
      if (start >= end) {
          return;
      }
      int point = nums[start];
      int l = start - 1,r = end + 1;
      while (l < r) {
          do l++; while (l < r && nums[l] < point);
          do r--; while (r > l && nums[r] >= point);
          // 因为当l++ 到等于r后，后续r--会至少执行一次，导致r < l,但是此时不应该发生交换
          if (l < r){
              int temp = nums[l];
              nums[l] = nums[r];
              nums[r] = temp;
          }
      }
      quick_sort(nums,start,r);
      quick_sort(nums,r+1,end);
  }
  ```

- 时间复杂度：

  - 最坏时间复杂度： `O(n^2)`

  - 平均时间复杂度：`O(n·logn)`
    
  - 最优时间复杂度：`O(n·logn)`

#### 冒泡排序

- 思路：

  - `比较相邻的元素。如果第一个比第二个大，就交换他们两个。`
  - `对每一对相邻元素作同样的工作，从开始第一对到结尾的最后一对。这步做完后，最后的元素会是最大的数。`
  - `针对所有的元素重复以上的步骤，除了最后一个。`
  - `持续每次对越来越少的元素重复上面的步骤，直到没有任何一对数字需要比较。`

- 算法：

  ``` java
  private int[] bubbleSort(int[] array) {
      int temp;
      for (int i = 0; i < array.length - 1; i++) {
          boolean Flag = false; // 是否发生交换。没有交换，提前跳出外层循环
          for (int j = 0; j < array.length - 1 - i; j++) {
              if (array[j] > array[j + 1]) {
                  temp = array[j];
                  array[j] = array[j + 1];
                  array[j + 1] = temp;
                  Flag = true;
              }
          }
          if (!Flag)
          {
              break;
          }
      }
      return array;
  }
  ```
  
- 时间复杂度：

  - 最坏时间复杂度： `O(n^2)`

  - 平均时间复杂度：`O(n^2)`
    
  - 最优时间复杂度：`O(n)`

#### 插入排序

- 思路：

  - `在数组前部维护一个有序区间，初始时有序区间的长度为1，而后从index = 1开始逐个添加进入有序区间，就和抓牌时抓一个插入一个一样。因为少了很多比较次数，但是相比之下，他每次往有序数组中插入时都需要移动一部分元素`

- 算法：

  ``` java
  /**
       * 插入排序
       * @param nums
       */
  public static void insertionSort(int[] nums) {
      int len = nums.length;
      for (int i = 1; i < len; i++) {
          int temp = nums[i];
          int pointer = i;
          for (int j = i - 1; j >= 0; j--) {
              if (nums[j] >= temp) {
                  nums[j + 1] = nums[j];
                  pointer = j;
              }
          }
          nums[pointer] = temp;
      }
  }
  ```

- 时间复杂度：

  - 最坏时间复杂度： `O(n^2)`
  - 平均时间复杂度：`O(n^2)`  
  - 最优时间复杂度：`O(n)`
  
  

#### 选择排序

- 思路：

  - `选择，顾名思义，每次选择待排序数组中的剩余的最大值（最小值）移动到有序序列的最前（最后）。思考一下，我们需要在代码中维护两个循环变量，一个是下一个极值放置位置的索引，另一个就是循环查找极值的循环。`

- 算法：

  ``` java
  /**
       * 选择排序
       * @param nums
       */
  public static void maopao_sort(int[] nums) {
      int n = nums.length;
  
      for (int i = n - 1; i >= 0; i--) {
          int nextIndex = 0;
          for (int j = 0; j <= i;j++) {
              if (nums[j] > nums[nextIndex]) {
                  nextIndex = j;
              }
          }
          int temp = nums[i];
          nums[i] = nums[nextIndex];
          nums[nextIndex] = temp;
      }
  }
  ```

- 时间复杂度：

  - 最坏时间复杂度： `O(n^2)`
  - 平均时间复杂度：`O(n^2)`  
  - 最优时间复杂度：`O(n^2)`

  

#### 堆排序

- 思路：

  - `指利用堆这种数据结构所设计的一种排序算法。堆是一个近似完全二叉树的结构，并同时满足堆的性质：即子节点的键值或索引总是小于（或者大于）它的父节点`
  - `整体上有两个步骤，第一个就是构建堆，将数组构建为一个大顶堆或者小顶堆`
  - `第二步，在数组尾部(前部)维护一个有序序列，每次将堆顶元素移到有序序列的最前面，而后将原本在此地的值移入堆顶，然后重新构建堆`

- 算法：

  ``` java
  /**
   * 堆排序
   * @Classname HeapSort
   * @Description TODO
   * @Date 2021/4/22 21:21
   * @Created by Huan
   */
  public class HeapSort {
  
      /**
       * 堆排序的主要入口方法，共两步。
       */
      public static void sort(int[] arr) {
          /*
           *  第一步：将数组堆化
           *  beginIndex = 第一个非叶子节点。
           *  从第一个非叶子节点开始即可。无需从最后一个叶子节点开始。
           *  叶子节点可以看作已符合堆要求的节点，根节点就是它自己且自己以下值为最大。
           */
          int len = arr.length - 1;
          int beginIndex = (arr.length >> 1)- 1;
          for (int i = beginIndex; i >= 0; i--) {
              maxHeapify(arr,i, len);
          }
          /*
           * 第二步：对堆化数据排序
           * 每次都是移出最顶层的根节点A[0]，与最尾部节点位置调换，同时遍历长度 - 1。
           * 然后从新整理被换到根节点的末尾元素，使其符合堆的特性。
           * 直至未排序的堆长度为 0。
           */
          for (int i = len; i > 0; i--) {
              swap(arr,0, i);
              maxHeapify(arr,0, i - 1);
          }
      }
  
      private static void swap(int[] arr, int i, int j) {
          int temp = arr[i];
          arr[i] = arr[j];
          arr[j] = temp;
      }
  
      /**
       * 调整索引为 index 处的数据，使其符合堆的特性。
       *
       * @param index 需要堆化处理的数据的索引
       * @param len 未排序的堆（数组）的长度
       */
      private static void maxHeapify(int[] arr,int index, int len) {
          int li = (index << 1) + 1; // 左子节点索引
          int ri = li + 1;           // 右子节点索引
          int cMax = li;             // 子节点值最大索引，默认左子节点。
          if (li > len) return;      // 左子节点索引超出计算范围，直接返回。
          if (ri <= len && arr[ri] > arr[li]) // 先判断左右子节点，哪个较大。
              cMax = ri;
          if (arr[cMax] > arr[index]) {
              swap(arr,cMax, index);      // 如果父节点被子节点调换，
              maxHeapify(arr,cMax, len);  // 则需要继续判断换下后的父节点是否符合堆的特性。
          }
      }
  
      /**
       * 测试用例
       *
       * 输出：
       * [0, 0, 0, 1, 1, 1, 2, 2, 2, 3, 3, 3, 4, 4, 4, 5, 5, 5, 6, 6, 6, 7, 7, 7, 8, 8, 8, 9, 9, 9]
       */
      public static void main(String[] args) {
          int[] arr = new int[] {3, 5, 3, 0, 8, 6, 1, 5, 8, 6, 2, 4, 9, 4, 7, 0, 1, 8, 9, 7, 3, 1, 2, 5, 9, 7, 4, 0, 2, 6};
          sort(arr);
          System.out.println(Arrays.toString(arr));
      }
  }
  ```

- 时间复杂度：

  - 最坏时间复杂度： `O(n·logn)`
  - 平均时间复杂度：`O(n·logn)`  
  - 最优时间复杂度：`O(n·logn)`


#### 希尔排序

- 思路：

  - `在插入排序中，如果某个数开始所处位置和他排序后所处位置相差过大，则会导致非常多的交换操作，而希尔排序则是利用这一点，在开始的时候使用大步长，尽量的使nums[i]的位置尽早的在排序后所处位置附近。理论证明插入排序中占主要时间的是交换操作。`
  - `由希尔排序的含义可知，`
  
- 算法：

  ``` java
  public static void shellSort(int[] arr) {
          int length = arr.length;
          int temp;
      	// 步长循环
          for (int step = length / 2; step >= 1; step /= 2) {
              // 插入排序
              for (int i = step; i < length; i++) {
                  temp = arr[i];
                  int j = i - step;
                  while (j >= 0 && arr[j] > temp) {
                      arr[j + step] = arr[j];
                      j -= step;
                  }
                  arr[j + step] = temp;
              }
          }
      }
  ```
  
- 时间复杂度：

  - 最坏时间复杂度： ``
  - 平均时间复杂度：`根据步长序列的选择不同而不同,已知最好为O(n·log^2·n)`  
  - 最优时间复杂度：`O(n)`


#### 归并排序

- 思路：

  - `递归的将当前序列分为两半`
  - `保持元素顺序为基础将上一步得到的子序列合二为一`
  
- 算法：

  ``` java
  /**
   * 归并排序
   */
  static void merge_sort_recursive(int[] arr, int[] result, int start, int end) {
  	if (start >= end)
  		return;
  	int len = end - start, mid = (len >> 1) + start;
  	int start1 = start, end1 = mid;
  	int start2 = mid + 1, end2 = end;
  	merge_sort_recursive(arr, result, start1, end1);
  	merge_sort_recursive(arr, result, start2, end2);
  	int k = start;
  	while (start1 <= end1 && start2 <= end2)
  		result[k++] = arr[start1] < arr[start2] ? arr[start1++] : arr[start2++];
  	while (start1 <= end1)
  		result[k++] = arr[start1++];
  	while (start2 <= end2)
  		result[k++] = arr[start2++];
  	for (k = start; k <= end; k++)
  		arr[k] = result[k];
  }
  public static void merge_sort(int[] arr) {
  	int len = arr.length;
  	int[] result = new int[len];
  	merge_sort_recursive(arr, result, 0, len - 1);
  }
  ```
  
- 时间复杂度：

  - 最坏时间复杂度： `O(n·logn)`
  - 平均时间复杂度：`O(n·logn)`  
  - 最优时间复杂度：`O(n·logn)`


