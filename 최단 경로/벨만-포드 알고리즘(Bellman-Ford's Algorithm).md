# 벨만-포드 알고리즘(Bellman-Ford's Algorithm)

## 특징

1. **음의 간선이 포함된 상황에서도 사용, 최단 거리를 구할 수 있다.**

2. **`음의 간선의 순환을 감지`** 할 수 있다. 

- **`단순히 음수 간선이 포함`** 되어 있을 때는 여전히 최단 거리를 계산할 수 있다.

- **`음수 간선의 순환`** 이 포함되는 경우는 **최단 거리가 음의 무한인 노드** 가 발생한다.

## 분류

음수 간선에 관하여 최단 경로 문제는 다음과 같이 분류할 수 있다.

1. 모든 간선이 양수인 경우

2. 음수 간선이 있는 경우
   1. 음수 간선 순환은 없는 경우
   
   2. 음수 간선 순환이 있는 경우 

## 구현법

1. 출발 노드를 설정한다.

2. 최단 거리 테이블을 초기화한다.

3. 다음의 과정을 `N - 1` 번 반복한다.
   
   1. 전체 간선 E 개를 하나씩 확인한다.
   
   2. `각 간선을 거쳐 다른 노드로 가는 비용` 을 계산하여 최단 거리 테이블을 갱신한다.

- **만약 음수 간선이 발생하는지 체크하고 싶다면 3번의 과정을 한번 더 수행한다.**
  
  - 이때 최단 거리 테이블이 갱신된다면 음수 간선 순환이 존재하는 것이다.  

### 다익스트라 알고리즘과의 차이점

다익스트라 알고리즘도 N - 1 번 과정을 반복하지만 특정 노드에 붙어 있는 간선만 확인한다는 차이점이 있다.

- 다익스트라 알고리즘은 벨만 포드보다 더 적은 수의 간선을 확인한다는 것이다.
  
  - 다익스트라 알고리즘이 벨만 포드 알고리즘의 Optimal한 솔루션인 것

1. **다익스트라 알고리즘**
    
    - **매번 방문하지 않은 노드 중에서 최단 거리가 가장 짧은 노드를 선택** 한다.
    
    - 음수 간선이 없다면 최적의 해를 찾을 수 있다.

2. **벨만 포드 알고리즘**

    - **매번 모든 간선을 전부 확인한다.**
    
      - 따라서 **다익스트라 알고리즘에서의 최적의 해를 항상 포함** 한다.
    
    - 다익스트라 알고리즘에 비해서 시간이 오래 걸리지만 음수 간선 순환을 탐지 할 수 있다.  


```c++
#include <iostream>
#include <vector>

#define INF 1e9 // 무한을 의미하는 값으로 10억을 설정

using namespace std;

// 노드의 개수(N), 간선의 개수(M)
// 노드의 개수는 최대 500개라고 가정
int n, m;
// 모든 간선에 대한 정보를 담는 리스트 만들기
vector<pair<int, pair<int, int> > > edges;
// 최단 거리 테이블 만들기
long long d[501]; // 오버 플로우 및 언더 플로우 방지

bool bf(int start) {
    // 시작 노드에 대해서 초기화
    d[start] = 0;
    // 전체 n - 1번의 라운드(round)를 반복
    for (int i = 0; i < n; i++) {
        // 매 반복마다 "모든 간선"을 확인하며
        for (int j = 0; j < m; j++) {
            int cur_node = edges[j].first;
            int next_node = edges[j].second.first;
            int edge_cost = edges[j].second.second;
            // 현재 간선을 거쳐서 다른 노드로 이동하는 거리가 더 짧은 경우
            if (d[cur_node] != INF and d[next_node] > d[cur_node] + edge_cost) {
                d[next_node] = d[cur_node] + edge_cost;
                // n번째 라운드에서도 값이 갱신된다면 음수 순환이 존재
                if (i == n - 1) return true;
            }
        }
    }
    return false;
}

int main(void) {
    cin >> n >> m;

    // 모든 간선 정보를 입력받기
    for (int i = 0; i < m; i++) {
        int a, b, c;
        cin >> a >> b >> c;
        // a번 노드에서 b번 노드로 가는 비용이 c라는 의미
        edges.push_back({a, {b, c}});
    }

    // 최단 거리 테이블을 모두 무한으로 초기화
    fill_n(d, 501, INF);
    
    // 벨만 포드 알고리즘을 수행
    bool negative_cycle = bf(1); // 1번 노드가 시작 노드

    if (negative_cycle) {
        cout << "-1" << '\n';
        return 0;
    }
    // 1번 노드를 제외한 다른 모든 노드로 가기 위한 최단 거리를 출력
    for (int i = 2; i <= n; i++) {
        // 도달할 수 없는 경우, -1을 출력
        if (d[i] == INF) {
            cout << "-1" << '\n';
        }
        // 도달할 수 있는 경우 거리를 출력
        else {
            cout << d[i] << '\n';
        }
    }
}
```

## 시간 복잡도

벨만 포드 최단 경로 알고리즘의 시간 복잡도는 `O(VE)` 로 다익스트라 알고리즘에 비해 느리다.

## 활용

- **음수 간선이 포함된 상황에서의 최단 거리 문제**

- [(백준 골드 4) 11657번 타임머신](https://github.com/LeeHyungGeol/Algorithm_BaekJoon/blob/master/%EB%B0%B1%EC%A4%80_if%EB%AC%B8/%EC%B5%9C%EB%8B%A8%20%EA%B2%BD%EB%A1%9C_%ED%83%80%EC%9E%84%EB%A8%B8%EC%8B%A0(%EB%B2%A8%EB%A7%8C%ED%8F%AC%EB%93%9C%20%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98)(%EC%9D%8C%EC%9D%98%20%EA%B0%84%EC%84%A0%EC%9D%B4%20%EC%A1%B4%EC%9E%AC%ED%95%98%EB%8A%94%20%EA%B2%BD%EC%9A%B0).cpp)
  - [백준 11657번 타임머신 문제 링크](https://www.acmicpc.net/problem/11657)
