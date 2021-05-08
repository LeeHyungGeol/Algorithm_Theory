# 최소 신장 트리(Minimum Spanning Tree)

## 신장 트리(Spanning Tree)

**`신장 트리`** : **하나의 그래프**가 있을 때 ***모든 노드를 포함***하면서 **사이클이 존재하지 않는 부분 그래프**

* 모든 노드를 포함하면서 사이클이 존재하지 않는다 : `트리의 성립 조건`

* 트리 자료구조는 노드가 N개일 때, 항상 간선의 개수가 N - 1 이다.

![가능한 신장 트리 예시](https://user-images.githubusercontent.com/56071088/117545574-563bad80-b061-11eb-9012-d25f82f9e5c8.png)


## 크루스칼 알고리즘(Kruskal Algorithm)

### 특징

크루스칼 알고리즘은 최소 신장 트리 알고리즘 중 하나!!

* 신장 트리 중에서 **최소 비용**으로 만들 수 있는 신장 트리를 찾는 알고리즘
  * 가장 적은 비용으로 모든 노드를 연결

* 크루스칼 알고리즘은 **그리디 알고리즘(Greedy)** 으로 분류 

* **일종의 트리(Tree) 자료구조**이므로 신장 트리에 포함되는 **간선의 개수**가 `노드의 개수 - 1` 과 같다.

`크루스칼 알고리즘의 핵심 원리` : 가장 거리가 짧은 간선부터 차례대로 집합에 추가하면 된다.
  * 다만, 사이클을 발생시키는 간선은 제외하고 연결  

### 시간 복잡도

간선의 개수가 E개일 때, **`O(ElogE)의 시간 복잡도`** 를 가진다.

* 크루스칼 알고리즘에서 가장 오래 걸리는 작업이 간선을 `정렬`하는 작업
* E개의 데이터를 정렬했을 떄의 시간 복잡도는 O(ElogE)이기 때문이다.

### 구현법

1. 간선 데이터를 **비용**에 따라 **오름차순으로 정렬**
2. 간선을 하나씩 확인하며 현재의 간선이 사이클을 발생시키는지 확인한다.
   1. 사이클을 발생하지 않는 경우 최소 신장 트리에 포함시킨다.
   2. 사이클을 발생하는 경우 최소 신장 트리에 포함시키지 않는다.

3. 모든 간선에 대하여 2의 과정을 반복한다.

**크루스칼 알고리즘 구현**

```c++
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

// 노드의 개수(V)와 간선(Union 연산)의 개수(E)
// 노드의 개수는 최대 100,000개라고 가정
int v, e;
int result = 0;
int parent[100001] = { 0, };

// 모든 간선을 담을 리스트와, 최종 비용을 담을 변수
vector<pair<int, pair<int, int>>> graph;

int find(int x) {
    if(x != parent[x]) {
        return parent[x] = find(parent[x]);
    }
    return x;
}

void Union(int a, int b) {
    a = find(a);
    b = find(b);

    if(a < b) {
        parent[b] = a;
    } else {
        parent[a] = b;
    }
}

int main() {
    cin >> v >> e;

    // 부모 테이블상에서, 부모를 자기 자신으로 초기화
    for (int i = 1; i <= v; i++) {
        parent[i] = i;
    }

    // 모든 간선에 대한 정보를 입력 받기
    for (int i = 0; i < e; i++) {
        int a, b, cost;
        cin >> a >> b >> cost;

        // 비용순으로 정렬하기 위해서 튜플의 첫 번째 원소를 비용으로 설정
        graph.push_back({cost, {a, b}});
    }

    // 간선을 비용순으로 정렬
    sort(graph.begin(), graph.end());

    // 간선을 하나씩 확인하며
    for (int i = 0; i < graph.size(); i++) {
        int cost = graph[i].first;
        int a = graph[i].second.first;
        int b = graph[i].second.second;

        // 사이클이 발생하지 않는 경우에만 집합에 포함
        if (findParent(a) != findParent(b)) {
            unionParent(a, b);
            result += cost;
        }
    }

    cout << result << '\n';

    return 0;
}

```

### 활용

* N개의 도시가 존재하는 상황에서 두 도시 사이에서 두 도시 사이에 도로를 놓아 전체 도시가 서로 연결될 수 있게 도로를 설치하는 경우
  * 모든 도시를 `연결`할 때, `최소한의 비용`으로 연결할 때 사용  


- [(실버 3) 9372번 상근이의 여행](https://github.com/LeeHyungGeol/Algorithm_BaekJoon/blob/master/%EB%B0%B1%EC%A4%80_if%EB%AC%B8/%EC%B5%9C%EC%86%8C%20%EC%8B%A0%EC%9E%A5%20%ED%8A%B8%EB%A6%AC_%20%EC%83%81%EA%B7%BC%EC%9D%B4%EC%9D%98%20%EC%97%AC%ED%96%89(9372%EB%B2%88).cpp)
- [(골드 4) 1197번 최소 스패닝 트리](https://github.com/LeeHyungGeol/Algorithm_BaekJoon/blob/master/%EB%B0%B1%EC%A4%80_if%EB%AC%B8/%EC%B5%9C%EC%86%8C%20%EC%8B%A0%EC%9E%A5%20%ED%8A%B8%EB%A6%AC_%EC%B5%9C%EC%86%8C%20%EC%8A%A4%ED%8C%A8%EB%8B%9D%20%ED%8A%B8%EB%A6%AC(1197%EB%B2%88).cpp)
- [(골드 4) 4386번 별자리 만들기](https://github.com/LeeHyungGeol/Algorithm_BaekJoon/blob/master/%EB%B0%B1%EC%A4%80_if%EB%AC%B8/%EC%B5%9C%EC%86%8C%20%EC%8B%A0%EC%9E%A5%20%ED%8A%B8%EB%A6%AC_%EB%B3%84%EC%9E%90%EB%A6%AC%20%EB%A7%8C%EB%93%A4%EA%B8%B0(4386%EB%B2%88).cpp)
- [(골드 4) 1774번 우주신과의 교감](https://github.com/LeeHyungGeol/Algorithm_BaekJoon/blob/master/%EB%B0%B1%EC%A4%80_if%EB%AC%B8/%EC%B5%9C%EC%86%8C%20%EC%8B%A0%EC%9E%A5%20%ED%8A%B8%EB%A6%AC_%EC%9A%B0%EC%A3%BC%EC%8B%A0%EA%B3%BC%EC%9D%98%20%EA%B5%90%EA%B0%90(1774%EB%B2%88).cpp)
- [(골드 4) 1647번 도시 분할 계획](https://github.com/LeeHyungGeol/Algorithm_BaekJoon/blob/master/%EB%B0%B1%EC%A4%80_if%EB%AC%B8/%5B%EB%B0%B1%EC%A4%80%20%EA%B3%A8%EB%93%9C%204%5D%201647%EB%B2%88.cpp)
- [(골드 1) 2887번 행성 터널](https://github.com/LeeHyungGeol/Algorithm_BaekJoon/blob/master/%EB%B0%B1%EC%A4%80_if%EB%AC%B8/%EC%B5%9C%EC%86%8C%20%EC%8B%A0%EC%9E%A5%20%ED%8A%B8%EB%A6%AC_%ED%96%89%EC%84%B1%20%ED%84%B0%EB%84%90(2887%EB%B2%88).cpp)
  
