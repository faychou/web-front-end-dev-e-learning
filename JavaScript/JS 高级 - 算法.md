# 算法
「 算法 」： 算法是指解题方案的准确而完整的描述，是一系列解决问腿的清晰指令，算法代表着用系统的方法描述解决问题的策略机制。

一个算法的优劣可以用空间复杂度和时间复杂度来衡量。

* 「 时间复杂度 」：评估执行程序所需的时间。可以估算出程序对处理器的使用程度。

* 「 空间复杂度 」：评估执行程序所需的存储空间。可以估算出程序对计算机内存的使用程度。

「 时间频度 」： 一个算法中的语句执行次数称为语句频度或时间频度。

## 案例
### 数组全排列：
> 有一个数组[1,2,3,4]，请实现算法，得到这个数组的全排列的数组，如[2,1,3,4]，[2,1,4,3]。

实现思路：从“开始元素”起，每个元素都和开始元素进行交换；不断缩小范围，最后输出这种排列。暴力法的时间复杂度是 ，递归实现的时间复杂度是 

如何去重？去重的全排列就是从第一个数字起每个数分别与它后面非重复出现的数字交换。对于有重复元素的数组，例如：[1, 2, 2]，应该剔除重复的情况。每次只需要检查arr[start, i)中是不是有和arr[i]相同的元素，有的话，说明之前已经输出过了，不需要考虑。

``` js
const swap = (arr, i, j) => {
  let tmp = arr[i]
  arr[i] = arr[j]
  arr[j] = tmp
}

const permutation = arr => {
  const _permutation = (arr, start) => {
    if(start === arr.length){
      console.log(arr)
      return
    }
    for(let i = start; i < arr.length; ++i){
      // 全排列：去重操作
      if(arr.slice(start, i).indexOf(arr[i]) !== -1){
        continue
      }
      swap(arr, i, start) // 和开始元素进行交换
      _permutation(arr, start + 1)
      swap(arr, i, start) // 恢复数组
    }
    return 
  }
  return _permutation(arr, 0)
}

permutation([1, 2, 2])
console.log("**********")
permutation([1, 2, 3, 4])
```

### 背包问题
> 现在有一个背包，容量为m，然后有n个货物，重量分别为w1,w2,w3...wn，每个货物的价值是v1,v2,v3...vn，w和v没有任何关系，请求背包能装下的最大价值。

