BFS 的核心思想是把问题抽象成图，从一个点开始，向**四周**开始扩散。一般来说，BFS 算法都是用「队列」这种数据结构，每次将一个节点周围的所有节点加入队列，再遍历这些节点，依次将它周围的结点加入队列，循环往复，直到队列为空
BSF 也是一种暴力穷举的算法
[[二叉树#层序遍历]]
> [!NOTE]  BFS vs DFS
> BFS 找到的路径一定是最短的，所以一般都用BFS找最短路径，但它的代价是空间复杂度比 DFS 大很多
>

# 算法框架
三层循环框架
1. while 循环判断队列是否为空
2. 第一个 for 循环循环一层的结点 （在这里访问结点、并判断，这样第一轮循环后 step = 1）
3. 第一个 for 循环循环一个结点的邻居结点 （在这里把邻居结点加入队列，下一轮判断）

```js
const BFS = (start, target) => {
	const q = [] // 队列
	const visited = new Set() // 防止走回头路
	q.push(start) // 初始起点
	visited.add(start)
	let step = 0 // 记录步数

	while(q.length !== 0){
		const len = q.length
		for(let i = 0; i < len; i++){ // 遍历同层的结点
			const cur = q.shift() // 取出一个结点
			// 判断是否抵达终点
			if(cur === target) return step
			// 将结点的邻接结点添加进队列
			for(let neighbor in cur.neighbors){
				if(!visited.has(neighbor)){
					q.push(neighbor)
					visited.add(neighbor);
				}
			}
		}
		// 遍历完一层以后，step+1
		step++
	}
}
```

## 二叉数的最小深度
[Leetcode 111](https://leetcode.cn/problems/minimum-depth-of-binary-tree/) 给定一个二叉树，找出其最小深度
问题可以等价于一个寻找最短路径的问题：从根节点到最近的叶子节点的最短路径
```js
var minDepth = function(root) {
    if(!root) return 0
    const q = [root]
    let depth = 1

    while(q.length !== 0){
        const len = q.length
        for(let i = 0; i < len; i++){ // 遍历一层
            const cur = q.shift()
            // 找到终点，叶子结点
            if (cur.left == null && cur.right == null) 
                return depth
            if (cur.left != null)
                q.push(cur.left);
            if (cur.right != null) 
                q.push(cur.right);
        }
        depth++ // 遍历下一层
    }
};
```
使用DFS也可以求解，但是要遍历完整颗二叉树才能找到最小深度
一般只在求最小路径时使用DFS

## 打开转盘锁
[Leetcode 752](https://leetcode.cn/problems/open-the-lock/) 带有四个圆形拨轮的转盘锁，每个拨轮都有10个数字（0-9），初始值为0000，`deadends` 数组存放死亡数组，`target` 是解锁数字，给出解锁需要的最小旋转次数
将问题抽象成图：从 `"0000"` 开始，转一次，可以穷举出 `"1000", "9000", "0100", "0900"...` 共 8 种密码，再以这 8 种密码作为基础，对每个密码再转一下，也能穷举出 8 种密码；那么问题可以抽象成每个结点有 8 个邻居结点的图，原问题等价于找最短路径（避开 `deadends` 密码）
```js
var openLock = function(deadends, target) {
    const plusOne = (s, j) => {
        const chars = s.split("")
        if(chars[j] === "9"){
            chars[j] = "0"
        } else {
             chars[j] = String.fromCharCode(chars[j].charCodeAt() + 1)
        }
        return chars.join("")
    }
    const minusOne = (s, j) => {
        const chars = s.split("")
        if(chars[j] === "0"){
            chars[j] = "9"
        } else {
             chars[j] = String.fromCharCode(chars[j].charCodeAt() - 1)
        }
        return chars.join("")
    }
    // BFS 框架
    const q = ["0000"]
    const deads = new Set(deadends)
    const visited = new Set("0000")
    let step = 0
    while(q.length !== 0){
        const len = q.length
        for(let i = 0; i < len; i++){ // 遍历一层的所有结点
            const s = q.shift()
            if (deads.has(s)) continue // 跳过该结点
            if (target === s) return step
            // 遍历所有邻居，加入队列
            for(let j = 0; j < 4; j++){ 
                const up = plusOne(s, j)
                const down = minusOne(s, j)
                if (!visited.has(up)) {
                    q.push(up);
                    visited.add(up);
                }
                if (!visited.has(down)) {
                    q.push(down);
                    visited.add(down);
                }
            }
        }
        step++
    }
    return -1
};
```
## 滑动谜题
[Leetcode 773](https://leetcode.cn/problems/sliding-puzzle/)
todo https://labuladong.github.io/algo/di-san-zha-24031/bao-li-sou-96f79/ru-he-yong-7333b/
# 双向 BFS 
双向 BFS 是从起点和终点同时开始扩散，当两边有交集的时候停止



