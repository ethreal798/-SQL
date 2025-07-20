## SQL64 商品交易(网易校招笔试真题)

### 做什么

查找购买个数超过20,质量小于50的商品，按照商品id升序排序,如:

| ***\**\*id\*\**\*** | ***\**\*name\*\**\*** | ***\**\*weight\*\**\*** | ***\**\*total\*\**\*** |
| ------------------- | --------------------- | ----------------------- | ---------------------- |
| 2                   | A2                    | 20                      | 81                     |
| 3                   | B3                    | 29                      | 30                     |
| 5                   | G2                    | 33                      | 23                     |

- 再两个表中根据商品id查询 id name weight   total （count的总和）
- 条件total>20 并且 weight < 50 
- 商品id升序排序



### 怎么做

- 两表就按照id关联即可
- 因为在trans(交易表)中我们需要将count去进行累加 才方便求total  所以我想到写一个子查询去对商品id分组 进而求count的总和
- order by 商品_id即可

### 代码展示

```
select
    g.id,
    g.name,
    g.weight,
    t.count total
from 
    goods g
join 
    (
        select
            goods_id,
            sum(count) count
        from trans
        group by goods_id
    ) t
on g.id = t.goods_id
where g.weight <50 and t.count >= 20
order by g.id
```