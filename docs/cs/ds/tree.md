# 一些算法及代码实现

!!! abstract

    记录我学习到的算法



## 图论

### 图的存储——链式前向星

链式前向星就是静态建立的邻接表，存储的是以$(1,n)$为起点的边的集合。定义如下：

```c
struct edge{
    int next;//表示与这个边起点相同的上一条边的编号
    int to;//该条边指向的点
    int w;//权值
}edge[MAX];//边集
int head[MAX];//表示以i为起点的最后一条边的编号
int cnt=0;//cnt 作为结构体下标
```

加边函数如下：

```c
void addedge(int u,int v,int w){
    edge[++cnt].next=head[u];//以u为起点上一条边的编号，即与该边起点相同的上一条边的编号
    edge[cnt].to=v;//该边的终点
    edge[cnt].w=w;
    head[u]=cnt;//更新以u为起点上一条边的编号
}
```

遍历函数：

```c
for(int i=1;i<=n;i++){
    printf("%d\n",i);
    for(int j=head[i];j;j=edge[j].next){
        printf("%d %d %d\n",i,edge[j].to,edge[j].w);
    }
}
//第一层循环找到图中的所有点，第二次循环遍历以i为起点的所有边。head[i]是以i为起点最后一条边的编号，通过edge[j].next找上一条边的编号，直至第一条边,head初始化为0，故终止条件为j=0
```



### 拓扑排序

拓扑排序是用于处理有向无环图DAG的问题。

- 初始化队列，将入度为0的节点放入队列(DAG一定有入度为0的节点)
- 取出队首，遍历其出边，将能够到达的点入度减一，同时维护答案数组(动态规划的思想)
- 若此时一个点入度为1，则入队
- 重复2直至队列为空。

在实际应用中，我们完成各项工作有一定依赖条件，在进行工作X之前需要完成其它的工作。因此完成工作X所需的时间和所有X所依赖的工作完成时间的最大值有关：$f_i=max\left\{pre_i\right\}+a_i$



```c
void topology_sort()
{
    int queue[MAX];
    int front=0;
    int rear=0;
    for(int i=1;i<=n;i++){
        if(in[i]==0){
            queue[rear++]=i;
            dp[i]=time[i];//每项工作的时间
        }
    }
    while(front!=rear){
        int d = queue[front++];
        for(int i=head[d];i;i=edge[i].next){//链式前向星存储
            int v=edge[i].to;
            in[v]--;
            if(in[v]==0){
                queue[rear++]=v;
            }
            if(dp[v]<dp[d]+time[v]){
                dp[v]=dp[d]+time[v];//动态规划
            }
        }
    }
    for(int i=1;i<=n;i++){
        ans=ans>dp[i]?ans:dp[i];
    }
}
```



### 网络流问题

#### Edmonds-Karp 增广路算法

- 不断用BFS寻找增广路并更新最大流量值，直到网络上不存在增广路为止。
- **反向边**： 一条边可以被包含于多条增广路径，所以需要让这条边有多次被选择的机会。在实现上使用邻接表成对存储，这样在更新边权时，利用奇数异或1相当于减1，偶数异或1相当于加1的性质，可以直接使用*异或1*的方式找到对应的正向边或反向边。

