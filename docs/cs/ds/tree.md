# 树

## 二叉树

```C
//Definition for a binary tree node.
 	struct TreeNode {
      int val;
     struct TreeNode *left;
     struct TreeNode *right;
 };
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

