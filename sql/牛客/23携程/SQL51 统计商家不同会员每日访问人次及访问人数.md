## ***\**\*SQL51 统计商家不同会员每日访问人次及访问人数\*\**\***

### 做什么 连接两表进行查询即可

根据用户访问数据统计出不同会员等级访问人次及访问人数总和。

要求输出：会员等级、访问人次、访问人数

注：按照访问人次降序排序

示例数据结果如下：

| vip      | visit_nums | visit_users |
| -------- | ---------- | ----------- |
| 银卡会员 | 6          | 2           |
| 金卡会员 | 3          | 1           |
| 钻石会员 | 2          | 1           |
| 普通会员 | 1          | 1           |

结果解释：

钻石会员有user_id为12的用户，该用户分别在9月1日、9月2日访问，故访问人次为2、访问人数为1；

其他结果同理。



### 怎么做

- join on user_id
- 对vip字段进行group by 
- 对两个字段进行计数  但注意用户需要去重count(visit_nums)    count(distinct visit_users)



### 代码展示

```
select
--查询字段
ut.vip,
count(vt.user_id) visit_nums,
count(distinct ut.user_id) visit_users

from visit_tb vt
--两表连接
join uservip_tb ut
on vt.user_id = ut.user_id
--分组
group by ut.vip
--排序
order by visit_nums desc
```