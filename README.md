# Algorithm

## heap

堆可以实现一个优先队列, 可以用一颗树表示,``子节点都不能大于其根节点``,最简单的堆可以使用数组存放,然后在插入书时按照子节点都不能大于其根节的要求插入

* insert操作

  每一次插入都是把元素插入到数组的最后,然后对这个数据进行shift up操

  ```markdown
  shift up

  //向上移动元素
  //新的数据总是加在最后一个,所以就是外面传进来的k这个元素
  //调整这个新加入的元素,调整到合适的位置,每次都跟父节点比大小,如果比父节点大就交换位置
  //直到调整到合适的位置,父节点的位置就是当前index/2
  void shiftUp(int k) {
      while(k > 1 && data[k/2] < data[k]) {
          swap(data[k/2], data[k]);
          k /= 2;
      }
  }
  ```

* 取出堆中,权重最大的值,并且调整堆,也就是取出第一个元素,然后把最后一个元素放到第一个元素的位置,count -- ,然后再针对,第一个元素做shift down 的操作

  堆排序也是同样的道理,不停的取出第一个元素, 调整堆再取第一个元素

  ```
  shift down

  //向下移动元素
  //下一个元素的index值为2*index,所以只需要比较index 与2*index 还有 2*index + 1的大小就可以了
  //如果index<2*index,那么交换位置,继续比较
  //跟左右孩子比,谁大跟谁换

  void shiftDown(int k) {

      //k这个元素有左孩子的话
      while(count >= 2*k) {
          //j与j+1表示将要被比较的元素的位置,就是k元素的左右孩子
          int j = 2*k;

          //j+1就是右孩子,j+1 <= count 说明有右孩子
          //先看右孩子跟左孩子谁大
          //如果是右孩子大的话,把j更新成j+1
          if(j+1 <= count && data[j+1] > data[j]){
              ++j;
          }

          //目前 j就是左右孩子中最大的一个

          //如果k本来就比左右孩子都大,那么啥都不干就可以
          if(data[k]>=data[j]) {
              break;
          }

          swap(data[k], data[j]);
          k=j;
      }
  }
  ```

* heapify操作

  用于生成一个堆的时候,区别于最简单的方式, 直接针对一个数组执行heapify操作生成堆,而不是一个一个的shiftup操作

  除去全部的没有孩子的叶子节点,然后遍历,挨个执行shiftdown的操作,heapify全部的数据,也就是都进行一遍shiftdown的操作

  ```
  //执行heapify操作
  //count/2; 这个值正好是除去全部的叶子节点,剩下的最大的一个不是节点的堆
  //然后遍历,执行shiftdown的操作就可以将全部的数据都进行一遍shiftdown的操作
  for (int i = count/2; i >=1 ; --i) {
      shiftDown(i);
  }
  ```

* 索引堆

  不直接交换数组,交换数组的索引,在创建堆的时候,创建一个数组存放索引,这样就可以,操作索引,而不是数组本身,避免交换产生的消耗



### tree-二分搜索树

每个节点值大于左孩子,小于右孩子这样的一颗完全二叉树,经常可以用来处理key-value类型,类似字典的实现

* 定义,BST对象

```
class BST{
    //二分搜索树的结构体
    //每个节点值大于左孩子,小于右孩子
    struct Node {
        Key key;
        Value value;
        Node *right;
        Node *left;
    };
    Node *root;
    int count;//维护这个属性可以记录有多少节点
}
```

* 插入,递归插入,一直插到最后

```
//插入从根节点开始
//比当前节点小就插入到左孩子,大就插入到右孩子,一直插到底(即下一个左孩子或者右孩子为空的时候)
//如果跟某个节点一样的话就更新这个值
Node* insert(Node *node, Key key, Value value) {
    //递归退出的情况,代表已经找到了
    if(node == NULL) {
        count++;
        return new Node(key, value);
    }
    //更新当前key值
    if (node->key == key) {
        node->value = value;
    }
    else if(key < node->key) {
        //先理解等号右边
        node->left = insert(node->left, key, value);
    }
    else {
        node->right = insert(node->right, key, value);
    }
    return node;
}
```

* 遍历

  * 深度遍历,也就是一直遍历到底

  ```
  //前序遍历-深度遍历
  void preOrder(Node *node) {
    if(node != NULL) {
        cout<<node->key<<" ";
        preOrder(node->left);
        preOrder(node->right);
    }
  }
  //中序遍历-深度遍历
  void inOrder(Node *node) {
    if(node != NULL) {
        inOrder(node->left);
        cout<<node->key<<endl;
        inOrder(node->right);
    }
  }
  //后序遍历-深度遍历
  void postOrder(Node *node) {
    if(node != NULL) {
        postOrder(node->left);
        postOrder(node->right);
        cout<<node->key<<endl;
    }
  }
  ```

  * 广度遍历,通过一个队列的协助来进行层次的遍历

  ```
  //层序遍历,也就是广度遍历
  void levelOrder(){
      queue<Node*> q;
      q.push(root);
      while(!q.empty()) {
          Node *node = q.front();
          q.pop();
          cout<<node->key<<" ";
          
          if(node->left) {
              q.push(node->left);
          }
          if(node->right) {
              q.push(node->right);
          }
      }
      cout<<endl;
  }
  ```

  ​

