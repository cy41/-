## 普通平衡树

支持新增，删除，询问排名，询问第k小，前驱，后继

```cpp
#include <bits/stdc++.h>
using namespace std;

typedef long long ll;

const int maxn=1e5+7;
const int inf=1e9;

//cnt为重复数量，siz为节点及子树大小
int cnt[maxn],siz[maxn],ch[maxn][2],f[maxn],val[maxn],tot;

bool which(int x){ return ch[f[x]][1]==x; }//1为右儿子;
int rt;

inline void connect(int son,int fa,int lr){
    ch[fa][lr]=son;
    f[son]=fa;
}

void newnode(int x){
    ++tot;
    cnt[tot]=siz[tot]=1;
    ch[tot][0]=ch[tot][1]=0;
    f[tot]=0;
    val[tot]=x;
}

int pre(){
    int now=ch[rt][0];
    while(ch[now][1]) now=ch[now][1];
    return now;
}

int nex(){
    int now=ch[rt][1];
    while(ch[now][0]) now=ch[now][0];
    return now;
}

void clear(int x){ cnt[x]=siz[x]=ch[x][0]=ch[x][1]=f[x]=val[x]=0; }
void pushup(int x){
    if(x){
        siz[x]=cnt[x];
        if(ch[x][0]) siz[x]+=siz[ch[x][0]];
        if(ch[x][1]) siz[x]+=siz[ch[x][1]];
    }
}

void rotate(int x){
    int old=f[x],oldf=f[old],who=which(x);
    connect(ch[x][who^1],old,who);
    if(oldf) connect(x,oldf,which(old));
    f[x]=oldf;
    connect(old,x,who^1);
    pushup(old),pushup(x);
}

void splay(int x){
    for(int fa;fa=f[x];rotate(x))
        if(f[fa]) rotate(which(fa)==which(x)?fa:x);
    rt=x;
}

void insert(int x){//x为权值;
    if(!rt){
        newnode(x);
        rt=tot;
        return ;
    }
    int now=rt,fa=0;
    while(1){
        if(x==val[now]){
            ++cnt[now];
            pushup(now);
            pushup(fa);
            splay(now);
            return ;
        }
        fa=now,now=ch[now][val[now]<x];
        if(!now){
            newnode(x);
            connect(tot,fa,val[fa]<x);
            pushup(fa);
            splay(tot);
            return ;
        }

    }
}

//查找排名为k的;
int kth(int k){

    int now=rt;
    while(now){
        if(ch[now][0]&&siz[ch[now][0]]>=k) now=ch[now][0];
        else{
            int temp=cnt[now]+siz[ch[now][0]];
            if(temp>=k) return val[now];
            k-=temp; now=ch[now][1];
        }
    }
    return -1;//找不到;
}

//查找x的排名
int getrank(int x){
    int ans=0;
    int now=rt;
    while(1){
        if(!now) return ans+1;
        if(x<val[now]) now=ch[now][0];
        else{
            ans+=siz[ch[now][0]];
            if(x==val[now]){
                splay(now);
                return ans+1;
            }
            ans+=cnt[now];
            now=ch[now][1];
        }
    }
}

void del(int x){
    getrank(x);
    if(val[rt]==x){
        if(cnt[rt]>1){
            --cnt[rt];
            pushup(rt);
            return ;
        }
        if(!ch[rt][0]&&!ch[rt][1]){
            clear(rt);
            rt=0;
            return ;
        }
        if(ch[rt][0]&&ch[rt][1]){
            int newrt=pre();
            int oldrt=rt;
            splay(newrt);
            connect(ch[oldrt][1],rt,1);
            clear(oldrt);
            pushup(rt);
        }
        else if(ch[rt][0]){
            int newrt=ch[rt][0];
            clear(rt);
            f[newrt]=0;
            rt=newrt;
        }
        else{
            int newrt=ch[rt][1];
            clear(rt);
            f[newrt]=0;
            rt=newrt;
        }

    }
}

void init(){
    tot=0;
    rt=0;
    //insert(-inf),insert(inf);
}

int main(){
    int q,op,x;
    init();
    scanf("%d",&q);
    while(q--){
        scanf("%d%d",&op,&x);
        if(op==1) insert(x);
        else if(op==2) del(x);
        else if(op==3) printf("%d\n",getrank(x));
        else if(op==4) printf("%d\n",kth(x));
        else if(op==5) {insert(x); printf("%d\n",val[pre()]); del(x); }
        else {insert(x); printf("%d\n",val[nex()]); del(x); }
    }

    return 0;
}

```



## poj 3580 对数列进行操作

对数列进行操作。

支持区间加法，区间翻转，区间轮换，某位置后插入，删除某个位置上的数，询问区间最小值

维护的信息，线段树是怎么维护的，平衡树就怎么维护！

