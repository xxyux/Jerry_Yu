# Jerry_Yu
Something need to be noted

#include <iostream>
#include "vector"
#include "string"
#include "algorithm"
#include "queue"
#include "unordered_map"

#define ll long long
#define inf 0x3f3f3f3f
#define ls st<<1
#define rs st<<1|1
#define mid (l+r)/2
using namespace std;
//线段树
#define MAX 500500
int deg[MAX*4];
int lazy[MAX*4];
void push_up(int st){//上溯
    deg[st]=deg[ls]+deg[rs];
}
void push_down(int st,int ln,int rn){//下溯
    if(lazy[st]){
        lazy[ls]+=lazy[st];
        lazy[rs]+=lazy[st];
        deg[ls]+=lazy[st]*ln;
        deg[rs]+=lazy[st]*rn;
        lazy[st]=0;
    }
}
void build(int st,int l,int r){//建树
    lazy[st]=0;
    if(l==r) {
        deg[st]=0;
        return;
    }
    build(ls,l,mid);
    build(rs,mid+1,r);
    push_up(st);
}
int ask_point(int pos,int st,int l,int r){//点查询
    if(l==r&&l==pos) {
        return deg[st];
    }
    push_down(st,mid-l+1,r-mid);
    if(pos<=mid) return ask_point(pos,ls,l,mid);
    else return ask_point(pos,rs,mid+1,r);
}
void change_point(int x,int pos,int st,int l,int r){//点修改
    if(l==r&&l==pos) {
        deg[st]+=x;
        return;
    }
    push_down(st,mid-l-1,r-mid);
    if(pos<=mid) change_point(x,pos,ls,l,mid);
    else change_point(x,pos,rs,mid+1,r);
    push_up(st);
}
int ask_interval(int tl,int tr,int st,int l,int r){//区间查询
    if(tl<=l&&r<=tr) {
        return deg[st];
    }
    push_down(st,mid-l-1,r-mid);
    int res=0;
    if(tl<=mid) res+=ask_interval(tl,tr,ls,l,mid);
    if(mid<tr) res+=ask_interval(tl,tr,rs,mid+1,r);
    return res;
}
void change_interval(int x,int tl,int tr,int st,int l,int r){//区间修改
    if(tl<=l&&r<=tr) {
        deg[st]+=x*(r-l+1);
        lazy[st]+=x;
        return;
    }
    push_down(st,mid-l+1,r-mid);
    if(tl<=mid) change_interval(x,tl,tr,ls,l,mid);
    if(tr>mid) change_interval(x,tl,tr,rs,mid+1,r);
    push_up(st);
}

//单源最短路，时间复杂度O(N^2)
int n,m;
int v[200][200];//邻接矩阵
int dis[200];//答案数组
int f[200];//标记数组

void dj(){
    int i,a;
    int min_;
    int pos=1;
    for(i=1;i<=n;i++){//初始化dis数组
        if(v[1][i]>0) dis[i]=v[1][i];
    }
    for(i=0;i<n-1;i++){//找到剩余n-1个点的最短路
        min_=inf;
        for(a=1;a<=n;a++){//找已知没有用过最短节点pos
            if(f[a]==0) {
                if(min_>dis[a]){
                    min_=dis[a];
                    pos=a;
                }
            }
        }
        f[pos]=1;
        for(a=1;a<=n;a++){//贪心外延
            if(v[pos][a]>0){
                dis[a]=min(dis[a],dis[pos]+v[pos][a]);
            }
        }
    }
}

int main(){
    while(scanf("%d%d",&n,&m)!=EOF&&(n!=0||m!=0)){
        int i,a,x,y,z;
        memset(v,0,sizeof(v));
        memset(dis,inf,sizeof(dis));
        memset(f,0,sizeof(f));
        f[1]=1;
        for(i=0;i<m;i++){
            scanf("%d%d%d",&x,&y,&z);
            v[x][y]=z;
            v[y][x]=z;
        }
        dj();
        cout<<dis[n]<<endl;
    }
}
