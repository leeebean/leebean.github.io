---
title: Java常见算法
date: 2020-02-25 11:30:00
## updated: 2018-01-05 00:00:00
tags: Java
categories: 算法
keywords: [Java,算法]
description: java常见算法
top_img:
comments: true
cover:  https://i.loli.net/2020/12/02/oy1xXtevMQnWgHz.jpg
toc:
toc_number:
auto_open:
copyright:
mathjax:
katex:
aplayer:
highlight_shrink:
---
<iframe src = "https://algorithm-visualizer.org/backtracking/hamiltonean-cycles"
        style= "width:100%;height:500px;"
></iframe>

## 1.排序算法
### 冒泡排序

```
        //Bubble Sort
        int length = arr.length;
        boolean flag;
        for (int i = length - 1; i > 0; i--) {
            flag = true;
            for (int j = 0; j < i; j++) {
                if (arr[j] > arr[j + 1]) {
                    arr[j] = arr[j + 1] + arr[j];
                    arr[j + 1] = arr[j] - arr[j + 1];
                    arr[j] = arr[j] - arr[j + 1];
                    flag = false;
                }
            }
            if (flag) {
                break;
            }
        }
```


### 梳排序

```
        //Comb Sort
        int length = arr.length;
        int gap = length;
        float shrink = 1.3f;
        boolean swapped;
        do {
            swapped = false;
            gap = (int) Math.floor(gap / shrink);
            if (gap < 1) {
                gap = 1;
            }
            for (int i = 0; i + gap < length; i++) {
                if (arr[i] > arr[i + gap]) {
                    arr[i] = arr[i] + arr[i + gap];
                    arr[i + gap] = arr[i] - arr[i + gap];
                    arr[i] = arr[i] - arr[i + gap];
                    swapped = true;
                }
            }
        } while (gap != 1 || swapped);
```

### 计数排序

![计数排序](https://upload-images.jianshu.io/upload_images/11765489-06050cebd6a5bbf8.gif)
[动图来自@_Cappuccino_](https://www.jianshu.com/u/2529ca0530d6)

```
    //Counting Sort
    private static void countingSort(Integer[] arr, int max) {
        int length = arr.length;
        int[] counts = new int[max + 1];
        int[] sortedArr = new int[length];
        // store counts of each number
        for (int i = 0; i < length; i++) {
            counts[arr[i]]++;
        }
        // calculate the prefix sums
        for (int i = 1; i <= max; i++) {
            counts[i] += counts[i - 1];
        }
        // sort
        for (int i = 0; i < length; i++) {
            sortedArr[counts[arr[i]]-- - 1] = arr[i];
        }
    }
```