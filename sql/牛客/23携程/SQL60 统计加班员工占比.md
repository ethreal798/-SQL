# 构造字段

## ***\*SQL60 统计加班员工占比\****

### 做什么

问题：请统计该公司各部门加班员工所占比例？

注：工作时长大于9.5小时定义为加班

要求输出：部门（department）、加班员工占比（ratio，以百分数形式输出并保留1位小数），查询结果按照加班员工占比降序排序；

示例数据结果如下：

| department | ratio |
| ---------- | ----- |
| dep1       | 25.0% |
| dep2       | 0.0%  |

解释：dep1部门共有4名员工，其中仅有员工6(Ben)在该日加班，所以结果为1/4=25.0%

其他结果同理.....

- 各部门   所以要进行分组
- 加班员工    这里就要进行一个区分
- 区分条件是  工作时长>9.5小时为加班
- ratio的结果是 百分数形式且保留一位小数
- 最后还要排序 desc



### 怎么做

- 分组使用group by 即可
- 加班员工 我的做法是在出勤信息表 attendent_tb 自拟一个字段overtime表示有没有加班
- 我使用if进行条件判断
- 工作时长题目给的是年月日时分秒  我们可以使用timestampdiff()函数 求分钟在进行➗60操作得到小时数
- 百分数形式 我们使用concat函数拼接一个‘%’就可以了  保留一位小数我们使用round函数
- 最后order by 即可



### 代码展示

```
select

    s.department,
    concat(round(sum(a.overtime) / count(s.staff_id)*100,1),'%') ratio

from(
    select 
        staff_id,
        if(timestampdiff(Minute,first_clockin,last_clockin) / 60 >9.5,1,0) overtime
    from 
        attendent_tb
)a 
join 
    staff_tb s 
on 
    a.staff_id = s.staff_id
group by 
    s.department
order by 
    ratio desc
```