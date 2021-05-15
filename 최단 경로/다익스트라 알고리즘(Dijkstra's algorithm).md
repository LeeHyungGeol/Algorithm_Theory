# 다익스트라 알고리즘(Dijkstra's algorithm)

## 특징

그래프에서 여러 개의 노드가 있을 때, 특정한 노드에서 출발하여 다른 노드로 가는 각각의 최단 경로를 구해주는 알고리즘

* `음의 간선`이 없을 때 정상적으로 동작
* 기본적으로 `그리디 알고리즘`, 매번 **가장 비용이 적은 노드**를 선택해서 임의의 과정을 반복하기 때문이다.

* **각 노드에 대한 현재까지의 최단 거리 정보**를 항상 `1차원 리스트(배열)` 에 저장하며 리스트를 계속 갱신한다.

* 한번 방문한 노드는 최단 거리가 감소하지 않는다.
  * ***한 단계당 하나의 노드에 대한 최단 거리를 확실히 찾는 것으로 이해할 수 있다.***  
  
* 노드의 개수 **`1만개 이상`**

## 구현법

***단계마다 '방문하지 않은 노드 중에서 최단 거리가 가장 짧은 노드를 선택'하기 위해 매 단계마다 1차원 리스트의 모든 원소를 확인(순차 탐색)***

1. 출발 노드를 설정한다.
2. 최단 거리 테이블을 초기화한다.
   * 출발 노드에서 출발 노드로의 거리는 **0**
   * 다른 모든 노드로 가는 최단 거리를 **무한 : (1e9) 10억**으로 초기화한다. 
3. 방문하지 않은 노드 중에서 최단 거리가 짧은 노드를 선택한다.
4. **`해당 노드를 거쳐 다른 노드로 가는 비용`** 을 계산하여 최단 거리 테이블을 갱신한다.
    * ***한 단계당 하나의 노드에 대한 최단 거리를 확실히 찾는 것으로 이해할 수 있다.***
5. 위 과정에서 3 과 4 번을 반복한다.

* `우선순위 큐(priority_queue)` 중 `최소 힙`을 이용하여 구현한다.
  
  * **C++ 에서의 우선순위 큐는 기본적으로 최대 힙** 이므로 우선순위에 해당하는 값에 **음수 부호(-)** 를 붙혀 넣었다가, 
  
  * 나중에 우선순위 큐에서 꺼낸 다음에 다시 음수 부호(-)를 붙여서 원래의 값으로 돌리는 방식을 사용할 수 있다. 

[다익스트라 알고리즘(Dijkstra's algorithm) 동작 과정](/최단%20경로/다익스트라%20알고리즘(Dijkstra's%20algorithm)%20동작%20과정.md)

```c++
#include <iostream>
#include <vecotr>
#include <queue>
#include <cstring>

#define INF 1e9 // 무한을 의미하는 값으로 10억을 설정

using namespace std;

typedef pair<int, int> pii;

// 노드의 개수(N), 간선의 개수(M), 시작 노드 번호(Start)
// 노드의 개수는 최대 100,000개라고 가정
int n, m, start;

// 각 노드에 연결되어 있는 노드에 대한 정보를 담는 배열
vector<pii> graph[100001];
// 최단 거리 테이블 만들기
int d[100001] = { 0, };

void dijkstra(int start) {
    // { 거리, 노드 } 의 묶음
    priority_queue<pii> pq;

    // 시작 노드로 가기 위한 최단 경로는 0으로 설정하여, 큐에 삽입
    pq.push( { 0, start } );
    d[start] = 0;

    // 큐가 비어있지 않다면
    while (!pq.empty()) {
        
        // 가장 최단 거리가 짧은 노드에 대한 정보 꺼내기
        int dist = -pq.top().first; // 현재 노드까지의 비용 
        int now = pq.top().second; // 현재 노드
        
        pq.pop();

        // 현재 노드가 이미 처리된 적이 있는 노드라면 무시
        // 이전에 처리된 노드이므로 최단 거리 테이블에 있는 값이 현재 큐에서 나온 거리 보다 작은 것 이다.
        if (d[now] < dist) { 
            continue;
        }

        // 현재 노드와 연결된 다른 인접한 노드들을 확인
        for (int i = 0; i < graph[now].size(); i++) {
            int cost = dist + graph[now][i].second;
            int next = graph[now][i].first;
            // 현재 노드를 거쳐서, 다른 노드로 이동하는 거리가 더 짧은 경우
            if (cost < d[next]) {
                d[next] = cost;
                pq.push({ -cost, next });
            }
        }
    }
}

int main(void) {
    cin >> n >> m >> start;

    // 모든 간선 정보를 입력받기
    for (int i = 0; i < m; i++) {
        int a, b, c;
        cin >> a >> b >> c;
        // a번 노드에서 b번 노드로 가는 비용이 c라는 의미
        graph[a].push_back({b, c});
    }

    // 최단 거리 테이블을 모두 무한으로 초기화
    fill(d, d + 100001, INF);
    //memset(d, INF, sizeof(d));
    
    // 다익스트라 알고리즘을 수행
    dijkstra(start);

    // 모든 노드로 가기 위한 최단 거리를 출력
    for (int i = 1; i <= n; i++) {
        // 도달할 수 없는 경우, 무한(INFINITY)이라고 출력
        if (d[i] == INF) {
            cout << "INFINITY" << '\n';
        }
        // 도달할 수 있는 경우 거리를 출력
        else {
            cout << d[i] << '\n';
        }
    }
}
```

* **최단 거리 테이블을 모두 무한으로 초기화** 할 때

  * `#include <cstring>`의 `memset`함수는 배열의 크기가 너무 클 때는 적용이 안되는 것 같다.
  
  * 대신 `fill` 함수를 사용하도록 하자.  

## 시간 복잡도

다익스트라 알고리즘의 시간 복잡도는 **`O(ElogV)`**

* 한번 처리된 노드는 다시 처리하지 않는다.
  * 큐에서 노드를 하니씩 꺼내 검사하는 반복문(while문)은 노드의 개수 V 이상의 횟수로는 반복되지 않는다.
  * V 번 반복될 때 마다 각각 자신과 연결된 모든 간선을 확인한다.
  * 즉, **'현재 우선순위 큐에서 꺼낸 노드와 이어진 간선들을 확인' 하는 총 횟수** 는 ***총 최대 간선의 개수(E)*** 만큼 수행한다.

따라서, 전체 다익스트라 알고리즘은 E개의 원소를 우선순위 큐에 넣었다가 모두 빼내는 연산과 유사하다.

우선순위 큐는 힙(Heap) 자료구조로 구현되어 있으므로 E개의 간선 데이터를 모두 힙에 넣었다가 다시 빼는 과정은 **O(ElogE)** 라고 볼 수 있다.

중복 간선을 포함하지 않는 경우, `O(ElogV)` 라고 할 수 있다.
* 왜냐하면, 모든 노드끼리 서로 다 연결되었다고 했을 때 간선의 개수가 약 V^2이고 E는 항상 V^2 이하이기 때문이다.
  
* O(ElogE) -> O(ElogV²) -> O(2ElogV) -> O(ElogV)

## 활용

* 한 지점에서 다른 특정 지점까지의 최단 경로를 구해야 하는 경우

- [(백준 골드 5) 1753번 최단경로](https://github.com/LeeHyungGeol/Algorithm_BaekJoon/blob/master/%EB%B0%B1%EC%A4%80_if%EB%AC%B8/%5B%EB%B0%B1%EC%A4%80%20%EA%B3%A8%EB%93%9C%205%5D%201753%EB%B2%88.cpp)
- [(백준 골드 4) 1504번 특정한 최단 경로](https://github.com/LeeHyungGeol/Algorithm_BaekJoon/blob/master/%EB%B0%B1%EC%A4%80_if%EB%AC%B8/%EC%B5%9C%EB%8B%A8%20%EA%B2%BD%EB%A1%9C_%ED%8A%B9%EC%A0%95%ED%95%9C%20%EC%B5%9C%EB%8B%A8%EA%B2%BD%EB%A1%9C(%EB%8B%A4%EC%9D%B5%EC%8A%A4%ED%8A%B8%EB%9D%BC%20%EC%9D%91%EC%9A%A9).cpp)
- [(백준 골드 2) 9370번 미확인 도착지](https://github.com/LeeHyungGeol/Algorithm_BaekJoon/blob/master/%EB%B0%B1%EC%A4%80_if%EB%AC%B8/%EC%B5%9C%EB%8B%A8%20%EA%B2%BD%EB%A1%9C_%EB%AF%B8%ED%99%95%EC%9D%B8%20%EB%8F%84%EC%B0%A9%EC%A7%80(%EB%8B%A4%EC%9D%B5%EC%8A%A4%ED%8A%B8%EB%9D%BC%20%EC%9D%91%EC%9A%A9).cpp)
