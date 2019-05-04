---
title: 递归经典算法与模板
date: 2019-05-04 20:58:45
categories: 经典算法与模板
---

> 说明

**本次实验主要利用分治、散列、递归，回溯、深度优先思想，完成n的阶乘、全排列问题、n皇后问题的C语言代码编写以及优化，解决问题难度逐渐升高。完成自主代码编写之后，最后分析代码的优缺点**



> 内容

#### **1.n的阶乘**

**分析**:递归主要需要找出递归边界和递归式，n！=1* 2* 3 *4 ... * n，所以写出递推的形式为n!=(n-1)! * n，递归边界即为到乘以1。

```c++
#include <iostream>
#include<cstdio> 
using namespace std; 

long long  F(long long n){      //使用long long存储，提高程序计算能力
	if(n==0)    //递归边界 ，到达及返回1 
	return 1;
	else
	return n*F(n-1); //递归式 
}

int main(int argc, char *argv[]) {
	int n;
	printf("请输入需要计算的正数n:");
	scanf("%lld",&n);
	printf("%d的阶乘为：%lld",n,F(n));
	
	return 0;
}
```



**优缺点:**

1.用long long 类型存储结果，可以有效的提升算法的计算能力







#### **2.全排列（输出从1~n的全排列）**

**分析**
问题为”输出1-n这n个整数的全排列“，那么它就可以被分为若干个子问题：”输出以1开头的全排列“，”输出以2开头的全排列“......”输出以n开头的全排列“。这就是递归的思想，递归边界是递归位置到达最后，进入下一层递归的条件是枚举所以可以填入的数，如果该数未使用过，则处理下一位（即进行递归），其中使用散列表的值确定是否使用过；当递归结束后，还需要恢复散列表的值。

```c++
#include <iostream>
#include<cstdio>
using namespace std; 

const int maxnum=100;  
int hashTable[maxnum]={false};  //用于表示数字是否已经在此种排列中的散列表 
int result[maxnum]={0};      //临时存放结果的数组 

void generate(int pos,int n){  
	if(pos==n+1){         //递归边界，到达一个结果的边界，打印结果并返回 
		for(int i=1;i<=n;i++){
			printf("%d ",result[i]);
		}
		printf("\n");
		return;
	}
	for(int j=1;j<=n;j++){   //枚举1~n，看是否可以填入 
		if(hashTable[j]==false){   //只有j未使用过才能填入 
			result[pos]=j;
			hashTable[pos]=true; 
			generate(pos+1,n);   //递归处理pos+1位置 
			hashTable[pos]=false; //恢复状态 
		}
	}
} 

int main(int argc, char *argv[]) {
	int n;
	printf("请输入整数n:");
	scanf("%d",&n);
	generate(1,n); 
	return 0;
}
```




**存在的问题**
只能1——n的全排列，没有普遍性，上面这个算法主要在于递归与散列数组的使用 ，还实现了按字典序从小到大输出全排列 



**改进思路**
将算法的条件改为输入任意个序列，输出该序列的全排列和总数



**分析**
输入的任意个序列，通过数组保存下来，为保证实现字典序从小到大的输出，需要先进行排序；改用数组指针后移实现递归前进；递归边界是到达数组的最后一个位置，在特定位置上枚举所有在其位置后面的数，判断该数是否可以填入，如果可以，则交换至该位置，并进入下一层递归

```c++
#include <iostream>
#include<cstdio>
#include<algorithm>
using namespace std;

const int maxnum=100;
int a[maxnum]={-1};    //存放数列的数组
int sum=0;             //结果数量 

void print(int a[],int q){   //打印输出 
	for(int i=0;i<=q;i++){
		printf("%d ",a[i]);
	} 
	printf("\n");
}

void swap(int a[],int p,int i){ //交换i,p位置上的两个数 
	int temp=a[p];
	a[p]=a[i];
	a[i]=temp;
}

void generate(int a[],int p,int q){
	if(p==q){     //到达递归边界，打印结果，并计数 
		print(a,q);
		sum++;
		return; 
	}
	for(int i=p;i<=q;i++){   //枚举数组位置及以后的所有数 
		swap(a,p,i);
		generate(a,p+1,q);    //进入下一层循环
		swap(a,p,i);         //恢复状态 
	}
	
} 

int main(int argc, char *argv[]) {
	int n;    //序列数量
	
	scanf("%d",&n);
	printf("请输入序列：\n"); 
	for(int i=0;i<n;i++){
		scanf("%d",&a[i]);
	} 
	printf("全排列结果如下：\n"); 
	sort(a,a+n);      //STL库中的排序函数 
	
	generate(a,0,n-1);
	printf("结果总数为：%d",sum);
	return 0;
}
```



**存在的问题**
未考虑如果序列中存在重复是数字，如果出现重复的数字，结果将出问题



**改进思路**
在枚举的过程中，添加判断函数，让从第一个数字起每个数分别与它后面非重复出现的数字交换。

