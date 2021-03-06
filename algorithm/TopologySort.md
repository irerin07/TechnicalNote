# **TopologySort(위상정렬)**

## 작성자
[![tdm1223](https://avatars1.githubusercontent.com/u/21440957?s=100&v=4)](https://github.com/tdm1223)

## 1. TopologySort란?
- 순서가 정해져있는 작업을 수행해야 할 때 그 **순서를 정하기 위해** 사용하는 정렬 알고리즘이다.
- **DAG**<sup>[1)](#ref1)</sup>에만 적용이 가능하다.
- 정렬의 결과는 여러가지가 나올 수 있다.

![image](https://user-images.githubusercontent.com/21440957/64424653-9c01df00-d0e4-11e9-8f22-32f954e765ca.png)

- 위 그림에서 Topology Sort 결과는 0->1->3->2->4가 된다.

### TopologySort의 **시간 복잡도**
- 시간 복잡도 : O(V+E) (정점의 개수 + 간선의 개수)

## 2. TopologySort의 구현
1. 진입차수가 0인 정점을 큐에 삽입한다.
2. 큐에서 원소를 꺼내 연결된 모든 간선을 제거한다.
3. 간선 제거 후에 진입차수<sup>[2)](#ref1)</sup>가 0이 된 정점을 큐에 삽입한다.
4. 큐가 빌 때까지 2~3의 과정을 반복한다.
- 모든 원소를 방문하기 전에 큐가 비었다면 사이클이 존재하는 것이다.
- 모든 원소를 방문하고 큐가 비었다면 큐에서 꺼낸 순서가 정렬의 결과가 된다.

### C++ Version
```cpp
// 인자 : 간선 모음, 진입차수 배열, 크기
vector<int> topologySort(vector<vector<int>>& a, vector<int>& inDegree, int size)
{
	queue<int> q;
	vector<int> result;
	
	// 진입 차수가 0인 노드를 큐에 삽입
	for (int i = 0; i < size; i++)
	{
		if (inDegree[i] == 0)
		{
			q.push(i);
		}
	}
	
	// 모든 노드 반복
	for (int i = 0; i < size; i++)
	{
		// n개를 방문하기 전에 큐가 빈다면 사이클 발생
		if (q.empty())
		{
			return result;
		}
		int front = q.front();
		q.pop();
		result.push_back(front);
		for (int j = 0; j < a[front].size(); j++)
		{
			int x = a[front][j];
			// 새롭게 진입차수가 0이 된 정점을 큐에 삽입
			if (--inDegree[x] == 0)
			{
				q.push(x);
			}
		}
	}
	
	return result;
}
```

### Java Version
```java
import java.util.*;

class TopologySort{
    
    /**
    * @param adjacencyList 인접리스트
    * @param inDegree 진입차수의 리스트
    * @param size 노드수
    * @return List<Integer> 정렬의 결과(크기가 size와 다를 경우 사이클 존재)
    */
    public List<Integer> topologySort(List<List<Integer>> adjacencyList, List<Integer> inDegree, int size){
        LinkedList<Integer> queue = new LinkedList<>();
        LinkedList<Integer> result = new LinkedList<>();
        inDegree = new ArrayList<>(inDegree); // pass by value를 이용한 재정의
        // 진입 차수가 0인 노드를 큐에 삽입
        for(int i = 0 ; i < size; i++){
            if(inDegree.get(i) == 0){
                queue.offer(i);
            }
        }

        int currentNode, connectedNode;
        List<Integer> currentList;
        // 모든 노드 반복
        for(int i = 0 ; i < size; i++){
            if(queue.isEmpty()){
                return result;
            }
            currentNode = queue.poll();
            result.offer(currentNode);
            currentList = adjacencyList.get(currentNode);
            for(int j = 0, len = currentList.size(); j < len; j++){
                connectedNode = currentList.get(j);
                inDegree.set(connectedNode, inDegree.get(connectedNode)-1);
                if(inDegree.get(connectedNode) == 0){
                    queue.offer(connectedNode);
                }
            }
        }
        return result;
    }
}
```

### Python Version
```python3
def topologySort(adjacencyList, inDegree, size):
    queue = []
    result = []
    inDegree = [x for x in inDegree] # call by sharing을 이용한 재정의
    for i in range(size):
        if inDegree[i] == 0:
            queue.append(i)
    
    currentNode = 0
    for i in range(size):
        if len(queue) == 0 :
            return result
        
        currentNode = queue.pop(0)
        result.append(currentNode)
        for connectedNode in adjacencyList[currentNode]:
            inDegree[connectedNode] = inDegree[connectedNode] -1
            if(inDegree[connectedNode] == 0):
                queue.append(connectedNode)
    return result
```

### JavaScript Version
```js
let topologySort = (adjacencyList, inDegree, size)=>{
    let result = new Array();
    inDegree = inDegree.map(v=>v); // call by sharing 이용
    // 진입 차수가 0인 노드를 큐에 삽입
    let queue = inDegree.filter(value => value == 0);
    
    // 모든 노드 반복
    let currentNode;
    for(let i = 0; i < size; i++){
        if(queue.length == 0){
            return result;
        }

        currentNode = queue.shift();
        result.push(currentNode);
        adjacencyList[currentNode].forEach(value => {
            inDegree[value]--;
            if(inDegree[value] == 0){
                queue.push(value);
            }
        });
    }
    return result;
}
```

## 각주
<a id="ref1">

1. Directed Acyclic Graph : 사이클이 없는 방향 그래프
2. 특정한 노드가 있을때 그 노드로 들어오는 다른 노드의 개수

</a>
