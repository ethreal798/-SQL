## SQL99 分析每个商品在不同时间段的销售情况

### 做什么

【要求】：根据上面这三个表格，查询每个商品的销售情况，包含的字段：商品 ID、商品名称、该商品在 2024 年第二季度的销售总额，该商品在所属类别的销售排名，所属供应商。查询出来的数据按照商品 ID 升序排列。要求查询出来的表格的字段如下：

- product_id: 商品的唯一标识符。
- product_name: 商品的名称。
- q2_2024_sales_total: 2024 年第二季度的销售总额。4、5、6月份属于第二季度
- category_rank: 在所属类别的销售排名。
- supplier_name: 所属供应商。



即：

1. 筛选第二季度的(order_info)
2. 求销售总额     (order_info)  
3. 求销售排名   （product_info + order_info）
4. 所属供应商 （supplier_info表）

注意样例输出productE 他的销售额是0 



### 怎么做

- 我们对order_info表进行一个子查询 筛选出4，5，6月下单的 同时对销售额进行求和 （这里可能会隐性把产品不在第二季度下单过滤掉）但注意题目即使销售额为0也要输出

```sql
    select
        product_id,if(sum(total_amount)>0,sum(total_amount),0)q2_2024_sales_total
    from order_info
    where month(order_date) in (4,5,6)
    group by product_id
```

- 针对上面 我们只需要进行left join 商品信息表（product_info）这样我们就可以得到商品的具体销售额了对于 题目样例我们还需要对销售额进行一个处理  将NULL -> 0 
- 品类内排名 涉及排名我们使用窗口函数

```sql
with t as(
select 
    p.product_id,
    p.product_name,
    --处理NULL值
    if(o.q2_2024_sales_total>0,o.q2_2024_sales_total,0)q2_2024_sales_total,
    row_number() over(partition by category order by o.q2_2024_sales_total desc) category_rank
from product_info p 
--左连接
left join (  
    select
        product_id,if(sum(total_amount)>0,sum(total_amount),0)q2_2024_sales_total
    from order_info
    where month(order_date) in (4,5,6)
    group by product_id
)o 
on p.product_id = o.product_id
)
```

- 最后关联供应商信息表即可

```sql
select
    t.product_id,
    t.product_name,
    t.q2_2024_sales_total,
    t.category_rank,
    s.supplier_name
from t 
join supplier_info s
on t.product_id = s.product_id
```

### 代码展示

```sql
with t as(
select 
    p.product_id,
    p.product_name,
    if(o.q2_2024_sales_total>0,o.q2_2024_sales_total,0)q2_2024_sales_total,
    row_number() over(partition by category order by o.q2_2024_sales_total desc) category_rank
from product_info p 
left join (
    select
        product_id,if(sum(total_amount)>0,sum(total_amount),0)q2_2024_sales_total
    from order_info
    where month(order_date) in (4,5,6)
    group by product_id
)o 
on p.product_id = o.product_id
)

select
    t.product_id,
    t.product_name,
    t.q2_2024_sales_total,
    t.category_rank,
    s.supplier_name
from t 
join supplier_info s
on t.product_id = s.product_id
```