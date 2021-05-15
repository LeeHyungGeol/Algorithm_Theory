# 플로이드 워샬 알고리즘(Floyd-Warshall Algorithm)

## 특징

**'모든 지점에서 다른 모든 지점까지의 최단 경로를 모두 구해야 하는 경우' 에 사용하는 알고리즘**

* 노드의 개수가 N개일 때 알고리즘 상으로 N번의 단계를 수행하며,     
   * 단계마다 O(N^2)의 연산을 통해 **`현재 노드를 거쳐가는 모든 경로를 고려한다.`**

* `2차원 리스트`에 **'최단 거리'** 정보를 저장한다.
  * 모든 노드에 대하여 다른 모든 노드로 가는 정보를 담아야 하기 때문이다.

* 플로이드 워셜 알고리즘은 `다이나믹 프로그래밍`이다.
  * 노드의 개수가 N개일 때, 매 단계마다 `점화식에 맞게` 2차원 리스트를 갱신하기 때문이다.

* 플로이드 워셜 알고리즘을 사용해야 하는 경우 노드의 개수는 **`최대 500개`**

## 구현법

노드의 개수가 N개일 때, N번의 단계를 수행하며, 단계마다 `현재 노드를 거쳐가는 모든 경로`를 고려한다.

* 위에서 말했듯이, 각 단계에서는 해당 노드를 거쳐가는 모든 경로를 고려한다.
  * 각 단계에 대한 설명이다. 
  
  * **현재 확인하고 있는 노드를 제외하고, N - 1 개의 노드 중에서 서로 다른 노드 (A, B) 쌍을 선택한다.**
  * *A -> B* 와 *A -> 해당 노드 -> B* 중 짧은 거리로 갱신하는 것이다.  
  * 즉, `n-1P2 개의 쌍`을 각 단계마다 반복해서 확인하는 것이다.
  * O(n-1P2) = O(N^2)

**K번 단계에 대한 점화식**

![플로이드 워셜 점화식](https://user-images.githubusercontent.com/56071088/118375385-43465180-b5fc-11eb-8054-9673dc438ec5.png)

[플로이드 워셜 알고리즘 동작 과정](/최단%20경로/플로이드%20워샬%20알고리즘%20동작%20과정.md)

* 초기 상태인 [step 0] 에서는 '연결된 간선'은 단순히 그 값을 넣고, 연결되지 않은 간선은 INF 라고 한다.
* 2차원 리스트에서 각 값 `Dab` 는 `a에서 b로 가는 최단 거리` 이다.
* 자기 자신에서 자기 자신으로 가는 거리 `Dii` 는 `0` 이라는 값으로 초기화한다.

```C++
#include <iostream>

#define INF 1e9 // 무한을 의미하는 값으로 10억을 설정

using namespace std;

// 노드의 개수(N), 간선의 개수(M)
// 노드의 개수는 최대 500개라고 가정
int n, m;
// 2차원 배열(그래프 표현)를 만들기
int graph[501][501];

int main(void) {
    cin >> n >> m;

    // 최단 거리 테이블을 모두 무한으로 초기화
    for (int i = 0; i < 501; i++) {
        fill(graph[i], graph[i] + 501, INF);
    }

    // 자기 자신에서 자기 자신으로 가는 비용은 0으로 초기화
    for (int a = 1; a <= n; a++) {
        for (int b = 1; b <= n; b++) {
            if (a == b) graph[a][b] = 0;
        }
    }

    // 각 간선에 대한 정보를 입력 받아, 그 값으로 초기화
    for (int i = 0; i < m; i++) {
        // A에서 B로 가는 비용은 C라고 설정
        int a, b, c;
        cin >> a >> b >> c;
        graph[a][b] = c;
    }
    
    // 점화식에 따라 플로이드 워셜 알고리즘을 수행
    for (int k = 1; k <= n; k++) {
        for (int a = 1; a <= n; a++) {
            for (int b = 1; b <= n; b++) {
                graph[a][b] = min(graph[a][b], graph[a][k] + graph[k][b]);
            }
        }
    }

    // 수행된 결과를 출력
    for (int a = 1; a <= n; a++) {
        for (int b = 1; b <= n; b++) {
            // 도달할 수 없는 경우, 무한(INFINITY)이라고 출력
            if (graph[a][b] == INF) {
                cout << "INFINITY" << ' ';
            }
            // 도달할 수 있는 경우 거리를 출력
            else {
                cout << graph[a][b] << ' ';
            }
        }
        cout << '\n';
    }
}
```

## 활용

- '모든 지점에서 다른 모든 지점까지의 최단 경로를 모두 구해야 하는 경우' 에 사용하는 알고리즘

- [(백준 골드 4) 11404번 플로이드](https://github.com/LeeHyungGeol/Algorithm_BaekJoon/blob/master/%EB%B0%B1%EC%A4%80_if%EB%AC%B8/%EC%B5%9C%EB%8B%A8%20%EA%B2%BD%EB%A1%9C_%ED%94%8C%EB%A1%9C%EC%9D%B4%EB%93%9C(%ED%94%8C%EB%A1%9C%EC%9D%B4%EB%93%9C%20%EC%9B%8C%EC%85%9C%20%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98)(%EB%85%B8%EB%93%9C%EC%9D%98%20%EA%B0%9C%EC%88%98%20500%EA%B0%9C%20%EC%9D%B4%ED%95%98).cpp)
- [(백준 골드 4) 1956번 운동](https://github.com/LeeHyungGeol/Algorithm_BaekJoon/blob/master/%EB%B0%B1%EC%A4%80_if%EB%AC%B8/%EC%B5%9C%EB%8B%A8%20%EA%B2%BD%EB%A1%9C_%EC%9A%B4%EB%8F%99(%ED%94%8C%EB%A1%9C%EC%9D%B4%EB%93%9C%20%EC%99%80%EC%83%AC%20%EC%9D%91%EC%9A%A9).cpp)