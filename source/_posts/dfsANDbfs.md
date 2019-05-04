---
title: BFS,DFS经典算法与模板
date: 2019-05-04 21:05:07
categories: 经典算法与模板
---

> BFS模板

```c++
void BFS(int s){
	queue<int> q;
	q.push(s); 
	while(!q.empty()){
		取出队首元素top;
		访问队首元素top；
		将队首元素出队；
		将top的下一层结点未曾入队的结点全部入队，并设置为已入队
	}
} 
```



> BFS例题1

**问题描述**

给出mxn的矩阵，矩阵中的元素为0或者1，称位置与其上下左右四个位置是相邻的。如果矩阵中的若干个1是相邻的，那么称这些1构成了一个块。求给定矩阵中块的个数

```c++
#include <iostream>
#include<cstdio>
#include<queue>
using namespace std;

const int maxnum=100;

int m,n;     //m行n列的矩阵
int a[maxnum][maxnum];     //矩阵
int isInQueue[maxnum][maxnum]={false};   //是否入队
struct node{   //表示位置 
	int x;
	int y;
}Node; 

int X[4]={0,0,1,-1};   //增量数组，模拟上下左右 
int Y[4]={1,-1,0,0};

bool judge(int x,int y){   //判断坐标（x,y）是否需要访问 
	if(x<0||y<0||x>=m||y>=n)   //越界 
	return false;
	if(a[x][y]==0||isInQueue[x][y]==true)  //位置为0或者（x,y)已入队 
	return false;
	return true;
} 

void BFS(int x,int y){
	queue<node> q;      
	Node.x=x;
	Node.y=y;
	q.push(Node);
	isInQueue[x][y]=true;
	while(!q.empty()){
		node top=q.front();
		q.pop();          //队首元素出队 
		for(int i=0;i<4;i++){     //模拟4个相邻方向 
			int nexX=top.x+X[i];
			int nexY=top.y+Y[i];
			if(judge(nexX,nexY)){    //如果新位置需要访问 
				Node.x=nexX;
				Node.y=nexY;
				q.push(Node);       //将节点加入队列 
				isInQueue[nexX][nexY]=true;  //设置已经入队 
			} 
		}
	}
}

 
int main(int argc, char *argv[]) {
	printf("请输入矩阵的行数m,列数n:\n");
	scanf("%d%d",&m,&n);
	printf("请输入矩阵：\n");
	for(int i=0;i<m;i++){
		for(int j=0;j<n;j++){
			scanf("%d",&a[i][j]);
		}
	} 
	int sum=0;    //记录块数 
	for(int i=0;i<m;i++){
		for(int j=0;j<n;j++){
			if(a[i][j]==1&&isInQueue[i][j]==false){
				sum++;
				BFS(i,j);
			} 
		}
	}
	
	printf("%d",sum);    //输出块数 
	return 0;
}
```



**调试结果**

![QQ截图20190503200057](https://github.com/lamback/-/raw/master/QQ%E6%88%AA%E5%9B%BE20190503200057.png)





**DFS实现**

```c++
#include <iostream>
#include<cstdio>
#include<queue> 
using namespace std;

const int maxnum=100;
int a[maxnum][maxnum];     //矩阵
int hashTable[maxnum][maxnum]={false};

int X[4]={0,0,1,-1};   //增量数组 
int Y[4]={1,-1,0,0};

void DFS(int x,int y){
	                         //递归结束条件为，该块中所有的已访问 
	hashTable[x][y]=true;     //设置为已经访问 
	for(int i=0;i<4;i++){    //和BFS的差别：先访问一个方向，递归完，再进入另一个方向 
		int newX=x+X[i];
		int newY=y+Y[i];
		if(a[newX][newY]==1&&hashTable[newX][newY]==false)  //如果为1，且未访问过，则递归 
		DFS(newX,newY);
	}
} 

int main(int argc, char *argv[]) {
	int n,m;
	printf("请输入矩阵的行数m,列数n:\n");
	scanf("%d%d",&m,&n);
	printf("请输入矩阵：\n");
	for(int i=0;i<m;i++){
		for(int j=0;j<n;j++){
			scanf("%d",&a[i][j]);
		}
	} 
	int sum=0;    //记录块数 
	
	for(int i=0;i<m;i++){
		for(int j=0;j<n;j++){
			if(a[i][j]==1&&hashTable[i][j]==false){
				sum++;
				DFS(i,j);
			}
		}
	}
	
	
	printf("%d",sum);    //输出块数 
	return 0;
}
```



**调试结果**

![QQ截图20190503223116](https://github.com/lamback/-/raw/master/QQ%E6%88%AA%E5%9B%BE20190503223116.png)

> BFS例题2

**问题描述**

给定一个m*n大小的迷宫，其中 * 代表不可通过的墙壁，而“  .  “代表平地，S表示起点，T代表终点，移动过程中，如果当前位置是（x,y)（下表从0开始）,且每次只能前往上下左右四个位置的平地，求从起点到达终点T的最少步数。

```c++
#include <iostream>
#include<queue>
#include<cstdio>
using namespace std; 

const int maxnum=100;

char a[maxnum][maxnum];    //存储迷宫信息
int hashTable[maxnum][maxnum]={false};    //是否已经入队 
int m,n;                    //迷宫的行数和列数 

struct node{
	int x; 		//位置(x,y) 
	int y;
	int step;   //记录步数 
}S,T,Node;      //起点S，终点T，临时结点Node 

int X[4]={0,0,1,-1};  //增量数组 
int Y[4]={1,-1,0,0};

bool test(int xx,int yy){      //测试位置是否有效 
	if(xx<0||yy<0||xx>=m||yy>=n)    //越界 
		return false;		
	if(a[xx][yy]=='*')          //墙壁，不能通过 
		return false; 
	if(hashTable[xx][yy]==true)   //已入队 
		return false;
	return true; 
}

int BFS(){
	queue<node> q;
	q.push(S);                 //起点入队 
	while(!q.empty()){
		node temp=q.front();         //取出首元素 
		hashTable[temp.x][temp.y]=true;
		q.pop();
		if(temp.x==T.x&&temp.y==T.y)    //终点，返回步数 
			return  temp.step;
		for(int i=0;i<4;i++){      //得到相邻位置 
			int newX=temp.x+X[i];
			int newY=temp.y+Y[i];
			if(test(newX,newY)){
				Node.x=newX;
				Node.y=newY;
				Node.step=temp.step+1;
				q.push(Node);
			} 
		}
	}
	return -1;         //无法到达 
}


int main(int argc, char *argv[]) {
	printf("请输入矩阵的行数m,列数n:\n");
	scanf("%d%d",&m,&n);
	printf("请输入矩阵：\n");
	for(int i=0;i<m;i++){
		getchar();        //过滤换行符 
		for(int j=0;j<n;j++){
			scanf("%c",&a[i][j]);
		}
	} 	
	printf("请输入起点，终点坐标：");
	scanf("%d%d%d%d",&S.x,&S.y,&T.x,&T.y);
	S.step=0;       //初始化步数 
	 
	printf("%d",BFS());
	return 0;
}
```



**调试结果**

![QQ截图20190503231540](https://github.com/lamback/-/raw/master/QQ%E6%88%AA%E5%9B%BE20190503231540.png)