* 删除最大最小节点,很简单递归,左节点或者右节点

  ```
  Node* removeMin(Node *node) {
      //如果没有左节点
      if (node->left == NULL) {
          Node *rightNode = node->right;
          delete node;
          count --;
          return rightNode;
      }

      node->left = removeMin(node->left);

      return node;
  }

  Node* removeMax(Node *node) {
      //如果没有左节点
      if (node->right == NULL) {
          Node *leftNode = node->left;
          delete node;
          count --;
          return leftNode;
      }

      node->right = removeMax(node->right);

      return node;
  }
  ```

* 删除某个节点 

  根据待删除的key先找到(递归)对应的节点,如果找不到就返回空

  找到后需要删除当前节点,然后维护这颗树也就是补全要分几种情况

  ​	只有左或者右子树的时候,直接把left/right 替换为当前被删除的节点

  ​	当前节点有左右子树的时候, 寻找右子树中最小的/左子树中最大的节点 替换当前节点

  ```
  // 删除掉以node为根的二分搜索树中键值为key的节点, 递归算法
  // 返回删除节点后新的二分搜索树的根
  Node *remove(Node* node, Key key) {
      if(node == NULL) {
          return NULL;
      }
      if (key < node->key) {
          node->left = remove(node->left, key);
          return node;
      }
      else if (key > node->key) {
          node->right = remove(node->right, key);
          return node;
      }
      //key == node.key
      else {

          //待删除节点左子树为空的情况
          if( node->left == NULL ){
              Node *rightNode = node->right;
              delete node;
              count --;
              return rightNode;
          }

          // 待删除节点右子树为空的情况
          if( node->right == NULL ){
              Node *leftNode = node->left;
              delete node;
              count--;
              return leftNode;
          }

          // 待删除节点左右子树均不为空的情况
          // 找到比待删除节点大的最小节点, 即待删除节点右子树的最小节点
          // 用这个节点顶替待删除节点的位置

  		//有点错误不想改了
          Node *successor = new Node(minNode(node->right));
          //因为新生成了一个节点所以要++一下
          count ++;

          //寻找右子树中最小的跟左子树中最大的效果是一样的
          successor->right = removeMin(node->right);
          successor->left = node->left;

          //跟上面的两行一样
          // successor->left = removeMax(node->left);
          // successor->right = node->right;

          delete node;
          count --;
    
          return successor;
      }
  }
  ```

### 图-无权图

无权图 根据每个节点有多少边可以分为稀疏图(SparseGraph)跟稠密图(DenseGraph),都可以用一个二维数组表示,不同的是稀疏图不需要按钮节点个数全部初始化二维数组,有多少条边就放多少内容(节点),而稠密图不一样稠密图利用二维数组创建一个矩阵存放是否有相对应的边, 存放需要初始化全部的二维数组为false

* 基本结构
  * 稀疏图(SparseGraph),可以使用一个邻接表来表示,数据结构与构造方法如下

    ```
    //稀疏图-邻接表
    class SparseGraph {
    private:
        //节点数,变数
        int n,m;
        //是不是有向图
        bool directed;
        // 图的具体数据
        vector<vector<int> > g;
    public:
        SparseGraph(int n, bool directed) {
            assert(n >= 0);
            this->n = n;

            //初始化没有边
            this->m = 0;

            this->directed =directed;

            // g初始化为n个空的vector, 表示每一个g[i]都为空, 即没有任和边
            g = vector<vector<int> >(n, vector<int>());
        }
    }
    ```


  * 稠密图(DenseGraph),可以使用一个矩阵来表示,数据结构与构造方法如下

    ```
    //稠密图-邻接矩阵
    class DenseGraph {
    private:
        //节点数,变数
        int n,m;
        //是不是有向图
        bool directed;

        // 图的具体数据
        vector<vector<bool> > g;

    public:
        DenseGraph(int n, bool directed){
            assert(n >= 0);
            this->n = n;

            //初始化没有边
            this->m = 0;

            this->directed =directed;

            // g初始化为n*n的布尔矩阵, 每一个g[i][j]均为false, 表示没有任和边
            g = vector<vector<bool> >(n, vector<bool>(n, false));
        }
     }
    ```

    ​

