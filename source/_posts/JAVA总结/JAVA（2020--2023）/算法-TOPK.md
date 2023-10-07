---
categories:
  - JAVA总结
  - JAVA（2020--2023）
---
# d

[数组维护堆，堆排序](https://blog.csdn.net/qq_55624813/article/details/121316293)



大顶堆，小顶堆（完全二叉树）

TOPK（最小的K个数则使用大顶堆，剩的最终的堆内容为最小的TOPK）

> 1.建堆 （从非叶子节点开始，直到顶点）
>
> ```java
> // 实际是 ((arr.length - 1) - 1) / 2
> for (int i = (arr.length - 2) / 2; i >= 0; i--) {
>     // 每个非叶子结点都需要做下沉操作，从最后一个开始往前遍历
>     downAdjust(arr, i);
> }
> ```
>
> 2.调整 （构建的大顶堆，剔除最大的父节点，然后下沉节点）
>
> ```java
> /**
>  * 最小的K个数
>  * @param arr
>  * @param heap
>  * @param k
>  */
> public void topKSort(int[] arr, int[] heap, int k){
>     for(int i = k; i< arr.length; i++){
> 
>         int current = arr[i];
>         if(current > heap[0]){
>             continue;
>         }
>         //调整堆
>         heap[0] = current;
>         downAdjustTopK(heap, 0,5);
>         Arrays.stream(heap).forEach(t->System.out.print(t+","));
>         System.out.println();
>     }
> }
>  /**
>      * 建堆，调整
>      * @param arr 原始数据数组
>      * @param currentIndex 调整的节点索引号
>      * @param k 堆大小
>      */
>     public void downAdjustTopK(int[] arr, int currentIndex, int k) {
>         int tmp = arr[currentIndex];
>         int childIndex = 2 * currentIndex + 1;
>         while (childIndex < k) {
>             // 判断是左孩子还是右孩子
>             if (childIndex + 1 < k && arr[childIndex] < arr[childIndex+1]) {
>                 childIndex++;
>             }
>             // 若发现不满足循环条件即可跳出
>             if (tmp >= arr[childIndex]) {
>                 break;
>             }
>             // 单向赋值，无交换
>             arr[currentIndex] = arr[childIndex];
>             currentIndex = childIndex;
>             childIndex = 2 * currentIndex + 1;
>         }
>         arr[currentIndex] = tmp;
>     }
> 
> ```

