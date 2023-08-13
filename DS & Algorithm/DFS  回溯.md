# 回溯框架
回溯算法和 DFS 算法非常类似，本质上是一种**暴力穷举算法**，和 DFS 算法的细微差别是：回溯算法是在遍历「树枝」，DFS 算法是在遍历「节点」
在一个结点上，回溯算法要考虑：
1. 路径：已经做出的选择
2. 选择列表：当前可以做的选择
3. 结束条件：到达决策树底层，无法再做选择的条件

回溯框架 （注意前序位置和后序位置在 for 循环里面）
```python
result = []
def backtrack(路径, 选择列表):
    if 满足结束条件:
        result.add(路径)
        return
    
    for 选择 in 选择列表:
        做选择 
        backtrack(路径, 选择列表)
        撤销选择
```

多叉树的深度遍历（注意前序位置和后序位置在 for 循环外面）
```js
var traverse = function(root) {
	 // 前序位置需要的操作
    for (var i = 0; i < root.children.length; i++) {
        traverse(root.children[i]);
    }
     // 后序位置需要的操作
}
```


> [!Tip] 回溯的含义
> 回溯算法会像DFS一样，一路做选择直到达到递归树的底层（Base Case），然后撤回最后一个选择，回溯到上一层，做另一个选择，再达到底层；以此类推，逐层回溯，穷举所有选择

