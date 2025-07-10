## ***\*SQL54 统计用户从访问到下单的转化率\****

### 做什么

请统计该商城每天用户从访问到下订单的转化率。

要求输出：日期，转化率（该日下订单人数/访问人数，以百分数形式输出并四舍五入保留1位小数）

注：输出结果按照日期升序排序；

示例数据结果如下：

| date       | cr    |
| ---------- | ----- |
| 2022-09-01 | 75.0% |
| 2022-09-02 | 50.0% |
| 2022-09-03 | 66.7% |

结果解释：

以2022-09-01为例，该日共计有user_id为10、11、12、13共计4名用户访问商城，

其中11、12、13共计3名用户下了订单，故转化率为3/4=75.0%；

其他结果同理。

- 求日期
- 求该日下订单人数   求该日访问人数
- 推9月2 日     下单 11，12号  访问11 12 13 14   ->推得50%

### 怎么做 

对于这种订单和访问记录 我们可以使用子查询进行处理

- 对order表进行子查询得到下单人数
- 对visit表进行查询得到访问人数
- 加百分号这个操作 是有套路 记住即可
- date_format函数进行时间处理

### 代码展示

```
select
u.ordertime date,
concat( round(u.order_user_num / v.visit_user_num*100,1),'%') cr   

from(
    select 
        count(distinct user_id) order_user_num, 
        date_format(order_time,'%Y-%m-%d') ordertime
    from 
        order_tb
    group by 
        ordertime
)u 
join (
    select 
        count(distinct user_id) visit_user_num, 
        date_format(visit_time,'%Y-%m-%d') visittime
    from 
        visit_tb
    group by 
        visittime
)v on u.ordertime = v.visittime
```
