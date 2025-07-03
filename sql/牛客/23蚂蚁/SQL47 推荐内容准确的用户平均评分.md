# 子查询

## SQL47 推荐内容准确的用户平均评分

### 做什么   

问题：请统计推荐内容准确的用户平均评分？(结果保留3位小数)

注：（1）准确的定义：推荐的内容标签与用户喜好标签一致；如推荐多次给同一用户，有一次及以上准确就归为准确。

示例数据结果如下：

| avg_score |
| --------- |
| 84.500    |

解释：一共推荐8条内容，其中推荐给101、103、105、106四位用户的内容准确,

四位用户的评分分别是88、78、90、82，故平均评分=（88+78+90+82）/4=84.500

(2)如果同一用户推荐同一个内容标签的话，计算的时候只算一次



结论：在两个表中找出满足准确这个定义的数据   

1. 推荐的内容标签与用户喜好标签一致    
2. 如推荐多次给同一用户，有一次及以上准确就归为准确。
3. 如果同一用户推荐同一个内容标签的话，计算的时候只算一次
4. 保留三位小数

### 怎么做

- 两个表经典套路，join on 两表对应字段
- 推荐多次给同一用户，一次准确就准确，也是对应着去重
- 本题中注意如果同一用户推荐多个同样内容标签，只计算一次，这指的是要进行去重
- 于是我们可以使用子查询  先查出已经去重好的数据，在进行求得分均值

### 代码展示

思考一下如果不去重的数据是什么样的

```
select
    round(sum(uut.score) / count(uut.user_id),3) avg_score
from (
    select 
        distinct ut.user_id , ut.score
        from
            user_action_tb ut 
        join recommend_tb rt 
        on ut.user_id= rt.rec_user and rt.rec_info_l = ut.hobby_l
) uut

#也可以这样做 手动去重
select
round(sum(distinct ut.score) / count(distinct ut.user_id),3) avg_score
from recommend_tb rt 
join 
    user_action_tb ut
    on rt.rec_user = ut.user_id
where
    ut.hobby_l = rt.rec_info_l
```