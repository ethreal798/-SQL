## ***\*SQL59 统计各个部门平均薪资\****

### 做什么

问题：请统计各个部门平均实发薪资？

注：***\**\*实发薪资=标准薪资-扣除薪资\*\**\***，统计平均薪资要求剔除薪资小于4000和大于30000的员工

要求输出：部门，平均实发薪资（保留3位小数）按照平均实发薪资降序排序

示例数据结果如下：

| department | avg_salary |
| ---------- | ---------- |
| dep2       | 9300.000   |
| dep1       | 8350.000   |

解释：部门dep2共有员工3、5、7

实发薪资分别为9000-1800=7200、13500-2100=11400、50000-5000=45000>30000(剔除)

故结果为(7200+11400)/2=9300.000;

其他结果同理。



- 求平均实发薪资  ***\**\*实发薪资=标准薪资-扣除薪资 还要注意剔除薪资小于4000和大于30000的员工\*\**\***
- 这个平均实发薪资要保留三位小数
- 平均实发薪资要降序排序
- 是各部位的平均实发薪资



### 怎么做

- 我这边使用子查询进行剔除符号条件的员工
- 使用round()保留三位小数
- order by 字段 desc
- group by department



### 代码展示

```
select
    s1.department,
    round(sum(s2.salary)/count(s2.staff_id),3)avg_salary

from 
    staff_tb s1
join
    (
        select
            staff_id,
            normal_salary - dock_salary as salary
        from salary_tb 
        where normal_salary - dock_salary >= 4000 and normal_salary - dock_salary <= 30000
    )s2
on s1.staff_id = s2.staff_id
group by s1.department
order by avg_salary desc
```