# 时间序列问题

## ***\*SQL50 查询连续登陆的用户\****

### 做什么 

***\**\*问题\*\**\***：请查询***\**\*连续登陆不少于\*\**\***3天的新注册用户？要求：输出user_id并***\**\*升序排序\*\**\***。

注：登录表为单日随机***\**\*一次登\*\**\***录数据，***\**\*该题忽略\*\**\***单日多次登录情况。



示例输出如下：

| user_id |
| ------- |
| 1102    |
| 1106    |

解释：1102在9日、10日、11日，登陆了系统满足查询条件；1106在10日、11日、12日登录了系统满足查询条件。



### 怎么做

- 看到连续登录问题想到可以使用窗口函数解决
- 使用子查询对时间表进行构造得到其当前时间 后一行时间  后二行时间
- 判断条件   3个时间相邻差值为1即可



```
select
r.user_id
from(
    select
        user_id,
        date(log_time)l0,  --当前行时间
        --下一行时间
        lead(log_time,1) over(partition by user_id order by log_time)l1,
        --下两行时间
        lead(log_time,2) over(partition by user_id order by log_time)l2
    from login_tb
)d
--连接两表筛掉部分user_id
join register_tb r
on d.user_id = r.user_id
--判断条件
where datediff(d.l2,d.l1)=1 and datediff(d.l1,d.l0)=1
order by r.user_id
```