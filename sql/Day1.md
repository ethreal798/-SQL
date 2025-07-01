# 多表查询

## SQL42 分析课后逾期情况 Join on

###    做什么

请根据以上数据分析各还款能力级别的客户逾期情况，按照还款能力级别统计有逾期行为客户占比。要求输出还款能力级别、逾期客户占比。注：逾期客户占比要求按照百分数形式输出并四舍五入保留 1 位小数，最终结果按照占比降序排序。

| pay_ability | overdue_ratio |
| ----------- | ------------- |
| C           | 66.7%         |
| B           | 25.0%         |
| A           | 0.0%          |

###        怎么做

多表查询先关联表一般都是xx.id==xx.id

- 按照百分数形式输出 使用concat函数拼接
- 四舍五入使用round
- 降序排序order by
- 统计用户个数count

###        代码展示

```
select

ct.pay_ability , 
concat(round(count(lt.overdue_days) / count(ct.customer_id)*100,1),'%') 
overdue_ratio

from loan_tb lt 
    join customer_tb ct 
    on lt.customer_id = ct.customer_id
group by ct.pay_ability
order by overdue_ratio desc
```