## 全排列
[Leetcode 46](https://leetcode.cn/problems/permutations/) 给定一个不含重复数字的数组 `nums` ，返回其所有可能的全排列
路径：已经选择的数字构成的排列
选择：当前还可以选的数字
结束条件：路径长度等于nums.length
```js
var permute = function(nums) {
    const res = []
    const track = [] // 路径
    const used = new Array(nums.length).fill(false) // 记录哪些数已经在路径中
   
    const backtrack = () => {
        if (track.length === nums.length) { // 结束条件
            res.push([...track]); 
            return;
        }
		// 每次都遍历全部结点，筛选出可以选择的结点
        for(let i = 0; i < nums.length; i++){ // 层
            if(used[i] === true){
                continue // 跳过
            }
            used[i] = true 
            track.push(nums[i]) //做选择
            backtrack(track, nums, used) // 深度递归
            used[i] = false
            track.pop() // 撤销选择
        }
    }
    backtrack()
    return res
};
```

backtrack 函数：可以抽象理解为遍历指针，关注在一个结点上可以做出的选择，for循环遍历这些选择，做选择，进入下一个结点

## N皇后
[Leetcode 51](https://leetcode.cn/problems/n-queens/) 将 n 个皇后放置在 n×n 的棋盘上，并且使皇后彼此之间不能相互攻击
路径：已经放置皇后所构成的路径
选择：不构成攻击的情况下可选择放置的位置
结束条件：放置完 n 个皇后
```js
var solveNQueens = function(n) {
    const res = []
    const board = new Array(n).fill(".").map(() => new Array(n).fill("."))
    
    const backtrack = (row) => {  // row记录已经放置皇后的行数
        if(row === n){ // 结束条件
            res.push(board.map(row => row.join("")))
        }
        for(let col = 0; col < n; col++){ // 选择行的某一列
            if(!isValid(col, row)){ // 放置不合法
                continue; // 选择下一列
            }
            board[row][col] = "Q" // 放置皇后
            backtrack(row+1); // 下一行
            board[row][col] = "."  // 撤销放置
        }
    }

    // 是否可以在 row 行 col 列放置皇后
    const isValid = (col, row) => {
        // 检查列
        for(let i = 0; i <= row; i++){
            if(board[i][col] === "Q") return false
        }
        // 检查左上
        for(let i = row-1, j = col - 1; i >= 0 && j >= 0; i--, j--){
            if(board[i][j] === "Q") return false
        }
        // 检查右上
        for(let i = row-1, j = col + 1; i >= 0 && j < n; i--, j++){
            if(board[i][j] === "Q") return false
        }
        return true
    }
    
    backtrack(0)
    return res
};
```

backtrack 函数：在一个结点（棋盘）上，对下一行的可以走的选择进行遍历，因此函数需要记录一个行索引参数

[Leetcode 52](https://leetcode.cn/problems/n-queens-ii/) 返回 n 皇后问题 不同的解决方案的数量
思路：避免空间开销，将res初始化为0，每找到一个方案就+1

# 排列、组合、子集问题
这些问题都是要从序列 `nums` 中以一定规则选取若干元素
- 排列必须返回长度等于 `nums` 的数组
- 组合和子集返回长度小于等于 `nums` 的数组

算法设计上的细节：
- 组合/子集的结果是没有顺序的，使用 `start` 变量保证元素 `nums[start]` 之后只会出现 `nums[start+1..]` 中的元素
- 排列的结果是有顺序的，需要额外使用 `used` 数组来标记哪些元素还可以被选择 

## 子集问题
集合是不考虑元素顺序的
### 无重不可复选
[Leetcode 78](https://leetcode.cn/problems/subsets/) 输入一个无重复元素的数组 `nums`，返回 `nums` 的所有子集
![[assets/DFS  回溯_image_1.png]]
路径：当前选择的元素，每个路径形成的结点都是一个子集
选择：在当前路径上，可以选择的下一个元素（集合没有顺序，为了去重只能选择后面的元素）
根据回溯树分析，第 n 层的结点就是大小为 n 的所有子集
```js
var subsets = function(nums) {
    const res = []
    const track = []

    // 记录开始位置
    const backtrack = (start) => {
        // 每次做完选择都更新子集
        res.push([...track])
        // 遍历当前可以做的选择
        for(let i = start; i < nums.length; i++){
            track.push(nums[i]) // 选择
            backtrack(i + 1) // 更新选择列表，遍历下一层
            track.pop(nums[i]) // 撤销选择
        }
    }
    backtrack(0)
    return res
};
```

### 可重不可复选
[Leetcode 90](https://leetcode.cn/problems/subsets-ii/) 数组 `nums`  其中可能包含重复元素，返回该数组所有可能的子集
如果按照之前的解法，结果中会出现重复的track，如 `[1,2]` `[1,2']` ，而子集/组合必须是不包含重复元素
剪枝：
![[assets/DFS  回溯_image_2.png]]
如果一个节点有多条值相同的树枝相邻，则只遍历第一条
```js
var subsetsWithDup = function(nums) {
    const res = []
    const track = []
    // 排序，让重复值放在一起
    nums.sort((a, b) => a - b) 
    
    const backtrack = (start) => {
        res.push([...track])
        for(let i = start; i < nums.length; i++){ // 每一层
            if(i > start && nums[i] === nums[i-1]){
                continue // 跳过本层的重复树枝
            }
            track.push(nums[i]) 
            backtrack(i + 1) // 下一层
            track.pop()
        }
    }
    backtrack(0)
    return res
};
```

## 组合问题
### 无重不可复选
[Leetcode 77](https://leetcode.cn/problems/combinations/) 返回范围 `[1, n]` 中所有可能的 k 个数的组合
组合和子集是一类问题：大小为 k 的组合就是大小为 k 的子集
```js
var combine = function(n, k) {
    const res = []
    const track = []
    const backtrack = (start) => {
        // 遍历到了第 k 层，收集当前节点的值，返回
        if(track.length === k) {
            res.push([...track])
            return 
        }
		
        for(let i = start; i <= n; i++){ // 遍历选择
            track.push(i) // 选择
            backtrack(i + 1) // 下一层，更新选择
            track.pop() // 撤销选择
        }
    }
    backtrack(1)
    return res
};
```

[Leetcode 216](https://leetcode.cn/problems/combination-sum-iii/) 类似问题，改一下 Base case 即可
### 可重不可复选
[Leetcode 40](https://leetcode.cn/problems/combination-sum-ii/)  给一个可能有重复值的数组`candidates` ，找出 `candidates` 中所有可以使数字和为 `target` 的组合
和子集问题类似，需要对同层的相同数字进行剪枝；同时如果当前计算和超过  `target` 也要剪枝
```js
var combinationSum2 = function(candidates, target) {
    const res = []
    const track = []
    let trackSum = 0
    candidates.sort((a, b) => a - b)

    const backtrack = (start) => {
        if(trackSum === target){
            res.push([...track])
        }
        // 超过目标和也要剪枝
        if (trackSum > target) {
            return;
        }
        for(let i = start; i < candidates.length; i++){
            // 同层相同元素，剪枝
            if(i > start && candidates[i] === candidates[i-1]){
                continue
            }
            track.push(candidates[i])
            trackSum += candidates[i] // 更新和
            backtrack(i+1)
            track.pop()
            trackSum -= candidates[i] // 更新和
        }
    }
    backtrack(0)
    return res
};
```

### 无重可复选
[Leetcode 39](https://leetcode.cn/problems/combination-sum/) 无重复元素的整数数组 `candidates` ，找出 `candidates` 中可以使数字和为目标数 target 的 所有不同组合，元素可以被选多次
在之前的组合问题中，为了避免选到重复元素，使用 `start + 1`  更新下一轮选择的索引，下一轮只能选择 `nums[start+1, ...]` 中的数，如果想让**当前元素**可以被重复使用，可以将索引改为：
```js
for (var i = start; i < nums.length; i++) {
        // 递归遍历下一层时，当前元素仍然可以使用
        backtrack(nums, i)
    }
```
![[assets/DFS  回溯_image_3.png]]

```js
var combinationSum = function(candidates, target) {
    const track = []
    const res = []
    let sum = 0

    const backtrack = (start) => {
        if(sum === target){
            res.push([...track])
            return 
        }
        if(sum > target){ // 及时剪枝，否则会无限递归
            return 
        }

        for(let i = start; i < candidates.length; i++){
            track.push(candidates[i])
            sum += candidates[i]
            backtrack(i) // 可重复使用树枝 i
            track.pop()
            sum -= candidates[i]
        }
    }

    backtrack(0)
    return res
};
```
## 排列问题
### 无重不可复选
全排列问题见 [[DFS  回溯#全排列]]

> [!NOTE] DFS
> 注意树的遍历是深度优先的，也就是虽然 for 循环写在 backtrack 前面，但实际上 for 循环是在底层都遍历完往回走时才结束的

![[assets/DFS  回溯_image_4.png]]

如果题目求只包含 k 个元素的排列，在前序结点位置的判断添加一层：
```js
if (track.length === k) {
        // 第 k 层节点的值就是大小为 k 的排列
        res.push([...track])
        return
}
```
### 可重不可复选
[Leetcode 47](https://leetcode.cn/problems/permutations-ii/) 包含重复数字的序列 `nums` ，按任意顺序返回所有不重复的全排列
和子集问题类似，需要对重复元素剪枝，但是需要注意的是和组合/子集不同，后者用 `start` 维护剩余元素（遍历的范围为 `nums[start...]` ) 已经在 `track` 中的元素不会对新的循环产生影响；但是排列的 for 循环每次都要遍历所有的元素，比如假设 `nums=[1,1,2]` `track=[1]` 时，递归遍历到下一层时，由于 `nums[1] == nums[0]`，使用 `nums[i] == nums[i-1]` 剪枝，第二个 1 会被跳过 ，也就获取不到 `[1,1,2]` 的排列结果了
要剪的枝是同层的相同元素，而不是同一深度叉的相同元素，深度优先遍历时，前一个相同元素已经被遍历过，而同层 for 循环时，相同元素还未被处理，可以添加一个判断条件区分这种情况：
```js
//  如果前面的相邻相等元素没有用过，则跳过
if(i > 0 && nums[i] === nums[i-1] && used[i-1] === false){ // 剪枝条件
	continue;
}
```
### 无重可复选
`nums` 数组中的元素无重复且可复选的情况下，会有哪些排列？
假设数组长度为n，排列的每个位置都可以选择这任意 n 个数，那么排列有 n^n 种
在原来排列的代码中，去除 `used` 数组的限制即可

# 集合划分
[Leetcode 698](https://leetcode.cn/problems/partition-to-k-equal-sum-subsets/) 给定一个整数数组  `nums` ，把这个数组分成 `k` 个非空子集，其总和都相等

> [!Tip] 划分集合
> 如果只需要划分成两个总和相等的集合，可以转换为背包问题（装或不装）
> [[动态规划#子集背包]]

将 n 个数字分配到 k 个桶里，"如何选择"，可以有两种视角：
1. 数字的视角，对于每个数字都要选择进入到 k 个桶中的某一个，时间复杂度为 `O(k^n)`
2. 桶的视角，对于每个桶，都需要遍历 `nums` 中的 n 个数字，然后选择是否将当前遍历到的数字装进自己这个桶里（装或不装），时间复杂度为` O(k*2^n)`

## 数字视角
在决策树的每一层，数字 `nums[i]` 要选择进入进入一个桶：
- 检查桶的容量是否可以是否可以装下数字，不行则选择下一个桶
- 放入桶中后进入下一层，选择下一个数字 `nums[i+1]` 入桶

结束条件是所有的数字都处理过
- 在回溯的最底层，有两种情况
	- 对于最后一个数字，也找到了合适的桶，进入下一层递归的结束条件判断中，返回 true
	- 对于某个数字，找不到合适的桶放了，循环完桶，该层递归函数直接返回 false
- 在结束条件中返回分配后的结果 true / false 
```js
var canPartitionKSubsets = function(nums, k) {
    if(k < nums) return false
    const sum = nums.reduce((a,b) => a+b)
    if(sum % k !== 0) return false
    // k 个桶，记录每个桶装的数字之和
    const bucket = new Array(k).fill(0)
    const target = sum / k
    
    // 穷举
    const backtrack = (index) => { // 数字索引
        // base case
        if(index === nums.length){ 
                return true
        }

        // 遍历每个桶，做选择
        for(let i = 0; i < bucket.length; i++){
            if(bucket[i] + nums[index] > target){
                continue // 装不下
            }
            bucket[i] += nums[index] // 选择一个数
            if (backtrack(index + 1)) {
                return true; // 如果找到了符合的情况，直接返回true，不要再回溯了
            }
            bucket[i] -= nums[index] // 撤销选择，尝试别的选择
        }
        // 遍历所有的桶，都放不下，直接回溯到上一层
        return false
    }
    return backtrack(0)
};
```

剪枝：
1. 可以先对 nums 从大到小排序，以触发更多剪枝
2. 在遍历桶时，如果当前桶和上一个桶的元素和相等，那么可以跳过，因为元素放在这个桶的结果和上一个桶一致 `if (i > 0 && bucket[i] == bucket[i - 1]) continue;`


> [!Tip] 先记忆数字视角的算法
> 剪枝后效果还不错，而且这种算法也更好记忆和理解
> 1. 每次递归决定一个数 nums[index] 是否装入桶中，base case 是所有数分配完
> 2. 在递归中循环遍历每个桶，做选择
> 3. 递归中要接受底层传回的值，如果为 true ，就不要继续回溯了直接返回


## 桶视角
在决策树的每一层，`j` 号桶都要选择一个球：
- 遍历所有数字，如果数字已经被使用，则跳过；如果装不下，也跳过
- 选择数字并标记使用过
- 进入下一层，`j` 号桶选择下一个球

当一个桶装满，对下一个桶重复以上操作
- backtrack 需要两个索引，桶索引 和 球索引

Base case
- 当所有的桶装满，则满足要求返回 true

```js
var canPartitionKSubsets = function(nums, k) {
    if(k < nums) return false
    const sum = nums.reduce((a,b) => a+b)
    if(sum % k !== 0) return false
    // 记录数字是否使用过
    const used = new Array(nums.length).fill(false);
    const target = sum / k

    const backtrack = (k, start, bucket) => { // 桶的序号, 球的序号，当前桶的容量
        // base case
        if(k === 0){ // k个桶被装完
            return true
        }
        if(bucket === target){ // 当前桶被装完，递归下一个桶
            return backtrack(k-1, 0, 0)
        }

        for(let i = start; i < nums.length; i++){
            if((used[i])) continue;
            if(nums[i] + bucket > target) continue;
            bucket += nums[i]
            used[i] = true
            if(backtrack(k, i + 1, bucket)) return true
            bucket -= nums[i]
            used[i] = false
        }
        return false
    }
    return backtrack(k,0,0)
};
```

剪枝：
#todo  
https://lfool.github.io/LFool-Notes/algorithm/%E7%BB%8F%E5%85%B8%E5%9B%9E%E6%BA%AF%E7%AE%97%E6%B3%95%EF%BC%9A%E9%9B%86%E5%90%88%E5%88%92%E5%88%86%E9%97%AE%E9%A2%98.html

# 岛屿问题

> [!Tips] FloodFill
> - 总的来说，岛屿问题就是在一个二维数组中使用DFS找“连通分量”，这个“连通”的定义根据题目而定，一般是一片值相同的区域
> - FloodFill 将DFS遍历过的岛屿（二维数组的一个值）使用一个特殊值替换，以代替 visited 数组

## 二维矩阵的DFS搜索
```js
const dfs = (grid, i, j, visited) => {
	const m = grid.length, n = grid[0].length;
	if(i < 0 || j <0 || i >=m || j >= n) return
	if(visited[i][j]) return 
	visited[i][j] = true
	// 上下左右遍历
	dfs(grid, i - 1,  j, visited)
	dfs(grid, i +1,  j, visited)
	dfs(grid, i,  j - 1, visited)
	dfs(grid, i,  j + 1, visited)
}
```
## 岛屿数量
[Leetcode 200](https://leetcode.cn/problems/number-of-islands/)
思路：每遇到 1 ，res + 1，使用 DFS 将岛屿都变成 0（可以避免使用visited）
```js
var numIslands = function(grid) {
    let res = 0
    const m = grid.length, n = grid[0].length;
    for(let i = 0; i < m; i++){
        for(let j = 0; j < n; j++){
            if(grid[i][j] === "1"){
                res++;
                dfs(i, j)
            }
        }
    }
    return res

    function dfs (i, j) {
	    if(i < 0 || j <0 || i >=m || j >= n) return
        if(grid[i][j] === "0") return // 只遍历岛屿
        grid[i][j] = "0"; // 淹没
        dfs(i - 1,  j)
        dfs(i + 1,  j)
        dfs(i,  j + 1)
        dfs(i,  j - 1)
    }
};
```
## 封闭岛屿数量
[Leetcode 1254](https://leetcode.cn/problems/number-of-closed-islands/) 0为陆地，1为水，封闭岛是一个完全由1包围（左、上、右、下）的岛
![[assets/DFS  回溯_image_5.png]]
思路：关键是能够推出来，岛屿如果有靠边的，不算作「封闭岛屿」，那么可以先把靠边的岛屿淹掉，剩余的岛屿就是封闭岛屿
```js
var closedIsland = function(grid) {
    const m = grid.length, n = grid[0].length;

    function dfs (i, j) { // 淹没岛屿的DFS
	    if(i < 0 || j <0 || i >=m || j >= n) return
        if(grid[i][j] === 1) return // 只遍历岛屿
        grid[i][j] = 1; // 淹没
        dfs(i - 1,  j)
        dfs(i + 1,  j)
        dfs(i,  j + 1)
        dfs(i,  j - 1)
    }
    // 淹没 有边界的岛屿
    for(let i of [0, m-1]){
        for(let j = 0; j < n; j++){ 
            if(grid[i][j] === 0)dfs(i, j)
        }
    }
    for(let j of [0, n-1]){
        for(let i = 0; i < m; i++){ 
        if(grid[i][j] === 0) dfs(i, j)
        }
    }
    // 计算剩余岛屿
    let res = 0
    for(let i = 0; i < m; i++){
        for(let j = 0; j < n; j++){
            if(grid[i][j] === 0){
                res++;
                dfs(i, j)
            }
        }
    }
    return res
};
```

[Leetcode 1020](https://leetcode.cn/problems/number-of-enclaves/) 类似本题，求封闭岛屿的面积
```js
var numEnclaves = function(grid) {
    const m = grid.length, n = grid[0].length;
    function dfs (i, j) { // 淹没岛屿的DFS，并返回岛屿的面积
	    if(i < 0 || j <0 || i >=m || j >= n) return 0
        if(grid[i][j] === 0) return 0 // 只遍历岛屿

        let size = 1
        grid[i][j] = 0; // 淹没
        size += dfs(i - 1,  j)
        size += dfs(i + 1,  j)
        size += dfs(i,  j + 1)
        size += dfs(i,  j - 1)
        return size 
    }
    // 淹没 边界的岛屿
    for(let i of [0, m-1]){
        for(let j = 0; j < n; j++){ 
            if(grid[i][j] === 1)dfs(i, j)
        }
    }
    for(let j of [0, n-1]){
        for(let i = 0; i < m; i++){ 
        if(grid[i][j] === 1) dfs(i, j)
        }
    }
    // 计算剩余岛屿的面积
    let size = 0
    for(let i = 0; i < m; i++){
        for(let j = 0; j < n; j++){
            if(grid[i][j] === 1){
                size += dfs(i, j) // 1块岛屿
            }
        }
    }
    return size
};
```

## 岛屿的最大面积
[Leetcode](https://leetcode.cn/problems/max-area-of-island/) 返回 grid 中最大的岛屿面积，和求飞地面积类似，每次DFS一块岛屿都更新最大面积
```js
let max = 0
for(let i = 0; i < m; i++){
    for(let j = 0; j < n; j++){
		if(grid[i][j] === 1){
			max = Math.max(dfs(i, j), max) // 1块岛屿
		}
	}
 }
return max
```

## 子岛屿数量
[Leetcode 1905](https://leetcode.cn/problems/count-sub-islands/) 给定两个二维数组 grid1 grid2，求 grid2 相对 grid 1的子岛屿数量
思路：遍历 gird2 中的岛屿，遍历一块岛屿时，检查每个结点对应的 grid1 结点是否也是陆地

## 不同的岛屿数量
[Leetcode 694](https://leetcode.cn/problems/number-of-distinct-islands/)
关键点是在遍历岛屿时，记住岛屿的形状，对遍历的顺序进行序列化
用 `1, 2, 3, 4` 代表上下左右，用 `-1, -2, -3, -4` 代表上下左右的撤销
![[assets/DFS  回溯_image_6.png]]

# 数独
[Leetcode 37](https://leetcode.cn/problems/sudoku-solver/)
类似N皇后问题，对二维数组的可行解一一进行尝试：在每一个空值处穷举 1-9，判断合法性，做出合法的选择，进行下一步；如果递归到底没有发现可行解，回溯撤回选择
```js
```

# 括号生成