```c
#include<stdio.h>
#include<string.h>
#define MAX 520005;
struct node{
    int to,net;
    long long val;
}e[MAX];
int n,m,s,t,u,v;
long long w,ans,dis[MAX];
int tot=1;
int head[MAX],vis[MAX],pre[MAX];
void add(int u,int v,long long w){
    e[++tot].to=v;
    e[tot].val=w;
    e[tot].net=head[u];
    head[u]=tot;
    e[++tot].to=u;
    e[tot].val=0;
    e[tot].net=head[v];
    head[v]=tot;
}/*链式前向星存储*/

/*找增广路径*/
int bfs(){
    memset(vis,0,sizeof(vis));
    int q[MAX];
    int front=0;
    int rear=0;
    q[rear++]=s;
    vis[s]=1;
    dis[s]=0x3f3f3f3f;
    while(front!=rear){
        int x=q[front++];
        for(int i=head[x];i;i=e[i].net){
            if(e[i].val==0) continue;
            int v=e[i].to;
            if(vis[v]==1) continue;
            dis[v]=dis[x]<e[i].val?dis[x]:e[i].val;
            pre[v]=i;
            q[rear++]=v;
            vis[v]=1;
            if(v==t) return 1;
        }
    }
    return 0;

}

void update(){
    int x=t;
    while(x!=s){
        int v=pre[x];
        e[v].val-=dis[t];
        e[v^1].val+=dis[t];
        x=e[v^1].to;
    }
    ans+=dis[t];
}

int main(){
    scanf("%d%d%d%d",&n,&m,&s,&t);/*点的个数，有向边的个数，源点，汇点*/
    for(int i=1;i<=m;i++){
        scanf("%d%d%lld",&u,&v,&w);
        add(u,v,w);
    }
    while(bfs()){
        update();
    }
    printf("%lld",ans);
}
```





### 最小生成树

#### Kruskal算法

**主要思路**：

- 将边按照权值进行排序
- 用贪心的思想优先选取权值最小的边，依次连接，在这过程中使用并查集判断是否存在环。
- 直到边的数量等于总顶点数-1为止。

```c
void kruskal(){
    sort(edge,edge+m,cmp);//结构体快排
    for(int i=0;i<m;i++){
        eu=find(edge[i].start);//并查集的find
        ev=find(edge[i].to);
        if(eu==ev){
            continue;
        }
        ans+=edge[i].w;
        f[ev]=eu;
        if(++cnt==n-1){
            break;
        }
    }
}
```

### Prim算法

**主要思想**：

类似于Dijkstra算法。把点分为已经加入最小生成树的和未被加入的，每次把距离已加入的点最近的边加入生成树。



**代码实现**

记$v_i$为结点$i$到已加入部分最短的边的长度。

- 随便找一个点入最小堆
- 每次取出堆顶，pop，并判断是否加入最小生成树
- 如果不是，则将$v_i$加入`ans`
- 遍历所有连接的点$x$,若$v_x>v_u$，则将$x$入堆
- 直至堆为空或者已经加入$n-1$条边。









### 求最小环

#### 使用并查集

```c
int Find(int x){//查找操作，并记录当前结点到祖先的距离
    if(x!=a[x]){
        int last=a[x];
        a[x]=Find(a[x]);
        dist[x]+=dist[last];
    }
    return a[x];
}
for(int i=1;i<=n;i++){
        a[i]=i;//初始化祖先结点为自身
    }
for(int i=1;i<=n;i++){
        scanf("%d",&t);
        int root1=Find(i);
        int root2=Find(t);//寻找祖先结点
        if(root1!=root2){//两祖先结点不相等，说明无环，则将它们连通，更新距离。
            a[root1]=root2;
            dist[i]=dist[t]+1;
        }else{//祖先结点相同，且vertex相连，找到环，记录环的长度
            min=min<(dist[i]+dist[t]+1)?min:dist[i]+dist[t]+1;
        }
}
```







## 树

### 二叉树基础

```C
//Definition for a binary tree node.
 	struct TreeNode {
      int value;
     struct TreeNode *left;
     struct TreeNode *right;
 };
```

#### 创建节点

```C
TreeNode *newnode(int value){
    TreeNode *node=(TreeNode*)malloc(sizeof(struct TreeNode));
    node->value=value;
    node->left=NULL;
    node->right=NULL;
    return node;
}
```

#### 插入结点

