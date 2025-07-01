## SQL43 获取指定客户每月的消费额

### 做什么

现需要查询 Tom 这个客户在 2023 年每月的消费金额（按月份正序显示），示例如下：

| time    | total  |
| ------- | ------ |
| 2023-02 | 50.5   |
| 2023-07 | 1200.0 |
| 2023-09 | 130.6  |

### 怎么做

- tom这个用户  可以使用where进行筛选
- 2023年  使用year(time)==2023 进行筛选
- 每月  使用group by 对2023的每月进行分组  注：分组字段要与select字段的对应
- 月份正序显示  order by即可
- join on关联两表
- 消费金额 sum()求解即可 注意题目的type字段
- 题目给的时间精确到了秒 我们如何截取到月 使用date_format(time,'%Y-%m') 

### 代码展示

```
select 

date_format(t.t_time,'%Y-%m') time,
sum(t.t_amount)total

from trade t 
join customer c on t.t_cus = c.c_id
where c_name = 'Tom' and year(t.t_time) = 2023 and t.t_type = 1
group by time
order by time
```