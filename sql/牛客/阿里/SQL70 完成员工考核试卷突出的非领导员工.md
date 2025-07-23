## ***\*SQL70 完成员工考核试卷突出的非领导员工\****

### 做什么

请你找到作答每类试卷的突出非领导员工，并输出他们的员工ID，员工等级和突出试卷类别并按照员工ID升序排序，若某员工两类试卷都突出，则按照试卷ID升序排序。

由示例数据结果输出如下：

![img](http://www.kdocs.cn/api/v3/office/copy/MUQ0bkNla1dZTk5YT3BUcGx0NHNhMmRRQmJIcjFTQU14Z1l1TFpWOVlVWUhZTzdhRXBPYVpQOC9hdGQvSkx5WG1ZNlhUVlN3NHFqVUVDRldLcHBsUG10cm1rWmlBYXhXN1U5RnVYK3pkRWVteEZlYUljKzlYZk9hNzRDVjlGUG5FQkN1U3BJTGp6Zm9jZzlXdjdyNEdwZHVJeEJQN2hROG9WSUp4NmlHTzBMU1o3VmZidTJNNGkyNHlJZ3FTNmZubGFGcHRvRFByWU8zU3Bqa0R1UWJRVjVWOVFqbzlLRDlNRVN1SHhhTkdiSWphT0tBaUJjK29YMEhnUjF6MkVlODRiTzNsYkJyMUMwPQ==/attach/object/GIY4C6RBABQFM?)

解释：

9001试卷平均得分为78.667分，得分高于78.667且符合员工等级小于7的员工有1005、1007

9001试卷平均作答时间为27.3分钟，作答时间小于27.3分钟且符合员工等级小于7的员工有1006、1007

因此9001号卷突出员工为1007

9002试卷平均得分为75.833分，得分高于75.833且符合员工等级小于7的员工有1005、1006

9002试卷平均作答时间为34.2分钟，作答时间小于34.2分钟且符合员工等级小于7的员工有1006、1007

因此9002号卷突出员工为1006

注意：***\*作答用时少于同类试卷平均值且个人分数大于同类试卷总体平均值的员工记为该类型的突出员工。\****



***\*提取题干\****

1. 突出员工：作答用时少于同类试卷平均值且个人分数大于同类试卷总体平均值的员工
2. 作答每类试卷的突出非领导员工
3. emp_id 员工id  emp_level 员工等级 exam_tag突出试卷类别
4. 并按照员工ID升序排序，若某员工两类试卷都突出，则按照试卷ID升序排序。



### 怎么做

本题涉及三表，多条件。我们需要一个一个条件进行拆解

- 对于突出员工这个条件    我们先去exam_record 试卷作答记录表 进行分组聚合  得到同类试卷的平均作答时间和平均分  可以使用with创建临时表  也可以使用一个子查询
- 我们使用timestampdiff函数去处理时间
- 创建这个作为我们后续判断是否为突出员工的标准

```sql
select
    exam_id,
    avg(timestampdiff(MINUTE, start_time, submit_time)) avg_time,
    avg(score) score_avg
from
    exam_record
group by
    exam_id
```

- 接着我们再对试卷作答记录表进行筛选 使用上面创建的标准（表）筛选出满足突出员工条件的员工id
- 使用where对每行进行筛选

```sql
select
    t1.emp_id,
from
    #试卷作答记录表  筛选出符合条件的emp_id
    exam_record t1
    # 进行一个子查询 对试卷作答记录表进行对exam_id分组  求对应试卷的平均分 平均作答时间
    join (
        select
            exam_id,
            avg(timestampdiff(MINUTE, start_time, submit_time)) avg_time,
            avg(score) score_avg
        from
            exam_record
        group by
            exam_id
    ) t2 on t1.exam_id = t2.exam_id
where
    # 突出员工的条件
    timestampdiff(MINUTE, t1.start_time, t1.submit_time) < avg_time
    and t1.score > score_avg
```

- 对于剩下的字段我们只需要继续join 即可

```sql
    # 关联 考核试卷信息表 拿到tag字段
    join examination_info t3 on t3.exam_id = t1.exam_id
    
    # 关联员工信息表 筛选不是领导的员工id
    join emp_info t4 on t4.emp_id = t1.emp_id

# 再完善where语句中对非领导员工的筛选
where
    # 突出员工的条件
    timestampdiff(MINUTE, t1.start_time, t1.submit_time) < avg_time
    and t1.score > score_avg and t4.emp_level < 7
```

- 排序处理

```sql
order by t1.emp_id,t1.exam_id
```

### 代码展示

```sql
select
    t1.emp_id,
    t4.emp_level,
    t3.tag exam_tag
from
    #试卷作答记录表  筛选出符合条件的emp_id
    exam_record t1

    # 进行一个子查询 对试卷作答记录表进行对exam_id分组  求对应试卷的平均分 平均作答时间
    join (
        select
            exam_id,
            avg(timestampdiff(MINUTE, start_time, submit_time)) avg_time,
            avg(score) score_avg
        from
            exam_record
        group by
            exam_id
    ) t2 on t1.exam_id = t2.exam_id
    
    # 关联 考核试卷信息表 拿到tag字段
    join examination_info t3 on t3.exam_id = t1.exam_id
    
    # 关联员工信息表 筛选不是领导的员工id
    join emp_info t4 on t4.emp_id = t1.emp_id

where
    # 突出员工的条件
    timestampdiff(MINUTE, t1.start_time, t1.submit_time) < avg_time
    and t1.score > score_avg and t4.emp_level < 7

order by t1.emp_id,t1.exam_id
```