```c
TreeNode *insert_recursive(TreeNode *root,int value){
    if(root==NULL) return newnode(value);
    if(root->val>value){
        root->left=insert_recursive(root->left,value);
    }else{
        root->right=insert_recursive(root->right,value);
    }
    return root;
}

TreeNode *insert_iterative(TreeNode *root,int value){
    if(root==NULL) return newnode(value);
    TreeNode *node=newnode(value);
    TreeNode *cur=root,*parent=NULL;
    while(cur){
        parent=cur;
        if(cur->val>val){
            cur=cur->left;
        }else{
            cur=cur->right;
        }
    }
    if(parent->value>value){
        parent->left=node;
    }else{
        parent->right=node;
    }
    return root;
}
```

#### 删除结点

```c
TreeNode *delete(TreeNode *root,int value){
    TreeNode *parent=NULL,*current=root;
    TreeNode *node=search_i(root,parent,value);
    if(node==NULL){
        printf("Value not found\n");
        return root;
    }
    if(!node->left&&!node->right){//删除叶子
        if(node!=root){
            if(parent->left==node) parent->left=NULL;
            else parent->right=NULL;
        }else{
            root=NULL;
        }
        free(node);
    }else if(node->left&&node->right){//删除的结点有两个子节点
        TreeNode *predecessor=max(node->left);
        delete(root,predecessor->value);
        node->value=predecessor->value;
    }else{//删除的结点有一个子节点
        TreeNode *child=(node->left)?node->left:node->right;
        if(node!=root){
            if(node==parent->left){
                parent->left=child;
            }else{
                parent->right=child;
            }
        }else{
            root=child;
        }
        free(node);
    }
    return root;
}

TreeNode *search(TreeNode *root,TreeNode *parent,int value){
    if(root==NULL) return NULL;
    TreeNode *cur=root;
    while(cur&&cur->val!=value){
        parent=cur;
        if(cur->val>val){
            cur=cur->left;
        }else{
            cur=cur->right;
        }
    }
    return cur;
}//保存了待搜索的值的父节点，便于delete
```

### 树的遍历

#### 前序遍历

根节点——左子树——右子树

递归版本：

```c
void preorder(struct TreeNode *root,int *res,int *resSize){
    if(root==NULL){
        return;
    }
    res[(*resSize)++]=root->val;
    preorder(root->left,res,resSize);
    preorder(root->right, res, resSize);
}
```

迭代版本：

区别在于递归的时候隐式地维护了一个栈，而我们在迭代的时候需要显式地将这个栈模拟出来。

核心思想：

- 每拿到一个节点 就把它保存在栈中

- 继续对这个节点的左子树重复过程1，直到左子树为空

- 因为保存在栈中的节点都遍历了左子树 但是没有遍历右子树，所以对栈中节点出栈并对它的右子树重复过程1，直到遍历完所有节点。

```c
int* preorderTraversal(struct TreeNode* root, int* returnSize){
    int *res = malloc(sizeof(int)*2000);
    *returnSize=0;
    if(root==NULL) return res;
    struct TreeNode* stk[2000];
    struct TreeNode* node=root;
    int stk_top=0;
    while(stk_top>0||node!=NULL){
        while(node){//每遇到一个节点，就加入结果，并将节点保存到中间结果
            res[(*returnSize)++]=node->val;
            stk[stk_top++]=node;
            node=node->left;
        }//遍历左子树直到空
        node=stk[--stk_top];
        node=node->right;
    }
    return res;
}
```

#### 中序遍历

递归版本很trival，在这里实现一下迭代版本，练习对栈的使用。

```c
int* inorderTraversal(struct TreeNode* root, int* returnSize){
    *returnSize=0;
    int *res=malloc(sizeof(int)*2000);
    struct TreeNode *stk[2000];
    struct TreeNode* node=root;
    int stk_top=0;
    while(stk_top>0||node){
        while(node){
            stk[stk_top++]=node;
            node=node->left;
        }
        node=stk[--stk_top];
        res[(*returnSize)++]=node->val;
        node=node->right;
    }
    return res;
}
```

