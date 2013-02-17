---
title: A * 寻路
layout: post
categories: dev
published: true

---

目前的场景格式没有使用导航网格，只是基于方格。采用 A\* 寻路算法时，节点是多边形网格还是普通方格唯一的区别在于路径的估价。

A\* 算法可以看作是最好优先搜索（Best-First Search）的一种实现，采用启发式（Heuristic）搜索寻找给定结点到目标结点具有最低启发代价（Heuristic Cost）的路径。

### 术语（Terms）

* 总代价函数 `f(x)`：起点到终点的路径代价/估价，`f(x) = g(x) + h(x)`。
* 路径代价函数 `g(x)`：起点到最佳节点 `x` 的路径代价，已经走过的路径的实际代价。
* 启发估价函数 `h(x)`：从 `x` 到目标节点的距离估价，未走过的路径的启发估价，最佳节点到目标节点的直线距离可以作为可接受的启发估价，最简单，虽然未必准确。
* 开放列表（open list）：待评价的节点集合。
* 关闭列表（closed list）：评价过的节点集合。

### 实现（Descriptions）

1. 将起点添加到开放列表，初始化起点的 `f/g/h` 值。
2. 如果开放列表为空，寻路失败，转 9。
3. 从开放列表中选取总代价 `f` 最小的节点作为最佳节点，并将最佳节点移入关闭列表。
4. 如果最佳节点即目标节点，寻路成功，转 7。
5. 获取最佳节点的可通过邻接节点，将不在开放列表中的邻接节点加入到开放列表。
6. 遍历邻接节点，计算并更新邻接节点的估价及总代价，转 2。
7. 根据节点继承关系计算路径及关键节点，确定路径点。
8. 优化路径点（去除不必要的路径点）。
9. 结束。

    {% codeblock lang:cpp %}
    Path::Path(int idx, const point_t &start, const point_t &end)
        : _start_point(start)
        , _end_point(end)
    {
        _mesh = Mesh::GetMesh(idx);
        ELF_ASSERT(_mesh);
        _start_node = _mesh->GetTile(start);
        _end_node = _mesh->GetTile(end);
        _g[_start_node] = 0.0f; // cost from nodes along the best known path
        _f[_start_node] = _mesh->Distance(_start_node, _end_node);

        // the open list initially contains the start node
        _open.insert(_start_node);
    }

    bool Path::FindPath(void)
    {
        path_rc rc = PATH_RC_FINDING;
        bool res = false;

        while ((rc = Step()) == PATH_RC_FINDING) { // empty loop
        }
        if (rc == PATH_RC_FOUND) {
            ReconstructPath(_end_node);
            CalculatePathPoints();
            OptimizePathPoints();
            res = true;
        }
        return res;
    }

    Path::path_rc Path::Step(void)
    {
        if (_open.empty()) return PATH_RC_FAILED;

        CalculateCurrent();
        _selected.push_back(_cur_node);
        if (_cur_node == _end_node) {
            return PATH_RC_FOUND;
        }

        CalculateNeighbors();

        Mesh::tile_list::const_iterator itr = _neighbors.begin();

        // move the current node from the open list to the closed set
        _open.erase(_cur_node);
        _closed.insert(_cur_node);
        for (; itr != _neighbors.end(); ++itr) {
            int n = *itr;

            if (InClosedList(n)) continue;

            // tentative g score
            float tg = _g[_cur_node] + _mesh->Distance(_cur_node, n);
            bool in = InOpenList(n);

            if (!in || tg < _g[n]) {
                _came_from[n] = _cur_node;
                _g[n] = tg;
                _f[n] = _g[n] + _mesh->Distance(n, _end_node);
                if (!in) {
                    _open.insert(n);
                }
            }
        }
        return PATH_RC_FINDING;
    }
    {% endcodeblock %}

### RecastNavigation 的 A\* 实现

[RecastNavigation](http://code.google.com/p/recastnavigation/) （下文简称 Recast）基于导航网格，如前文所述，A\* 算法与节点形状无关，区别在于路径估价。

Recast 的代价函数综合了路径点的距离与所在节点的代价（节点的地形、地貌，沼泽、草地、沙漠、公路），距离估价函数略小于两点直线距离（乘了一个因子 0.999f）。

### 带动态阻挡的寻路算法

* 根据动态阻挡情况，定期调整静态阻挡（将暂时被堵死的路段上的节点修改为静态阻挡，以影响寻路结果）。
* 使用 A\* 寻路，确定路径点：`path_points`。
* 路径上没有动态阻挡，按路径点 `path_points` 移动，移除经过的路径点（`path_points`）。
* 路径上有动态阻挡，寻找合适的转向角，确认中间节点。
* 移动到中间节点。
* 重新优化路径点。

带动态阻挡的寻路实现是比较复杂的。难点在于：

* 当前路径上有动态阻挡时，如何计算合适的转向角及新的关键节点。
* 偏离起初的路径之后，如何尽快恢复到正轨上。
* 结合移动对象自身体积（半径）计算通过性和路径。

### TODO

* 优化最佳节点的选取，减少遍历，可使用一次冒泡（Bubble Up）。
* 优化寻路失败情况下的效率，简单粗暴的方法是设定路径长度/开放列表节点数阈值。

### References

* [A\* search algorithm](http://en.wikipedia.org/wiki/A*_search_algorithm)
* [A\* Pathfinding for Beginners](http://www.policyalmanac.org/games/aStarTutorial.htm)

