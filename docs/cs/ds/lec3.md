# Stack and Queue

A stack is a Last-In-First-Out(LIFO) list, that is, an ordered list in which insertions and deletions are made at the top only.

A queue is a First-in-First-out(FIFO) list, that is, an ordered list in which insertions take place at one end and deletions take place at the opposite end.

**Objects**: A finite ordered list with zero or more elements.

**Operations** : 

### Implementation

```c
struct StackRecord{
    int Capacity;/*size of stack*/
    int TopOfStack;/*the top pointer*/
    /*++for push,--for pop, -1 for empty stack*/
    ElementType *Array;/*array for stack elements*/
}
```

### Stack creation

```c
Stack CreateStack(int MaxElements){
    Stack S;
    if(MaxElements<MinStackSize)
        Error("Stack size is too small");
    S = malloc(sizeof(struct StackRecord));
    if(S==NULL)
        FatalError("Out of space!!");
    S->Array = malloc(sizeof(ElementType)*MaxElements);
}
```



### Applications

#### 有效的括号

给定一个只包括 `'('`，`')'`，`'{'`，`'}'`，`'['`，`']'` 的字符串 `s` ，判断字符串是否有效。

我们遍历给定的字符串 sss。当我们遇到一个左括号时，我们会期望在后续的遍历中，有一个相同类型的右括号将其闭合。由于后遇到的左括号要先闭合，因此我们可以将这个左括号放入栈顶。

当我们遇到一个右括号时，我们需要将一个相同类型的左括号闭合。此时，我们可以取出栈顶的左括号并判断它们是否是相同类型的括号。如果不是相同的类型，或者栈中并没有左括号，那么字符串 `s` 无效，返回 False

在遍历结束后，如果栈中没有左括号，说明我们将字符串`s`中的所有左括号闭合，返回 True，否则返回 False。

```c
char pairs(char a){
    if(a=='}') return '{';
    if(a==']') return '[';
    if(a==')') return '(';
    return 0;
}

bool isValid(char *s){
    int n = strlen(s);
    if(n%2){
        return false;
    }
    int skt[n+1],top=0;
    for(int i=0;i<n;i++){
        char ch=pairs(s[i]);
        if(ch){
            if(top==0||skt[top-1]!=ch){
                return false;
            }
            top--;
        }
        else{
            stk[top++]=s[i];
        }
    }
    return top==0;
}
```

