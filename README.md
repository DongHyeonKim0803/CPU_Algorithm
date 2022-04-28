---
layout: single
title:  "컴퓨터 알고리즘 중간고사"
date:   2022-04-28 10:00:46 +0900
---
#  Ford Fulkerson Algorithm
### - 특정한 지점에서 다른 지점으로 얼마나 많은 유량을 동시에 보낼 수 있는지 계산하는 문제에서 DFS 방식을 사용한 알고리즘.
---
## 기본 용어
### 용량 (Capacity) 
* #### c(u, v) : 정점 u 에서 v 로 전송할 수 있는 최대 용량
* #### 유량 (Flow) f(u, v) : 정점 u 에서 v 로 실제 흐르고 있는 유량
### 잔여 용량 (Residual Capacity) 
* #### r(u, v) = c(u, v) – f(u, v) 간선의 용량과 실제로 흐르는 유량의 차이
### 소스 (Source) 
* #### 모든 유량이 시작되는 정점
### 싱크 (Sink) 
* #### 모든 유량이 도착하는 정점
### 증가 경로 
* #### 소스에서 싱크로 유량을 보낼 수 있는 경로
---
## 동작 방식
#### 1. 네트워크에 존재하는 모든 간선의 유량을 0 으로 초기화하고, 역방향 간선의 유량도 0 으로 초기화한다.
#### 2. 소스에서 싱크로 갈 수 있는, 잔여 용량이 남은 경로를 DFS 로 탐색한다.
#### 3. 해당 경로에 존재하는 간선들의 잔여 용량 중, 가장 작은 값을 유량으로 흘려보낸다.
#### 4. 해당 유량에 음수값을 취해, 역방향 간선에도 흘려보낸다. (유량 상쇄)
#### 5. 더 이상 잔여 용량이 남은 경로가 존재하지 않을때까지 반복한다.
---

## 코드 실행 및 결과
```
#include <iostream>
#include <limits.h>
#include <queue>
#include <string.h>
using namespace std;

#define V 6
 
bool bfs(int rGraph[V][V], int s, int t, int parent[])
{
    bool visited[V];
    memset(visited, 0, sizeof(visited));
 
    queue<int> q;
    q.push(s);
    visited[s] = true;
    parent[s] = -1;
 
    while (!q.empty()) {
        int u = q.front();
        q.pop();
 
        for (int v = 0; v < V; v++) {
            if (visited[v] == false && rGraph[u][v] > 0) {
                if (v == t) {
                    parent[v] = u;
                    return true;
                }
                q.push(v);
                parent[v] = u;
                visited[v] = true;
            }
        }
    }
    return false;
}

int fordFulkerson(int graph[V][V], int s, int t)
{
    int u, v;
    int rGraph[V]
              [V];
    for (u = 0; u < V; u++)
        for (v = 0; v < V; v++)
            rGraph[u][v] = graph[u][v];
 
    int parent[V];
    int max_flow = 0;
    while (bfs(rGraph, s, t, parent)) {
        int path_flow = INT_MAX;
        for (v = t; v != s; v = parent[v]) {
            u = parent[v];
            path_flow = min(path_flow, rGraph[u][v]);
        }
        for (v = t; v != s; v = parent[v]) {
            u = parent[v];
            rGraph[u][v] -= path_flow;
            rGraph[v][u] += path_flow;
        }
        max_flow += path_flow;
    }
    return max_flow;
}
 
int main()
{
    int graph[V][V]
        = { { 0, 16, 13, 0, 0, 0 }, { 0, 0, 10, 12, 0, 0 },
            { 0, 4, 0, 0, 14, 0 },  { 0, 0, 9, 0, 0, 20 },
            { 0, 0, 0, 7, 0, 4 },   { 0, 0, 0, 0, 0, 0 } };
 
    cout << "최대 유량은 "
         << fordFulkerson(graph, 0, 5);
 
    return 0;
}
```
![logo](https://github.com/DongHyeonKim0803/CPU_Algorithm/blob/main/KakaoTalk_20220428_181021039.png)

####
---
## 성능 분석
* ###  For Fulkerson Algorithm은 비효율적으로 동작하는 경우가 있다. 
* ### 문제의 Flow가 적고, Edge가 많으면 효율적일 수 있다.
