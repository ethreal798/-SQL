## ***\*SQL58 查询单日多次下订单的用户信息\****

### 做什么

请查询单日下单多次的用户信息？

要求输出：订单日期，user_id，下单次数，会员等级

注：单日多次下订单指该日同一用户下单次数大于1次，结果按照下单次数降序排序

示例数据结果如下：

| order_date | user_id | order_nums | vip      |
| ---------- | ------- | ---------- | -------- |
| 2022-09-02 | 12      | 2          | 钻石会员 |

结果解释：

user_id为12的用户在9月2日分别下了order_id为105、106的订单，故结果如上。

- 单日下单多次   注意同一用户同一天
- 订单日期 是以年月日格式
- order_nums是下单次数 自拟        降序排序
- vip表的对应vip字段

### 怎么做

- 日期格式 我们使用date_format(date,'%Y-%m-%d')
- 我们使用子查询先查询order表中各用户下单了几次
- 在关联vip表时进行条件筛选
- order by  下单次数 降序

### 代码展示

```
select 
    o.order_date,
    o.user_id,
    o.order_nums,
    v.vip
from(
    select
        date_format(order_time,'%Y-%m-%d') order_date,
        user_id,
        count(order_id) order_nums
    from 
        order_tb
    group by user_id,order_date    
)o
join 
    uservip_tb v
on o.user_id = v.user_id and o.order_nums>1
order by o.order_nums desc
```