## ***\*SQL56 统计用户获得积分\****

### 做什么

该商城这几日推出新的推广活动，用户单次访问时长满10分钟则获得1积分，请查询这几日访问的用户可以获得多少积分？

要求输出：user_id,积分

注：输出结果按照积分降序排序

示例数据结果如下：

| user_id | point |
| ------- | ----- |
| 11      | 11    |
| 12      | 11    |
| 10      | 8     |
| 13      | 7     |
| 14      | 4     |

结果解释：

user_id为11的用户分别在0901 08:30、0902 10:30、0902 12:00、0903 08:30访问，

访问时长分别为40、35、2、40分钟，故获得积分为4+3+0+4=11积分；

其他结果同理



- 根据结果解释我们可以知道首先求出访问时长接着进行除10，抹去余数，进行求和
- 降序排序point 字段 即可



### 怎么做

- 求分钟  我们可以使用timestampdiff（minute,time1,time2）函数
- 抹去余数  我们可以使用floor()函数  求和sum()函数
- 排序就order by point desc 即可



### 代码展示

```
select 
    user_id,    
    sum(floor(timestampdiff(Minute,visit_time,leave_time)/10)) point
from 
    visit_tb
group by 
    user_id
order by 
    point desc
```