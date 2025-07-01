## SQL44查询连续入住多晚的客户信息？

### 做什么

问题：请查询该酒店从6月12日开始连续入住多晚的客户信息？

要求输出：客户id、房间号、房间类型、连续入住天数（按照连续入住天数的升序排序，再按照房间号的升序排序，再按照客户id的降序排序）

示例数据结果如下：

| user_id | room_id | room_type  | days |
| ------- | ------- | ---------- | ---- |
| 203     | 1003    | 商务单人房 | 2    |
| 205     | 1007    | 商务标准房 | 3    |
| 208     | 1009    | 商务标准房 | 4    |

解释：以客户203为例，在2022-06-12入住酒店，在2022-06-14退房，

连续在12日晚、13日晚入住在该酒店，故结果如上；

其他结果同理。



### 怎么做

在2022-06-12入住酒店，在2022-06-14退房 -> 两天 我们只需要对时间进行相减

- 使用datediff（time1，time2）> 1  



入住天数的升序排序，再按照房间号的升序排序，再按照客户id的降序排序）

- order by  days , room_id , user_id desc



两表关联

- join on   ......



### 代码展示

```
select 

ct.user_id,
gt.room_id,
gt.room_type,
datediff(ct.checkout_time,checkin_time) days

from guestroom_tb gt
join
    checkin_tb ct
on gt.room_id = ct.room_id
where datediff(ct.checkout_time,checkin_time)>1
order by days,gt.room_id,ct.user_id desc
```