#### 后序遍历

```c
int* postorderTraversal(struct TreeNode* root, int* returnSize){
    int *res = malloc(sizeof(int)*2000);
    *returnSize = 0;
    struct TreeNode *stk[2000];
    int top=0;
    struct TreeNode *node =root;
    struct TreeNode *prev = NULL;//与前序和中序不一样的地方，维护一个prev指针，判断是否加入答案。
    while(top>0||node!=NULL){
        while(node){
            stk[top++]=node;
            node=node->left;
        }
        node=stk[--top];
        if(node->right==NULL||node->right==prev){
            res[(*returnSize)++]=node->val;
            prev=node;
            node=NULL;
        }else{
            top++;
            node=node->right;
        }
    }
    return res;
}
```

#### 层序遍历

**广度优先搜索**： 

- 使用队列存储对应的二叉树结点
- 使用`head`和`rear`维护队列，FIFO
- 

```c
int** levelOrder(struct TreeNode* root, int* returnSize, int** returnColumnSizes){
    int** ans=(int**)malloc(sizeof(int*)*2000);
    *returnSize=0;
    if(root==NULL) return NULL;
    int columnSizes[2000];
    struct TreeNode *queue[2000];//模拟队列
    int rear=0;
    int head=0;//队列头尾
    queue[rear++]=root;//根节点入队
    while(rear!=head){//队列不为空
        ans[(*returnSize)]=(int*)malloc(sizeof(int)*2000);
        columnSizes[(*returnSize)]=rear-head;
        int start=head;//记录开始遍历的位置，本层的头
        head=rear;//本层的尾部为下层的头，因为本层所有元素均出队
        //在这里下层的头head同时作为遍历结束的位置，因为在遍历中rear会不断改变，成为下层的尾
        for(int i=start;i<head;i++){
            ans[(*returnSize)][i-start]=queue[i]->val;
            if(queue[i]->left){
                queue[rear++]=queue[i]->left;
            }
            if(queue[i]->right){
                queue[rear++]=queue[i]->right;
            }
        }
        (*returnSize)++;
    }
        *returnColumnSizes=(int*)malloc(sizeof(int)*(*returnSize));
        for(int i=0;i<*returnSize;i++)
             (*returnColumnSizes)[i]=columnSizes[i];
        return ans;

}
```

### 构建类问题

#### 根据先序，中序遍历构建二叉树

```c
btnode *buildtree(int preorder[],int inorder[],int n){
    if(n==0) return NULL;
    btnode *root=(struct btnode*)malloc(sizeof(struct btnode));
    root->value=preorder[0];
    int i=0;
    while(inorder[i]!=root->value){
        i++;
    }
    if(i==0){
        root->left=NULL;
        root->right=buildtree(&preorder[1],&inorder[1],n-1);
    }
    else if(i==n-1){
        root->right=NULL;
        root->left=buildtree(&preorder[1],inorder,n-1);
    }
    else if(i!=0&&i!=n-1){
        root->left=buildtree(&preorder[1],inorder,i);
        root->right=buildtree(&preorder[1],&inorder[i+1],n-i-1);
    }
    return root;
}
```

#### 根据中序，后序遍历构建二叉树

```c
Tree buildtree(int post[],int in[],int n){
    Tree root=(Tree)malloc(sizeof(Tree));
    root->key=post[n-1];
    if(n==1){
        root->left=NULL;
        root->right=NULL;
        return root;
    }
    int i=0;
    while(in[i]!=post[n-1]){
        i++;
    }
    if(i==0){
        root->left=NULL;
        root->right=buildtree(post,&in[1],n-1);
    }
    else if(i==n-1){
        root->right=NULL;
        root->left=buildtree(post,in,n-1);
    }
    else if(i!=0&&i!=n-1){
        root->left=buildtree(post,in,i);
        root->right=buildtree(&post[i],&in[i+1],n-i-1);
    }
    return root;
}
```