* 插入一条边,建立一条边也就是建立两个点的关系

  * 稀疏图(SparseGraph)

    因为是邻接表的结构,所以,增加一条边就是新增一个元素到数组中

    ```
    void addEdge(int v, int w) {
      assert( v >= 0 && v < n );
      assert( w >= 0 && w < n );

      g[v].push_back(w);

      //v w不一样就处理了自环边
      if( v!= w && !directed) {
          g[w].push_back(v);
      }
      m++;
    }

    // 验证图中是否有从v到w的边
    bool hasEdge( int v , int w ){

      assert( v >= 0 && v < n );
      assert( w >= 0 && w < n );

      for( int i = 0 ; i < g[v].size() ; i ++ )
          if( g[v][i] == w )
              return true;
      return false;
    }
    ```

  * 稠密图(DenseGraph)

    矩阵的结构,就是改变矩阵中原来的布尔值, 暂时不处理自环边

    因为判断vw两者原本有没有边存在要进行一次遍历,所以添加一条新边的时候暂时不判断

    ```
    void addEdge (int v, int w) {
      assert( v >= 0 && v < n );
      assert( w >= 0 && w < n );

      //如果已经有了
      if(hasEdge(v,w)) {
          return;
      }

      g[v][w] = true;

      if(!directed) {
          g[w][v] = true;
      }

      m ++;
    }

    bool hasEdge(int v, int w){
      assert( v >= 0 && v < n );
      assert( w >= 0 && w < n );
      return g[v][w];
    }
    ```

* 图的遍历

  图的遍历,可以分别遍历稀疏图与稠密图的二维数组,但是这样就不能统一使用接口,所以可以分别通过迭代器进行迭代

  ```
  // O(E)
  for( int v = 0 ; v < N ; v ++ ){
      cout<<v<<" : ";
      SparseGraph::adjIterator adj(g1, v);
      for (int w = adj.begin(); !adj.end(); w=adj.next()){
          cout<<w<<" ";
      }
      cout<<endl;
  }
  cout<<endl;
  cout<<endl;

  // O(V^2)
  for( int v = 0 ; v < N ; v ++ ){
      cout<<v<<" : ";
      DenseGraph::adjIterator adj( g2 , v );
      for( int w = adj.begin() ; !adj.end() ; w = adj.next() )
          cout<<w<<" ";
      cout<<endl;
  }
  ```

  迭代器生成具体规则如下

  - 稀疏图(SparseGraph)

    ```
    // 邻边迭代器, 传入一个图和一个顶点,
    // 迭代在这个图中和这个顶点向连的所有顶点
    class adjIterator {
    private:
        SparseGraph &G;
        int v;
        //迭代到哪里了
        int index;
    public:
        //:G(graph),因为传的是一个引用,所以可以这样去初始化
        adjIterator(SparseGraph &graph, int v):G(graph) {
            this->v = v;
            this->index = 0;
        }

        ~adjIterator(){}

        // 返回图G中与顶点v相连接的第一个顶点
        int begin(){
            index = 0;
            if(G.g[v].size() > 0){
                return G.g[v][index];
            }
            // 若没有顶点和v相连接, 则返回-1
            return -1;
        }

        // 返回图G中与顶点v相连接的下一个顶点
        int next(){
            index ++;
            if(G.g[v].size() > index) {
                return G.g[v][index];
            }
            // 若没有顶点和v相连接, 则返回-1
            return -1;
        }

        // 查看是否已经迭代完了图G中与顶点v相连接的所有顶点
        int end() {
            return G.g[v].size() <= index;
        }
    };
    ```

    ​

  - 稠密图(DenseGraph)

    ```
    // 矩阵迭代器, 传入一个图和一个顶点,
    // 迭代在这个图中和这个顶点向连的所有顶点
    class adjIterator {
    private:
        DenseGraph &G;
        int v;
        //迭代到哪里了
        int index;
    public:
        //:G(graph),因为传的是一个引用,所以可以这样去初始化
        adjIterator(DenseGraph &graph, int v):G(graph) {
            this->v = v;
            this->index = -1;
        }

        ~adjIterator(){}

        // 返回图G中与顶点v相连接的第一个顶点
        int begin(){
            index = -1;
            return next();
        }

        // 返回图G中与顶点v相连接的下一个顶点
        int next(){
            // 若没有顶点和v相连接, 则返回-1
            for ( index +=1; index < G.V() ; index++) {
                if(G.g[v][index]){
                    return index;
                }
            }
            return -1;
        }

        // 查看是否已经迭代完了图G中与顶点v相连接的所有顶点
        int end() {
            return index >= G.V();
        }
    };
    ```

    ​

































* ​
* 插入一条边,建立一条边也就是建立两个点之间的关系
  * 稀疏图(SparseGraph)
  * 稠密图(DenseGraph)