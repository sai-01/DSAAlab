```
#include <iostream>
#include <algorithm>//包含sort
typedef long long ll;
using namespace std;
const int maxn = 100007;
ll T,n,curr_num,index, max_time;//当前已购入的球员数；案例数；总球员数；index;总power；所有球员中卖得最久的那个的时间
struct player{ll pow_val, sell_time;};//定义运动员：有power值和出售时间两个属性
struct player club_players[maxn], players[maxn];//已经买入的球员；所有球员
void init();
void compete(ll p);
void buy(player new_player);
bool cmp(player a, player b) { return a.sell_time > b.sell_time; }
ll max_pow_val();
ll get_sum();
int main()
{
    cin >> T;
    while (T--)
    {
        init();//初始化
        sort(players + 1, players + n + 1, cmp);//按球员的出售时间期限对所有球员排序,时间长的排前面
        printf("%lld\n", get_sum());
    }
}
void init() //初始化并输入数据
{
    cin >> n;
    curr_num = 0;//当前已买入的球员数量设为0
    max_time = 0;//当前时刻设为0
    club_players[0].pow_val = 1e15 + 7;//第一个位置不用，放个很大的数
    for (int i = 1; i <= n; i++)cin >> players[i].pow_val;//读入power值
    for (int i = 1; i <= n; i++)
    {
        cin >> players[i].sell_time;//读入出售期限
        max_time = max(max_time, players[i].sell_time);//把这里面最大的时间提取出来
    }
}
ll get_sum()
{
    ll sum_power=0;
    index = 1;
    for (ll i = max_time; i >= 1; i--)//遍历最大的天数到第一天
    {
        while (index <= n and players[index].sell_time == i)buy(players[index++]);//符合条件的就买入
        sum_power += max_pow_val();
    }
    return sum_power;
}
void compete(ll p)
{
    ll cur_index = p;
    if (club_players[p].pow_val < club_players[p << 1].pow_val and (p << 1) <= curr_num)cur_index = p << 1;//若当前节点的power比父节点的power大则p移动到父节点
    if (club_players[cur_index].pow_val < club_players[(p << 1) + 1].pow_val and (p << 1) <= curr_num - 1)cur_index = (p << 1) + 1;
    if (p != cur_index)//若p发生了移动
    {
        swap(club_players[p], club_players[cur_index]);//交换位置
        compete(cur_index);//继续用同样的方法检查
    }
}
void buy(player new_player)//买入球员
{
    club_players[++curr_num] = new_player;//把球员加到club里
    ll p = curr_num;
    while (p >= 1 and club_players[p >> 1].pow_val < club_players[p].pow_val)//调整新来的球员在club中的位置
    {
        swap(club_players[p], club_players[p >> 1]);
        p = p >> 1;
    }
}
ll max_pow_val()//返回在某天可以购买且power最大的球员的power值，
{
    player tmp;//创建一个临时球员
    tmp.pow_val = 0;
    if (curr_num>0)
    {
        tmp = club_players[1];
        swap(club_players[1], club_players[curr_num]);//把1号位和当前位交换
        curr_num--;//不要了
        compete(1);
    }
    return tmp.pow_val;
}
```