### Dijkstra

最短路径算法Dijkstra 以贪心的方式每次按照结点顺序每次找到一个距离起始结点距离最近的路径，然后更新和这个结点联通的其他结点。

```python

def dijkstra(matrix, begin):
    n, m = len(matrix), len(matrix[0])
    vis = [False for i in range(n)]
    dis = [matrix[begin][i] for i in range(n)]
    vis[begin] = True
    for i in range(1, n):
        minn = float("inf")
        for j in range(1, n):
            if not vis[j] and dis[j] < minn:
                minn = dis[j]
                temp = j
        vis[temp] = True
        for i in range(n):
            if matrix[temp][i] + dis[temp] < dis[i]:
                dis[i] = matrix[temp][i] + dis[temp]
    return dis


matrix = [[0, 0, 0, 0, 0, 0, 0],
          [0, 0, 1, 12, float("inf"), float("inf"), float("inf")],
          [0, float("inf"), 0, 9, 3, float("inf"), float("inf")],
          [0, float("inf"), float("inf"), 0, float("inf"), 5, float("inf")],
          [0, float("inf"), float("inf"), 4, 0, 13, 15],
          [0, float("inf"), float("inf"), float("inf"), float("inf"), 0, 4],
          [0, float("inf"), float("inf"), float("inf"), float("inf"), float("inf"), 0]]

print(dijkstra(matrix, 1))
```

