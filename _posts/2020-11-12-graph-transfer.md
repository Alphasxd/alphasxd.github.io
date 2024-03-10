---
layout: post
title: 🔄 图的转换
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

## 有向图的邻接表转邻接矩阵

```c++
void Convert(ALGraph G, MGraph &M) {
	M->vexnum = G.vexnum; //顶点数赋值
	M->arcnum = G.arcnum; //边数赋值
	ArcNode *p;
	for(int i=0; i<G.vexnum; i++) {//依次遍历各顶点表结点为头的边链表
		M->vex[i] = G.vertices[i].data; //顶点信息赋值
		for(p=G.vertices[i].firstarc; p; p=p->nextarc) {
			M->arcs[i][p->adjvex] = 1;
		}
	}
}
```

## 有向图的邻接矩阵转邻接表

```c++
void Convert(MGraph M, ALGraph &G) {
	G->vexnum = M.vexnum; //顶点数赋值
	G->arcnum = M.arcnum; //边数赋值
	ArcNode *p;
	for(int i=0; i<M.vexnum; i++) {
		G->vertices[i].data = M.vex[i]; //顶点信息赋值
		G->vertices[i].firstarc = NULL; //初始化firstarc指针
	}
	for(int i=0; i<M.vexnum; i++)
		for(int j=0; j<M.vexnum; j++)
			if(M.arcs[i][j]==1){
				p = new ArcNode;
				p->adjvex = j;
				p->nextarc = G->vertices[i].firstarc;
				G->vertices[i].firstarc = p;
			}
}
```

