# 위상 정렬(Toplogical Sort)

## 특징 

* 정렬 알고리즘의 일종

* ***순서가 정해져 있는 일련의 작업***을 차례대로 수행해야 할 때 사용할 수 있는 알고리즘
* **방향 그래프**의 모든 노드를 ***방향성에 거스르지 않도록 순서대로 나열하는 것*** 

* 모든 원소를 방문하기 전에 **`큐(Queue)가 빈다면`** ***사이클이 존재***한다고 판단 할 수 있다.
  
  * 사이클이 존재하는 경우 사이클에 포함되어 있는 원소 중에서 어떠한 원소도 큐에 들어가지 못하기 때문이다.
  * 큐(Queue)를 사용하기 때문에 경우에 따라 **`우선순위 큐(priority_queue)`**도 사용 가능하다!!  

* 결국, 위상 정렬은 **사이클이 없는 방향 그래프(DAG)** 에 대해서만 수행 가능하다.
  * DAG : Direct Acyclic Graph

* 위상 정렬에서는 **답이 여러개** 가 존재 가능하다.
  * 큐에 새롭게 들어가는 원소가 2개 이상일 경우, 여러가지 답이 존재 가능하다. 

* 스택(Stack)을 활용한 DFS로도 위상정렬을 수행 가능하다.
    
## 시간 복잡도

* 시간 복잡도 : `O(V+E)`
  * 차례대로 모든 노드를 확인하면서, 해당 노드에서 출발하는 간선을 전부 제거해야 하기 때문이다.
  * 모든 노드와 간선을 확인해야 하기 때문에, O(V+E) 이다.  

## 구현법

`진입 차수(indegree)` : 특정한 노드로 **들어오는** 간선의 개수를 의미

1. 진입차수가 0인 노드를 큐(Queue)에 넣는다.

2. 큐가 빌 때까지 다음의 과정을 반복한다.
   1. 큐에서 원소를 꺼내 해당 노드에서 출발하는 간선을 그래프에서 제거한다.
   2. 새롭게 진입차수가 0이 된 노드를 큐에 넣는다.

큐가 빌 때까지 큐에서 원소를 계속 꺼내서 처리하는 과정을 반복하는 것이다.

**DAG 예시)**

![DAG 예시](https://user-images.githubusercontent.com/56071088/117559829-861b9d00-b0c3-11eb-8b73-ef9882c70bd3.png)


```c++
#include <iostream>
#include <vector>
#include <queue>

using namespace std;

// 노드의 개수(V)와 간선의 개수(E)
// 노드의 개수는 최대 100,000개라고 가정
int v, e;
// 모든 노드에 대한 진입차수는 0으로 초기화
int indegree[100001] = { 0, };
// 각 노드에 연결된 간선 정보를 담기 위한 연결 리스트 초기화
vector<int> graph[100001];

// 위상 정렬 함수
void topologySort() {
    vector<int> result; // 알고리즘 수행 결과를 담을 리스트
    queue<int> q; // 큐 라이브러리 사용

    // 처음 시작할 때는 진입차수가 0인 노드를 큐에 삽입
    for (int i = 1; i <= v; i++) {
        if (indegree[i] == 0) {
            q.push(i);
        }
    }

    // 큐가 빌 때까지 반복
    while (!q.empty()) {
        // 큐에서 원소 꺼내기
        int now = q.front();
        q.pop();
        result.push_back(now);
        // 해당 원소와 연결된 노드들의 진입차수에서 1 빼기
        for (int i = 0; i < graph[now].size(); i++) {
            indegree[graph[now][i]] -= 1;
            // 새롭게 진입차수가 0이 되는 노드를 큐에 삽입
            if (indegree[graph[now][i]] == 0) {
                q.push(graph[now][i]);
            }
        }
    }

    // 위상 정렬을 수행한 결과 출력
    for (int i = 0; i < result.size(); i++) {
        cout << result[i] << ' ';
    }
}

int main() {
    cin >> v >> e;

    // 방향 그래프의 모든 간선 정보를 입력 받기
    for (int i = 0; i < e; i++) {
        int a, b;
        cin >> a >> b;
        graph[a].push_back(b); // 정점 A에서 B로 이동 가능
        // 진입 차수를 1 증가
        indegree[b] += 1;
    }

    topologySort();

    return 0;
}
```

## 활용 

* 순서가 정해져 있는 일련의 작업을 차례대로 수행해야 할 때 사용할 수 있는 알고리즘
  * 예시) 선수 과목을 고려한 학습 순서 설정 


- [(백준 골드 2) 2252번 줄 세우기](https://github.com/LeeHyungGeol/Algorithm_BaekJoon/blob/master/%EB%B0%B1%EC%A4%80_if%EB%AC%B8/%EC%9C%84%EC%83%81%20%EC%A0%95%EB%A0%AC_%EC%A4%84%20%EC%84%B8%EC%9A%B0%EA%B8%B0(2252%EB%B2%88).cpp)
- [(백준 골드 1) 3665번 최종 순위](https://github.com/LeeHyungGeol/Algorithm_BaekJoon/blob/master/%EB%B0%B1%EC%A4%80_if%EB%AC%B8/%EC%9C%84%EC%83%81%20%EC%A0%95%EB%A0%AC_%EC%B5%9C%EC%A2%85%20%EC%88%9C%EC%9C%84(3665%EB%B2%88).cpp)
- [(백준 골드 3) 1005번 ACM Craft](https://github.com/LeeHyungGeol/Algorithm_BaekJoon/blob/master/%EB%B0%B1%EC%A4%80_if%EB%AC%B8/%EC%9C%84%EC%83%81%20%EC%A0%95%EB%A0%AC_ACM%20Craft(1005%EB%B2%88).cpp)
- [(백준 골드 2) 1766번 문제집](https://github.com/LeeHyungGeol/Algorithm_BaekJoon/blob/master/%EB%B0%B1%EC%A4%80_if%EB%AC%B8/%EC%9C%84%EC%83%81%20%EC%A0%95%EB%A0%AC_%EB%AC%B8%EC%A0%9C%EC%A7%91(1766%EB%B2%88).cpp)