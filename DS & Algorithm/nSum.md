# 2Sum
[Leetcode 1 ](https://leetcode.cn/problems/two-sum/)
哈希思路：遍历每一个数，找是否有一个数等于 target - num
双指针思路：对数组进行排序，使用头尾指针搜索
```js
var twoSum = function(nums, target) {
    const map = new Map() // val -> index
    for(let i = 0; i < nums.length; i++){
        if(map.has(target - nums[i])){
            return [map.get(target - nums[i]), i]
        }
        map.set(nums[i], i)
    }
    return null
};
```

## 2Sum的泛化
假设数组中可能有重复值，且找到的二元组可能有多个，返回不重复的二元组
沿用双指针思路，先对数组排序，使用头尾指针搜索，每次搜索到一个符合条件的二元组加入res数组，同时指针跳过重复的数，继续搜索

> [!Danger] 循环中循环改变循环指针
> 在循环中，使用循环改变循环指针一定要添加判断，因为走不到外部的循环判断

```js
var twoSum = function(nums, target) {
    let left = 0, right = nums.length - 1
    nums.sort((a, b) => a - b)
    const res = []
    while(left < right){
	    const sum = nums[left] + nums[right]
	    let left_val = nums[left], right_val = nums[right]
	    if(sum < target){ // left++
		    while(left < right && nums[left] === left_val){ // 跳过重复值
			    left++
		    }
	    } else if(sum > target){ // right--
		    while(eft < right && nums[right] === right_val){ // 跳过重复值
			    right--
		    }
	    }else { // find target
		    res.push([left_val, right_val])
		    while(eft < right && nums[left] === left_val){ // 跳过重复值
			    left++
		    }
		    while(eft < right && nums[right] === right_val){ // 跳过重复值
			    right--
		    }
	    }
    }
    return res
};
```

# 3Sum
[Leetcode 15](https://leetcode.cn/problems/3sum/) 返回数组中三个数和为target的三元组，答案可能有多个，返回的三元组值不能重复
思路：仍然先对数组排序，然后选择三元组中的第一个数 num，再在剩余的数组中寻找双元组 2sum = target - num（使用上述的2Sum算法，二元组一定不是重复的），遍历其他可能的第一个数 num（遍历时跳过重复值），重复以上操作

```js
var threeSum = function(nums) {
    nums.sort((a, b) => a - b)
    const target = 0
    const res = []
    for(let i = 0; i < nums.length; ){
        let num = nums[i]
        const twoTuple = twoSum(i + 1, nums.length - 1, nums, target - num)
        if(twoSum.length !== 0){
            for(let t of twoTuple){
                res.push([num].concat(t))
            }
        }
        while(i < nums.length && nums[i] === num) i++
    }
    return res
};

function twoSum(left, right, nums, target){
    const res = []
    while(left < right){
	    // 省略
    }
    return res
}
```

# 4Sum
1. 对数组排序
2. 先固定第一位数 num1，再对剩余的序列使用 3Sum 查找三元组和为 target - num1
3. 在三元组中，先固定第二位数num2，再对剩余的序列使用 2Sum 查找二元组和为 target - num1 - num3

# nSum
定义递归函数` nSum(nums, n, start, target)` 返回和为 target 的 n 元祖
Base Case：n = 2 计算二元组时，调用 2Sum 函数计算值
计算 n 元祖时，

```js
function nSum(nums, n, start, target){
	const len = nums.length
	const res = []
	if(n < 2 || len < n) return res
	if(n === 2) return twoSum(start, len - 1, nums, target) // base case
	// n > 2 的元祖
	for(let i = start; i < len; ){ 
		let num = nums[i] // 固定首位
		const tuples = nSum(nums, n - 1, i + 1, target - num) // 计算剩余位
		for(let t of tuples){
			res.push([num].concat(t))
		}
		while(i < len && nums[i] === num) i++
	}
	return res
}

function twoSum(left, right, nums, target){
    const res = []
    while(left < right){
	    let left_val = nums[left], right_val = nums[right]
        let sum = left_val + right_val
	    if(sum < target){ // left++
		    while(left < right && nums[left] === left_val){ 
			    left++
		    }
	    } else if(sum > target){ // right--
		    while(left < right && nums[right] === right_val){ 
			    right--
		    }
	    }else { // find target
		    res.push([left_val, right_val])
		    while(left < right && nums[left] === left_val){ 
			    left++
		    }
		    while(left < right && nums[right] === right_val){
			    right--
		    }
	    }
    }
    return res
}
```


