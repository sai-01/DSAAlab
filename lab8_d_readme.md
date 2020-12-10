## Lab8 D

### **题目大意**
  
  - 输入t,k,u, 分别为**接收数字的时间**、**已接收的数字中第k大的数**、**第一个**$last\_ans$**的值**  
  
  - 第$$$i$$$秒接收的数字为$a(i+last\_ans)$  ，其中$a(n)=n+(n各位上数之和)$
  例如，$a(2)=2+2=4$, $a(10)=10+(1+0)=11$, $a(100)=100+(1+0+0)=101$  

  
- 每次接收完$last\_ans$更新为$a(n)$, 然后计算下一个数字
  
- 要求输出每100秒时，已接收数字中第k大的数  

样例的输入为1926 8 17  
  
  则先用$(1926/100)*100=1900$得到要处理的秒数（后面26秒就不管了  
  开始遍历$i$从1到1900  
  接收的第一个数字$=a(1+u)=a(1+17)=a(18)=18+(1+8)=27$  
  然后更新$last\_ans:=a(18)$  
  接着继续计算后续的数字  
  每当$i$为100的整数倍时($i\%100=0$)  
  找到前$i$个数中第8大的数并打印
  这个例子显然要有19个输出  

### 解法
#### 思路  
建一个大小为k的小顶堆，先接收k个数，小顶堆的root就是第k大的数，然后接收第k+1个数、第k+2个数、第k+3个数...每接收一个数就让它替换掉小顶堆中的一个数，这样小顶堆里就会一直维持k个数，结束后，root就是答案。

**代码**

  定义了需要的全局变量t, k, last_ans(用这个不用s), curr_size  
  用数组nums保存接收的数字
```
#include <iostream>
using namespace std;
typedef long long ll;
ll t,k,nums[1007], last_ans, curr_size;
```
  写出$a(n)$:
```
ll a(ll n)
{
    ll sum = 0;
    sum += n;
    while (n>0)
    {
        sum += n % 10;//加上n的个位
        n /= 10;//删掉n的个位，n的十位成为新的个位
    }
    return sum;
}
```
初始化方法，读入数据
```
void init()
{
    cin >> t >> k >> last_ans;
    t = t / 100 * 100;//把总秒数转化成100的整数，如1926会转成1900
}
```
main()：先初始化，然后遍历，接下来要分两种情况接收数字：已接收的数字数目小于k，以及已接收的数字个数大于等于k 两种情况
```
int main()
{
    init();
    for (ll i = 1; i <= t; i++)//遍历
    {
        if (curr_size < k)
        {
            用第一种方法接收数字，记作receive1()
        }
        else
        {
            用第二种方法接收数字, 记作receive2()
        }
        if ((i % 100) == 0)
        {
            打印一次答案
        }
    }
    printf("\n");
}
```
下面来写这两种不同的接收方法，首先是已接收的数字数目小于k，在这个接收过程中，一个小顶堆会被构建出来
```
void receive1(ll x)//这个插入方法让最大的值移动到nums[1]
{
    curr_size++;
    ll tmp = curr_size;
    while (tmp != 1 and nums[tmp >> 1]>x)若接收数字比其父节点小
    {
        nums[tmp]=nums[tmp>>1];则当前节点保存父节点的值
        tmp >>= 1;//tmp=tmp>>1的简写，此处为指针移动至其父节点
    }
    nums[tmp]=x;//循环结束后,指针所在的点存入x
}//该方法结束后，curr_size==k且不再改变，答案会一直保存在nums[1]
```
然后是已接收的数字个数大于等于k的情况下接收数字的方法
```
void receive2(ll x)//在nums[1]到nums[k]之间找一个位置插进去
{
    ll v = 1, u = 2;
    if (x <= nums[1])return;//若x小于等于nums[1],就直接结束方法，不会被插入小顶堆里
    while (u <= curr_size)否则就遍历2到curr_size
    {
        if (u < curr_size and nums[u + 1] < nums[u])u++;//u指向左右节点中较小的那个
        if (x < nums[u])//若此时x<nums[u],说明x的值介于nums[v]和nums[u]之间
        {
            break；//那么就跳出循环，让nums[v]=x,即较小的那个值用x换掉
        }
        nums[v] = nums[u];
        v = u;
        u = v*2;//u跳到v的左子节点
    }
    nums[v] = x;
}
```
**完整代码**
```
#include <iostream>
using namespace std;
typedef long long ll;
ll t,k,nums[1007], last_ans, curr_size;
ll a(ll n)
{
    ll sum = 0;
    sum += n;
    while (n>0)
    {
        sum += n % 10;//加上n的个位
        n /= 10;//删掉n的个位，n的十位成为新的个位
    }
    return sum;
}
void init()
{
    cin >> t >> k >> last_ans;
    t = t / 100 * 100;//把总秒数转化成100的整数，如1926会转成1900
}
void receive1(ll x)//这个插入方法让最大的值移动到nums[1]
{
    curr_size++;
    ll tmp = curr_size;
    while (tmp != 1 and nums[tmp >> 1]>x)
    {
        swap(nums[tmp],nums[tmp>>1]);//若新输入的数字比它父节点的数小则交换
        tmp >>= 1;//tmp=tmp>>1的简写
    }
}//该方法结束后，curr_size==k且不再改变，答案会一直保存在nums[1]
void receive2(ll x)//在nums[1]到nums[k]之间找一个位置插进去
{
    ll v = 1, u = 2;
    if (x <= nums[1])return;//若x小于等于nums[1],就直接结束方法，不会被插入小顶堆里
    while (u <= curr_size)否则就遍历2到curr_size
    {
        if (u < curr_size and nums[u + 1] < nums[u])u++;//u指向左右节点中较小的那个
        if (x < nums[u])//若此时x<nums[u],说明x的值介于nums[v]和nums[u]之间
        {
            break；//那么就跳出循环，让nums[v]=x,即较小的那个值用x换掉
        }
        nums[v] = nums[u];
        v = u;
        u = v*2;//u跳到v的左子节点
    }
    nums[v] = x;
}
int main()
{
    init();
    for (ll i = 1; i <= t; i++)
    {
        if (curr_size < k)
        {
            receive1(a(i + last_ans));
        }
        else
        {
            receive2(a(i + last_ans));
        }
        if ((i % 100) == 0)
        {
            last_ans = nums[1];
            printf("%lld ", last_ans);
        }
    }
    printf("\n");
}
```
