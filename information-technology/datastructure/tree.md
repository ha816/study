# Introduction

Tree
: Graph의 일종으로 Node와 Edge로 이루어진 자료구조이다. 사이클이 존재하지 않으며 대표적인 Tree로는 Binary Tree가 있다. 

![enter image description here](https://i.imgur.com/6UeCp8t.png)

# [BinaryTree](https://ratsgo.github.io/data%20structure&algorithm/2017/10/21/tree/)

이진트리는 자식노드가 **최대 두 개인 노드들로 구성된 트리이다.** 이러한 이진트리에는 정이진트리(Full Binary Tree), 완전이진트리(Complete Binary Tree), 균형이진트리(Balanced Binary Tree) 등이 있다.

## 정이진트리(Full Binary Tree)
모든 노드가 0 또는 2개의 자식을 가지는 트리. 
```      
             18
           /    \   
         15     20    
        /  \       
      40    50   
    /   \
   30   50
 ```

## 완전이진트리(Complete Binary Tree)
가장 아래 두 레벨에 있는 노드들을 제외한 모든 노드들이 두개의 자식 노드를 가진다. 그리고 가장 아래 레벨에서 노드는 왼쪽에서 오른쪽으로  채워져야 한다. 
``` 
               18
           /       \  
         15         30  
        /  \        /  \
      40    50    100   40
     /  \   /
    8   7  9
```


# [Heap](https://gmlwjd9405.github.io/2018/05/10/algorithm-heap-sort.html)

힙은 여러 값들 중에서 최댓값이나 최솟값을 빠르게 찾아내도록 만들어진 자료구조이다. 최대값을 빠르게 찾아내는 힙을 최대힙(max heap), 최소값을 빠르게 찾아내는 힙을 최소힙(min heap)이라고 한다.
 
최대 힙(max heap)은 모든 부모 노드의 값이 자식 노드의 값보다 크거나 같은 규칙을 만족하는 완전 이진 트리(Complete Binary Tree)를 말한다. 반대로 최소 힙(min heap) 모든 부모 노드의 값이 자식 노드의 값보다 작거나 같은 완전 이진 트리(Complete Binary Tree)이다. 참고로 힙 트리에서는 중복된 값을 허용한다. (이진 탐색 트리에서는 중복된 값을 허용하지 않는다.)

![](https://gmlwjd9405.github.io/images/data-structure-heap/types-of-heap.png)

## 힙 구현

힙정렬은 최대힙이나 최소힙을 구성하여 정렬하는 것이다. 힙 정렬을 구현하는데는 크게 삽입(add), 삭제(remove) 두 가지 메서드를 구현해야 한다. 

### 삽입(add)
힙에 새로운 요소가 들어오면, 일단 새로운 노드를 힙의 마지막 노드에 이어서 삽입한다.
새로운 노드를 부모 노드들과 교환해서 힙의 성질을 만족시킨다.

![](https://gmlwjd9405.github.io/images/data-structure-heap/maxheap-insertion.png)

### 삭제(remove)

최대 힙(max heap)에서 삭제 연산은 최댓값을 가진 노드를 삭제하는 것이다. 최댓값을 가진 노드는 루트 노드이므로 루트 노드가 삭제한다. 그리고는 힙의 규칙에 맞게 노드들을 재구성한다.

![](https://gmlwjd9405.github.io/images/data-structure-heap/maxheap-delete.png)


### 힙 정렬(Heap Sort)의 성능
힙 트리의 전체 높이가 $\log{n}$이므로 하나의 요소를 힙에 삽입하거나 삭제할 때 힙을 재정비하는 시간이 $\log{n}$만큼 소요된다. 전체적으로 O(nlog₂n)의 시간이 걸린다.


# B+ Tree



> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTEwMjEwODM4NzIsMTM4MDg2MzcwOF19
-->