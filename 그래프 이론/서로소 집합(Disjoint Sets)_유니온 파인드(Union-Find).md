# 서로소 집합(Disjoint Sets)_유니온 파인드(Union-Find)

## 특징

서로소 집합(Disjoint Sets) 자료 구조는 유니온 파인드(Union-Find) 자료구조라고 불리기도 한다.

`서로소 집합(Disjoint Sets)`이란 **공통 원소가 없는 두 집합**

서로소 집합 자료구조는 **`트리 자료구조`**를 이용하여 집합으로 표현

* 서로소 부분 집합들로 나누어진 원소들의 데이터를 처리하기 위한 자료구조

* `Union(합집합 연산)` : 2개의 원소가 포함된 집합을 ***하나의 집합으로 합치는 연산 : 그래프에서의 간선(E) 의 정보***

* `Find(찾기 연산)` : 특정한 원소가 ***속한 집합이 어떤 집합인지 알려주는 연산***

## 트리 자료 구조를 이용한 서로소 집합 알고리즘 구현법

**서로소 집합 정보(Union : 합집합 연산)** 이 주어졌을 때 ***트리 자료구조***를 이용하여 집합을 표현하는 서로소 집합 알고리즘은 다음과 같다.

1. **union 연산을 확인**하여, 서로 연결된 두 노드 A, B를 확인한다.
   1. A 와 B 의 **루트 노드**를 각각 찾는다.
   2. **A' 를 B' 의 부모 노드로 설정한다. = B' 가 A' 를 가리키도록 한다 : { B' -> A' }**
    - `트리`에서 **가리킨다** 라는 표현의 의미는 ***부모 노드로 설정한다 라는 의미이다.***

2. 모든 union 연산을 처리할 때 까지 1 번 과정을 반복한다.

실제로 구현할 때 : A' 와 B' 의 번호 중 **번호가 작은 것**을 부모 노드로 설정하는 경우가 보통이다.

**경로 압축(Path Compression)을 이용한 서로소 집합 알고리즘 구현**

```c++
#include <iostream>

using namespace std;

// 노드의 개수(V)와 간선(Union 연산)의 개수(E)
// 노드의 개수는 최대 100,000개라고 가정
int v, e;

int parent[100001] = { 0, };

// 특정 원소가 속한 집합을 찾기(Find 연산)
int find(int x) {
    // 루트 노드가 아니라면 루트 노드를 찾을 때 까지 재귀적으로 호출
    if(parent[x] != x) {
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

    // 부모 테이블에서 각 노드의 부모를 자기 자신으로 초기화 
    for(int i = 1; i <= v; ++i) {
        parent[i] = i;
    }

    // Union 연산 수행 // 간선의 정보들
    for(int i = 0; i < e; ++i) {
        int a, b;
        cin >> a >> b;
        Union(a, b);
    }

    // 각 원소가 속한 집합 출력하기
    cout << "각 원소가 속한 집합: ";
    for (int i = 1; i <= v; ++i) {
        cout << find(i) << ' ';
    }
    cout << '\n';

    // 부모 테이블 내용 출력하기
    cout << "부모 테이블: ";
    for (int i = 1; i <= v; ++i) {
        cout << parent[i] << ' ';
    }
    cout << '\n';

    return 0;
}
```
## 활용

### 서로소 집합을 활용한 `사이클(Cycle) 판별`

서로소 집합은 ***무방향 그래프*** 내에서의 **`사이클 판별`**할 때 사용할 수 있다는 특징이 있다.

* 방향 그래프에서의 사이클 판별은 DFS를 이용

#### 구현법

간선을 하나씩 확인하다 보면 두 노드가 포함되어 있는 집합을 합치는 과정만으로도 사이클을 판별할 수 있다.

1. 각 간선을 확인하며 두 노드의 루트 노드를 확인한다.
   1. 루트 노드가 서로 다르다면 두 노드에 대하여 union 연산을 수행한다.
   2. 루트 노드가 서로 같다면 사이클(Cycle)이 발생한 것이다.

2. 그래프에 포함되어 있는 모든 간선(E)에 대하여 1번 과정을 반복한다.

```c++
#include <iostream>

using namespace std;

// 노드의 개수(V)와 간선(Union 연산)의 개수(E)
// 노드의 개수는 최대 100,000개라고 가정
int v, e;
int parent[100001] = { 0, };

// 특정 원소가 속한 집합을 찾기
int find(int x) {
    // 루트 노드가 아니라면, 루트 노드를 찾을 때까지 재귀적으로 호출
    if(x != parent[x]) {
        return parent[x] = find(parent[x]);
    }
    return x;
}

// 두 원소가 속한 집합을 합치기
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
    for(int i = 1; i <= v; ++i) {
        parent[i] = i;
    }

    // 사이클 발생 여부
    bool cycle = false;

    for(int i = 0; i < e; ++i) {
        int a, b;
        cin >> a >> b;
        // 루트 노드가 다르므로 같은 집합으로 만들어준다. 
        if(fina(a) != find(b)) {
            Union(a, b);
        }
        // 루트 노드가 같다면 같은 집합이므로 사이클이 발생한 것이다. 
        else {
            cycle = true;
            break;
        }
    }

    if(cycle) {
        cout << "사이클이 존재한다." << '\n'; 
    } else {
        cout << "사이클이 존재하지 않는다." << '\n';
    }
}
```






