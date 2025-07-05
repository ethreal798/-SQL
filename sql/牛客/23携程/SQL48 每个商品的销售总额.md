# 窗口函数

## SQL48 每个商品的销售总额 （为什么使用窗口函数）

### 做什么 （连接两表根据名称分类 聚合得到销售总量，使用窗口函数进行类别排名）

使用上述表格，编写一个SQL查询，返回每个商品的销售总量，先按照商品类别升序排序，再按销售总量降序排列，同时包括商品名称和销售总量。此外，还需要在结果中包含每个商品在其所属类别内的排名，排名相同的商品可以按照 product_id 升序排序。

示例输出如下：

| product_name | total_sales | category_rank |
| ------------ | ----------- | ------------- |
| Product A    | 12          | 1             |
| Product B    | 7           | 2             |
| Product D    | 22          | 1             |
| Product C    | 14          | 2             |
| Product E    | 24          | 1             |

### 怎么做

- join on p.id = o.id
- group by p.name ,category，product_id  根据商品名称分类以及商品类别分类还有id 因为only_full_group_by 模式 
- sum(quantity)  
- 窗口函数 使用row_number() 对category分区  order by sum(xx) ,id

### 代码展示

```
select 
    p.name as product_name,
    sum(o.quantity) as total_sales,
    row_number() over(partition by p.category order by sum(o.quantity) desc,p.product_id) as category_rank
from orders o
join products p
    on o.product_id = p.product_id
group by p.name,p.category,p.product_id
order by p.category

-- 始终在 GROUP BY 中包含所有在 SELECT 和 ORDER BY 中使用的非聚合列
```