```cpp
#include <bits/stdc++.h>
using namespace std;

typedef long long ll;

const int maxn=2e5+7;
const int inf=1e9;

int tot,rt;
void init(){tot=rt=0;}

int siz[maxn],val[maxn],rev[maxn],add[maxn],minn[maxn],ch[maxn][2],f[maxn],W[maxn];

bool which(int x){ return ch[f[x]][1]==x; }

void connect(int s,int fa,int lr){
    ch[fa][lr]=s;
    f[s]=fa;
}

void pushup(int k){
    if(k){
        minn[k]=val[k];
        siz[k]=1;
        if(ch[k][0]) siz[k]+=siz[ch[k][0]],minn[k]=min(minn[k],minn[ch[k][0]]);
        if(ch[k][1]) siz[k]+=siz[ch[k][1]],minn[k]=min(minn[k],minn[ch[k][1]]);
    }
}

void pushdown(int k){
    if(!k) return ;
    if(rev[k]){
        if(ch[k][0]) rev[ch[k][0]]^=1;
        if(ch[k][1]) rev[ch[k][1]]^=1;
        swap(ch[k][0],ch[k][1]);
        rev[k]=0;
    }
    if(add[k]){
        if(ch[k][0]) val[ch[k][0]]+=add[k],minn[ch[k][0]]+=add[k],add[ch[k][0]]+=add[k];
        if(ch[k][1]) val[ch[k][1]]+=add[k],minn[ch[k][1]]+=add[k],add[ch[k][1]]+=add[k];
        add[k]=0;
    }
}
queue<int> q;
int newnode(int w){
    int k;
    if(q.size()) { k=q.front(); q.pop(); }
    else k=++tot;
    rev[k]=add[k]=f[k]=ch[k][0]=ch[k][1]=0;
    minn[k]=val[k]=w;
    siz[k]=1;
    return k;
}

void clear(int k){
    rev[k]=add[k]=f[k]=ch[k][0]=ch[k][1]=minn[k]=val[k]=siz[k]=0;
    q.push(k);
}

int build(int l,int r,int fa){
    if(l>r) return 0;
    int mid=l+r>>1;
    int now=newnode(W[mid]);
    f[now]=fa;
    ch[now][0]=build(l,mid-1,now);
    ch[now][1]=build(mid+1,r,now);
    pushup(now);
    return now;
}

void rotate(int x){
    pushdown(f[x]),pushdown(x);
    int old=f[x],oldf=f[old],who=which(x);
    connect(ch[x][who^1],old,who);
    if(oldf) connect(x,oldf,which(old));
    f[x]=oldf;
    connect(old,x,who^1);
    pushup(old),pushup(x);
}

//将x转到pos的儿子处;
void splay(int x,int pos){
    for(int fa;(fa=f[x])!=pos;rotate(x))
        if(f[fa]!=pos) rotate(which(fa)==which(x)?fa:x);
    if(!pos) rt=x;
}

int kth(int x){
    int now=rt;
    while(1){
        pushdown(now);
        if(ch[now][0]&&x<=siz[ch[now][0]]) now=ch[now][0];
        else{
            x-=siz[ch[now][0]];
            if(x==1) return now;
            --x;
            now=ch[now][1];
        }

    }
    return -1;
}

pair<int,int> splayLR(int l,int r){
    int L=kth(l);
    int R=kth(r);
    splay(L,0);
    splay(R,L);
    return make_pair(L,R);
}
void add_to_some(int l,int r,int x){
    if(l>r) swap(l,r);
    pair<int,int> P=splayLR(l-1,r+1);
    int L=P.first,R=P.second;
    add[ch[R][0]]+=x;
    minn[ch[R][0]]+=x;
    val[ch[R][0]]+=x;
    pushup(R),pushup(rt);
}

void reverse(int l,int r){
    if(l>r) swap(l,r);
    pair<int,int> P=splayLR(l-1,r+1);
    int L=P.first,R=P.second;
    rev[ch[R][0]]^=1;
}

void insert(int pos,int x){
    pair<int,int> P=splayLR(pos,pos+1);
    int L=P.first,R=P.second;
    int now=newnode(x);
    connect(now,R,0);
    pushdown(R),pushup(rt);
}

void del(int pos){
    pair<int,int> P=splayLR(pos-1,pos+1);
    int L=P.first,R=P.second;
    clear(ch[R][0]);
    ch[R][0]=0;
    pushup(R),pushup(rt);
}

int askMin(int l,int r){
    if(l>r) swap(l,r);
    pair<int,int> P=splayLR(l-1,r+1);
    return minn[ch[P.second][0]];
}

void init(int n){
    init();
    W[1]=-inf;
    W[n+2]=inf;
    for(int i=2;i<=n+1;++i) scanf("%d",&W[i]);
    rt=build(1,n+2,0);
}

void exchange(int l,int r,int x){
    if(l>r) swap(l,r);
    x%=(r-l+1);
    if(!x) return ;
    int l1=l,r1=r-x,l2=r-x+1,r2=r;
    pair<int,int> pos2=splayLR(l2-1,r2+1);
    int L=pos2.first,R=pos2.second;
    int temp_r=ch[R][0]; ch[R][0]=0;
    pushup(R),pushup(rt);
    pair<int,int> pos1=splayLR(l1-1,l1);
    R=pos1.second;
    ch[R][0]=temp_r;
    f[temp_r]=R;
    pushup(R),pushup(rt);
}
char s[9];
/*
    A 区间加法
    D 删除下标为x的数
    M 区间最小值
    E 区间翻转
    else 区间轮换x次;
*/
int main(){
    int n,q,l,r,x;
    scanf("%d",&n);
    init(n);
    scanf("%d",&q);
    while(q--){
        scanf("%s",s);
        if(s[0]=='A'){ scanf("%d%d%d",&l,&r,&x); add_to_some(++l,++r,x); }
        else if(s[0]=='I'){ scanf("%d%d",&l,&x); insert(++l,x); }
        else if(s[0]=='D'){ scanf("%d",&x); del(++x); }
        else if(s[0]=='M'){ scanf("%d%d",&l,&r); printf("%d\n",askMin(++l,++r)); }
        else if(s[3]=='E'){ scanf("%d%d",&l,&r); reverse(++l,++r); }
        else{ scanf("%d%d%d",&l,&r,&x); exchange(++l,++r,x); }
    }

    return 0;
}
```



## 维护数列

区间覆盖，区间翻转，单点插入，区间求和，区间最大子段和。

由于该题区间覆盖时为$Z$，故还需要一个延迟标记来表示到底有没有覆盖操作。

区间最大子段和就像线段树一样即可。