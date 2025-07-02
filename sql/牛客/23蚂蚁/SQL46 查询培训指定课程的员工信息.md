## ***\*SQL46 查询培训指定课程的员工信息\****

### 做什么   （在ct这张表对字段course进行筛选得到有course的行）

员工培训信息表***\**\*cultivate_tb\*\**\***(info_id-信息id，staff_id-员工id，course-培训课程)，如下所示：

注：该公司共开设了三门课程，员工可自愿原则性培训0-3项；

| info_id | staff_id | course                    |
| ------- | -------- | ------------------------- |
| 101     | 1        | course1, course2          |
| 102     | 2        | course2                   |
| 103     | 3        | course1, course3          |
| 104     | 4        | course1, course2, course3 |
| 105     | 5        | course3                   |
| 106     | 6        | NULL                      |
| 107     | 7        | course1, course2          |

问题：请查询培训课程course3的员工信息？

注：只要培训的课程中包含course3课程就计入结果

要求输出：员工id、姓名，按照员工id升序排序；

示例数据结果如下：

| staff_id | staff_name |
| -------- | ---------- |
| 3        | Aldis      |
| 4        | Lawson     |
| 5        | Carl       |

解释：有员工3、4、5培训了course3课程，故结果如上

### 怎么做

- 关联表从而匹配姓名 工号
- 对于如何找course3  我们可以使用like 进行模糊匹配    course like '%course3%'
- order by 工号

### 代码展示

```
select 
st.staff_id,st.staff_name
from staff_tb st 
join
    cultivate_tb ct 
on st.staff_id = ct.staff_id
where ct.course like '%course3%'
order by st.staff_id
```