```c++
#include <iostream>
#include<cstdio>
#include<algorithm>
using namespace std;

const int maxnum=100;
int a[maxnum]={-1};    //存放数列的数组
int sum=0;             //结果数量 

void print(int a[],int q){   //打印输出 
	for(int i=0;i<=q;i++){
		printf("%d ",a[i]);
	} 
	printf("\n");
}

void swap(int a[],int p,int i){ //交换i,p位置上的两个数 
	int temp=a[p];
	a[p]=a[i];
	a[i]=temp;
}

bool isswap(int a[],int q,int i){
	for(int j=i+1;j<=q;j++){
		if(a[j]==a[i])
		return false;
	}
		return true;
}

void generate(int a[],int p,int q){
	if(p==q){     //到达递归边界，打印结果，并计数 
		print(a,q);
		sum++;
		return; 
	}
	for(int i=p;i<=q;i++){   //枚举数组位置及以后的所有数 
		if(isswap(a,q,i)){		//解决重复问题：从第一个数字起每个数分别与它后面非重复出现的数字交换
			swap(a,p,i);
			generate(a,p+1,q);    //进入下一层循环
			swap(a,p,i);         //恢复状态 
		}
	}
	
} 

int main(int argc, char *argv[]) {
	int n;    //序列数量
	
	scanf("%d",&n);
	printf("请输入序列：\n"); 
	for(int i=0;i<n;i++){
		scanf("%d",&a[i]);
	} 
	printf("全排列结果如下：\n"); 
	sort(a,a+n);      //STL库中的排序函数 
	
	generate(a,0,n-1);
	printf("结果总数为：%d",sum);
	return 0;
}
```







#### **3.n的皇后问题**

**问题描述**
在n*n的国际象棋棋盘上放置n个皇后，使得这n个皇后两两均不在同一行、同一列、同一条对角线上，其合法的方案数



**分析**
如果采用组合数的方式来枚举每一种情况，那么枚举量会特别大；所以换一种思路，考虑到每行只能放置一个皇后，那么如果n行皇后的列数依次写出来，那么就会是1~n的一个排列；将二维的问题转化为一维的排列问题

```c++
#include <iostream>
#include<cstdio>
#include<cmath> 
using namespace std; 

const int maxnum=100;
int a[maxnum]={0};    //暂存结果数组 
int hashTable[maxnum]={false}; //散列表表示列数是否可用 
int sum=0;     		  //方案数
 
void print(int n){   //打印输出结果函数 
	for(int i=1;i<=n;i++){
		printf("%d ",a[i]);
	}
	printf("\n");
}

void generate(int p,int n){
	if(p==n+1){     //递归边界 
		for(int j=1;j<n;j++){  //遍历任意两个皇后 
			for(int k=j+1;k<=n;k++){
				if(abs(j-k)==abs(a[j]-a[k])) //如果在同一对角线上，直接返回 
				return;
			}
		}
		sum++; 
		print(n);     //打印输出合法结果 
		return;
	}
	for(int i=1;i<=n;i++){   //枚举列数，该列未填数，则填入进行下一层递归 
		if(hashTable[i]==false){
			hashTable[i]=true;
			a[p]=i;
			generate(p+1,n);
			hashTable[i]=false;  //恢复未填状态 
		} 	
	}
} 
 
int main(int argc, char *argv[]) {
	int n;    //皇后数 
	printf("请输入你想求解的皇后数:\n");
	scanf("%d",&n);
	generate(1,n);
	
	printf("全部结果数：");
	printf("%d",sum);
	return 0;
}
```





**存在问题**
在调试该程序过程中发现当计算到n=12时，该程序已经无法迅速给出结果了；所以该程序的时间复杂度有待提高



**改进思路**
上面程序的思路是枚举所有情况，然后再判断这种情况是否合法。事实上，通过思考可以发现，当已经放置了一部分皇后时（对应生成了一部分排列），可能剩下的皇后无论怎么放置都不合法，此时已经没有必要再递归下去，直接返回。也就是采用回溯法优化，将判断合法条件在递归中判断。

```c++
#include <iostream>
#include<cstdio>
#include<cmath> 
using namespace std; 

const int maxnum=100;
int a[maxnum]={0};    //暂存结果数组 
int hashTable[maxnum]={false}; //散列表表示列数是否可用 
int sum=0;     		  //方案数
 
void print(int n){   //打印输出结果函数 
	for(int i=1;i<=n;i++){
		printf("%d ",a[i]);
	}
	printf("\n");
}

bool islegal(int p,int j){       //回溯判断条件，是否在这同一对角线上 
	for(int i=1;i<p;i++){
		if(abs(p-i)==abs(j-a[i]))
		return false;
	}
	return true;
} 

void generate(int p,int n){
	if(p==n+1){     //递归边界 
	/*	for(int j=1;j<n;j++){  //遍历任意两个皇后 
			for(int k=j+1;k<=n;k++){
				if(abs(j-k)==abs(a[j]-a[k])) //如果在同一对角线上，直接返回 
				return;
			}
		}
	*/
		sum++; 
		//print(n);     //打印输出合法结果 
		return;
	}
	for(int i=1;i<=n;i++){   //枚举列数，该列未填数，则填入进行下一层递归 
		if(hashTable[i]==false){
			if(islegal(p,i)){        //增加回溯判断 
				hashTable[i]=true;
				a[p]=i;
				generate(p+1,n);
				hashTable[i]=false;  //恢复未填状态 
			} 
		} 	
	}
} 
 
int main(int argc, char *argv[]) {
	int n;    //皇后数 
	printf("请输入你想求解的皇后数:\n");
	scanf("%d",&n);
	generate(1,n);
	
	printf("全部结果数：");
	printf("%d",sum);
	return 0;
}
```



**调试结果：**

已经可以快速算出14皇后问题，时间复杂度明显降低



**优缺点：**

1.枚举实现时间复杂度高，且代码编写难度要比递归实现大

2.我还是认为函数不要太大，需要在主要函数中体现清晰的逻辑

3.使用数组+标志位的方法判断该数是否被使用过，我觉得没有使用上面散列表的方式简单清晰，这也是引入散列表的优势所在

4.都进行的优化，使其能够尽快计算到14皇后问题