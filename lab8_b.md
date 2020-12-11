```
#include <iostream>
using namespace std;
const int maxn = 1000007;
struct student{int juan_val, student_id;};//学生，包含内卷值和学号两个属性
struct student students[maxn];//存放学生的数组
int  cur_size, check_id, T,n;//当前已参与内卷的人数（也是垫底的同学的排名）；要被查排名的学生的id；样例数；学生总数
void start_juan();//大家一起开始卷
void join_juan(student freshman);//让新来的学生加入内卷
void check_rank(int check_id);//查看输入id对应学生的排名(level,index)
int main()
{
    cin>>T;
    while (T--)
    {
        cin >> n;
        start_juan();//开卷有益
        cin >> check_id;
        check_rank(check_id);//查看排名
    }
}
void start_juan()
{
    student freshman;//新建学生
    cur_size = 0;//初始化当前内卷人数
    students[0].juan_val = 1000000001;//用一个卷值很大的东西占位（位置0不放学生
    for (int i = 1; i <= n; i++)//读数据
    {
        cin >> freshman.juan_val;//读入卷值
        freshman.student_id = i;//分配id
        join_juan(freshman);//加入卷
    } 
}
void join_juan(student freshman)
{
    students[++cur_size] = freshman;//当前内卷人数+1，用这个位置保存新来卷的学生
    int tmp_rank = cur_size;//临时排名
    while (tmp_rank >= 1 and students[tmp_rank >> 1].juan_val < students[tmp_rank].juan_val)//当该学生的卷值大于排在他上面的学生的卷值...
    {//"x>>1"意思是把x先变成二进制数，然后删除末尾，再转回十进制，如7>>1, 7转为111，删末尾变成11，再转就变成3了，6>>1的结果也是3，所以3是6和7的父节点
        swap(students[tmp_rank], students[tmp_rank >> 1]);//交换位置
        tmp_rank = tmp_rank >> 1;//该学生向上流动
    }
}
void check_rank(int check_id) 
{
    int ranking, juan_level, juan_index;
    for (int i = 1; i <= cur_size; i++)//遍历学生数组，在里面找一个id
    {
        if (students[i].student_id == check_id)//找到后保存他在学生数组中的位置
        {
            ranking = i;
            break;
        }
    }
    juan_level = log2(ranking)+1;//计算level
    juan_index = ranking - (1 << (juan_level-1))+1;//"1<<x"意思是在1的右边补x个0，再转成1十进制
    printf("%d %d\n", juan_level, juan_index);//打印结果
}
```