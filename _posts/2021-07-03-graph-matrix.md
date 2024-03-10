---
layout: post
title: 🔳 基于邻接矩阵的图的算法
categories: codetips
---

## 邻接矩阵定义

```c++
#define MAX_VERTERX_NUM 100
typedef char VertexType;
typedef int EdgeType;
typedef struct {
	VertexType vex[MAX_VERTERX_NUM];
	EdgeType arcs[MAX_VERTERX_NUM][MAX_VERTERX_NUM];
	int vexnum, arcnum;
} MGraph;
```

## 建立有向图

```c++
void createMGraph(MGraph &G) {
	int i, j, k;
	cout<<"请输入顶点数和边数"<<endl;
	cin>>G->vexnum>>G->arcnum; //输入顶点数和边数
	cout<<"请输入顶点"<<endl;
	for(i=0;i<G.vexnum;i++)
        cin>>G.vex[i];
    /*初始化二维数组*/ 
    for(i=0;i<G.n;i++)
        for(j=0;j<G.n;j++)
        		G.edge[i][j]=0;
 
     cout<<"请输入弧度<vi,vj>的顶点的下标"<<endl;
     for(int k=0; k<G.arcnum; k++) {
     	cin>>i>>j;
     	G->arcs[i][j] = 1;
     }
 }
```

## 删除有向图的一条弧,传参为<i,j>

```c++
void removeArc(MGraph &G, int i, int j) {
 	if(i<0||j<0)
		return;
 	if(G->arcs[i][j]==1)
 		G->arcs[i][j] = 0;
 	G->arcnum--;
 }
```

## 删除有向图的一条弧,传参为<v1,v2>

```c++
int Locate_Vex(MGraph G, char ch) {
	for (int i=0; i<G.vexnum; i++) {
		if(G.vex[i]==ch) 
			return i;
	}
	return -1;
}
void removeArc(MGraph &G, char v1, char v2) {
	int i = Locate_Vex(G,v1);
	int j = Locate_Vex(G,v2);
	if(i<0||j<0)
		return;
 	if(G->arcs[i][j]==1)
 		G->arcs[i][j] = 0;
 	G->arcnum--;
 }
```

## 求某顶点的入度

```c++
int countIndegree(MGraph G, int k) {
	int cnt = 0;
	for(int i=0; i<G.vexnum; i++)
		if(G.arcs[i][k]==1)
			cnt++;
	return cnt;
}
```

## 求某顶点的出度

```c++
int countOutdegree(ALGraph G, int k) {
	int cnt = 0;
	for(int i=0; i<G.vexnum; i++)
		if(G.arcs[k][i]==1)
			cnt++;
	return cnt;
}
```

## 基于邻接矩阵的深度优先搜索

```c++
bool visited[MAX_VERTERX_NUM]; //访问标记
void DFS(MGraph G, int v) {
	visit(v); //根据题目描述修改函数即可
	visited[v] = TRUE; //访问标记置为TRUE
	for(int i=0; i<G.vexnum; i++) {
		if(！visited[i]&&G.arcs[v][i]==1) {
			DFS(G,i);
		}
	}
}

/*当图不是连通图时需要调用本函数*/
void DFSTraverse(MGraph G) {
	for (int i = 0; i < G.vexnum; ++i)
		visited[i] = FALSE; //初始化标记数组
	for (int i = 0; i < G.vexnum; ++i) {
		/*针对非连通图,这样就实现了遍历下一个连通分量的遍历*/
		if(!visited[i]) //顶点未被访问
			DFS(G,i);
	}
}
```

## 基于邻接矩阵的广度优先搜索

```c++
bool visited[MAX_VERTERX_NUM]; //访问标记
void BFS(MGraph G, int v){
	visit(v); //根据题目描述修改函数即可
	visited[i] = TRUE;
	InitQueue(Q); //初始化一个队列
	EnQueue(Q,v);
	while(!IsEmpty(Q)) {
		DeQueue(Q,v);
		for(int i=0; i<G.vexnum; i++) {
			if(arcs[v][i]==1) {
				visit(i);
				visited[i] = TRUE;
				EnQueue(Q,i);
			}
		}
	}
}

/*当图不是连通图时需要调用本函数*/
void BFSTraverse(ALGraph G) {
	for (int i = 0; i < G.vexnum; ++i)
		visited[i] = FALSE; //初始化标记数组
	for (int i = 0; i < G.vexnum; ++i) {
		/*针对非连通图,这样就实现了遍历下一个连通分量的遍历*/
		if(!visited[i]) //顶点未被访问
			BFS(G,i);
	}
}
```

## 基于邻接矩阵的拓扑排序

```c++
int indegree[MAX_VERTERX_NUM]; //存放顶点入度信息的数组
bool TopoSort(MGraph G) {
	InitStack(S);//初始化一个栈
	for(int i=0; i<G.vexnum; i++) {
		if(indegree[i]==0)
			Push(S,i);
	}
	int cnt = 0;//计数,记录当前输出的顶点数
	while(!IsEmpty(S)) { //栈不空,则存在入度为0的顶点
		Pop(S,i); //取栈顶元素
		cnt++;//计数
        count<<G.vex[i];//输出序号为i的顶点
		for(int j=0; j<G.vexnum; j++) {
		将所有i指向的顶点入度减1,并且将入度减为0的顶点压入栈S
			if(!(--indegree[j]))//i和j是邻接的,且j的入度变为0
				Push(S,j);
		}
	if(cnt<G.vexnum)
		return FALSE; //得到的拓扑序列顶点个数小于图的顶点个数,说明图中存在回路
	else
		return TRUE;
}
```