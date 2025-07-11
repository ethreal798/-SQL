## ***\*SQL55 统计员工薪资扣除比例\****

### 做什么

问题：请统计部门 dep1 扣除薪资比例。

要求输出：员工id、姓名、扣除薪资占比（保留1位小数以百分数形式输出）按照扣除薪资占比降序排序

知识点：关联查询、子查询、聚合函数、字符串函数

示例数据结果如下：

| staff_id | staff_name | dock_ratio |
| -------- | ---------- | ---------- |
| 1        | Angus      | 20.8%      |
| 2        | Cathy      | 20.0%      |
| 4        | Lawson     | 18.1%      |
| 6        | Ben        | 13.3%      |



解释：部门dep1共有员工1、2、4、6

其中员工1标准薪资为12000，扣除薪资为2500，故扣除薪资比例为2500/12000=20.8%

其他结果同理。

- 部门指定dep1
- 输出staff_id staff_name dock_ratio  其中dock_ratio 保留1位小数 以百分数形式输出
- dock_ratio 降序排序



### 怎么做

- 两表查询 join on staff_id 即可 加上筛选条件department= dep1
- 加百分号操作  concat(num,'%')   经常遇见纯套路
- 保留一位小数 round(num,1)
- 降序排序直接 order by 



### 代码展示

```
select
    st.staff_id,
    st.staff_name,  
    concat(
        round(sa.dock_salary / sa.normal_salary *100,1)
        ,'%')dock_ratio

from 
    staff_tb st 
join 
    salary_tb sa
on
    st.staff_id = sa.staff_id and st.department = 'dep1' 
order by 
    dock_ratio desc
```