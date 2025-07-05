# 时间函数

***\*SQL49 统计各岗位员工平均工作时长\****

### 做什么  （使用timestampdiff函数求两个时间之差）

问题：请统计该公司各岗位员工平均工作时长？要求输出：员工岗位类别、平均工作时长（以小时为单位输出并保留三位小数），按照平均工作时长降序排序。

***\**\*注：\*\**\***如员工未打卡该字段数据会存储***\**\*为NUL\*\**\***L，那么***\**\*不计入在内\*\**\***。

示例数据结果如下：

| post      | work_hours |
| --------- | ---------- |
| Engineer  | 9.639      |
| Financial | 8.992      |
| Director  | 8.875      |

解释：Engineer类岗位有4、5、6共计3名员工，工作时长分别为：9.500、9.167、10.250，则平均工作时长为 (9.500+9.167+10.250)/3=9.639小时。

其他结果同理.....



### 怎么做

- TIMESTAMPDIFF(单位, 开始时间, 结束时间)
- round保留三位小数
- order by xxx desc 降序
- 去除null值



```
select 
st.post,
--求时间差并作格式处理
round(
    sum(
        timestampdiff(minute,at.first_clockin,at.last_clockin)/60
    )
    --相除求平均值
    /  count(st.staff_id),3) work_hours
    
from staff_tb st
--多表连接
join
    attendent_tb at
on st.staff_id = at.staff_id
--空值处理
where at.first_clockin is not null and at.last_clockin is not null
--分组处理
group by st.post
--排序处理
order by work_hours desc
```