---
layout: post
title: ⛓️ 基于邻接表的图的算法
categories: codetips 
---

## 邻接表定义

```c++
#define MAX_VERTERX_NUM 100
typedef char VertexType;
typedef struct ArcNode {
	int adjvex;
	struct ArcNode *nextarc;
} ArcNode;
typedef struct VNode{
	VertexType data;
	ArcNode *firstarc;
} VNode, AdjList[MAX_VERTERX_NUM];
typedef struct {
	AdjList vertices;
	int vexnum, arcnum;
} ALGraph;
```

## 建立有向表

```c++
void createALGraph(ALGraph &G) {
	ArcNode *p;
	cout<<"请输入顶点数和边数"<<endl;
	cin>>G->vexnum>>G->arcnum; //输入顶点数和边数
	cout<<"请输入顶点"<<endl;
	for(int i=0; i<G->vexnum; i++) {
		cin>>G->vertices[i].data; //输入顶点信息
		G->vertices[i].firstarc = NULL;
	}
	cout<<"请输入边(vi,vj)的顶点的下标"<<endl;
	for(int k=0; k<G->arcnum; k++) {
		cin>>i>>j; //输入顶点序列(即建边)
		p = new ArcNode;
		p->adjvex = j;
		p->nextarc = G->vertices[i].firstarc;
		G->vertices[i].firstarc = p;
		//若是无向图，再建一条(j,i)边即可
	}
}
```

## 删除有向图的一条弧,传参为<i,j>

```c++
void removeArc(ALGraph &G, int i, int j) {
	if(i<0||j<0)
		return;
	ArcNode *pre = NULL;
	ArcNode *p = G->vertices[i].firstarc;
	while(p) {
		if(p->adjvex==j) {
			if(pre==NULL)
				G->vertices[i].firstarc = p->nextarc;
			else
				pre->nextarc = p->nextarc;
			delete p; //释放边结点
		}
		else {
			pre = p;
			p = p->nextarc;
		}
	} 
	G->arcnum--;
}
```

## 删除有向图的一条弧,传参为<v1,v2>

```c++
int Locate_Vex(ALGraph G, char ch) {
	for (int i=0; i<G.vexnum; i++) {
		if(G.vertices[i].data==ch) 
			return i;
	}
	return -1;
}
void removeArc(ALGraph &G, char v1, char v2) {
	ArcNode *pre = NULL;
	ArcNode *p = G->vertices[i].firstarc;
	int i = Locate_Vex(G, v1);
	int j = Locate_Vex(G, v2);
	if(i<0||j<0)
		return;
	while(p) {
		if(p->adjvex==j) {
			if(pre==NULL)
				G->vertices[i].firstarc = p->nextarc;
			else
				pre->nextarc = p->nextarc;
			delete p; //释放边结点
		}
		else {
			pre = p;
			p = p->nextarc;
		}
	} 
	G->arcnum--;
}
```

## 求某顶点的入度

```c++
int countIndegree(ALGraph G, int k) {
	int cnt = 0;
	ArcNode *p;
	for (int i = 0; i < G.vexnum; ++i) {
		for(p=G.vertices[i].firstarc; p; p=p->nextarc) {
			if(p->adjvex==k) //遍历整个边表
				cnt++;
		}
	}
	return cnt;
}
```

## 求某顶点的出度

```c++

int countOutdegree(ALGraph G, int k) {
	int cnt = 0;
	ArcNode *p = G.vertices[k].firstarc;
	while(p) {
		cnt++; //边表结点个数即为出度
		p = p->nextarc;
	}
	return cnt;
}
```

## 基于邻接表的深度优先搜索

```c++
bool visited[MAX_VERTERX_NUM]; //访问标记
void DFS(ALGraph G, int v) {
	ArcNode *p; //声明一个指向边结点的工作指针
	visit(v); //根据题目描述修改函数即可
	visited[v] = TRUE; //访问标记置为TRUE
	for(p=G.vertices[i].firstarc; p; p=p->nextarc) {
		if(!visited[p->adjvex])//若顶点未被访问过
			DFS(G,p->adjvex); //递归调用
	}
}

/*当图不是连通图时需要调用本函数*/
void DFSTraverse(ALGraph G) {
	for (int i = 0; i < G.vexnum; ++i)
		visited[i] = FALSE; //初始化标记数组
	for (int i = 0; i < G.vexnum; ++i) {
		/*针对非连通图,这样就实现了遍历下一个连通分量的遍历*/
		if(!visited[i]) //顶点未被访问
			DFS(G,i);
	}
}
```

## 基于邻接表的广度优先搜索

```c++
bool visited[MAX_VERTERX_NUM]; //访问标记
void BFS(ALGraph G, int v) {
	InitQueue(Q); //初始化一个队列,王道上是在Traverse函数里初始化的,可我觉得这里更合适
	ArcNode *p; //声明一个指向边结点的工作指针
	visit(v); //根据题目描述修改函数即可
	visited[v] = TRUE; //访问标记设为TRUE
	EnQueue(Q,v); //将访问顶点入队
	while(!IsEmpty(Q)) { 
		DeQueue(Q,i); //取队头元素
		for(p=G.vertices[i].firstarc; p; p=p->nextarc){
			if(!visited[p->adjvex]) {
				visit(p->adjvex); //根据题目描述修改函数即可
				visited[p->adjvex] = TRUE; //访问标记设为TRUE
				EnQueue(Q,p->adjvex);
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

## 基于邻接表的拓扑排序

```c++
//存放顶点入度信息的数组(假设题目已给出，一般都给出不然手撸代码工作量太大了)
int indegree[MAX_VERTERX_NUM]; 
bool TopoSort(ALGraph G) {
	InitStack(S); //初始化一个栈
	ArcNode *p; //声明一个指向边结点的工作指针
	for(int i=0; i<G.vexnum; i++) {
		if(indegree[i]==0)
			Push(S,i); //将入度为0的顶点入栈
	}
	int cnt = 0; //计数,记录当前输出的顶点数
	while(!IsEmpty(S)) { //栈不空,则存在入度为0的顶点
		Pop(S,i); //取栈顶元素
        cout<<G.vertices[i].data;//输出序号为i的顶点
		cnt++; //计数
		for(p=G.vertices[i].firstarc; p; p=p->nextarc) {
		/*将所有i指向的顶点入度减1,并且将入度减为0的顶点压入栈S*/
			int v = p->adjvex;
			if(!(--indegree[v]))
				Push(S,v);
		}
	}
	if(cnt<G.vexnum)
		return FALSE; //得到的拓扑序列顶点个数小于图的顶点个数,说明图中存在回路
	else
		return TRUE;
}
```

