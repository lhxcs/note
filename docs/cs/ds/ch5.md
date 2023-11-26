# Priority Queues(Heaps)



## ADT Model

**Objects**:  A finite ordered list with zero or more elements.

**Operations**: 

- `Initialize`
- `Insert`
- `DeleteMin`
- `FindMin`



## Binary Heap

### Structure Property

A binary tree with $n$ nodes and height $h$ is **complete** iff its nodes correspond to the nodes numbered from 1 to $n$ in the perfect binary tree of height $h$.



A complete binary tree of height $h$ has between $2^h$ and $2^{h+1}-1$ nodes.

$h=\lfloor{logN}\rfloor$



If a complete binary tree with $n$ nodes is represented sequentially, then for any node with index $i(1\le i\le n)$, we have 

- index of parent(i): $\lfloor i/2\rfloor$
- index of left_child: $2i$
- index of right_child: $2i+1$



### Heap Order Property

A min tree is a tree which the key value in each node is no larger than the key values in its children(if any). 

A min heap is a complete binary tree that is also a min tree.

Analogously, we can declare a max heap.



### Basic Heap Operations

#### Insertion

```c
void Insert(ElementType X, PriorityQueue H){
    int i;
    if(IsFull(H)){
        Error("Priority queue is full.");
        return;
    }
    for(i=++H->Size;H->Element[i/2]>X;i/=2){
        H->Element[i]=H->Element[i/2];
    }
    H->Element[i]=X;
}
```

$T(N)=O(logN)$

#### DeleteMin

```c
ElementType DeleteMin(PriorityQueue H){
    int i,child;
   	ElementType MinElement,LastElement;
    if(IsEmpty(H)){
        Error("Prioriy queue is empty");
        return H->Elements[0];
    }
    MinElement=H->ELements[1];
    LastElement=H->Elements[H->Size--];
    for(i=1;i*2<=H->Size;i=Child){
        Child=i*2;
        if(Child!=H->Size&&H->Elements[Child++]<H->Elements[Child])
            Child++;
        if(LastElement>H->Element[Child]){
            H->Elements[i]=H->Elements[Child];
        }
        else break;
    }
    H->Elements[i]=LastElement;
    return MinElement;
}
```

$T(N)=O(logN)$



### Other Heap Operations

Note that finding any key except the minimum one will have to take a linear scan through the entire heap.



- DecreaseKey: Lower the value of the key in the heap at position P by a positive amount, called percolate up.
- IncreaseKey: Increase the value of the key in the heap at position P by a positive amount, called percolate down.
- Delete: remove the node at position P.
- BuildHeap:  percolate down from next to last level to the root.

**Theorem**: For the perfect binary tree of height $h$ containing $2^{h+1}-1$ nodes, the sum of the heights of the nodes is $2^{h+1}-1-(h+1)$, which means $T(N)=O(N)$

