# 三表查询

## ***\*SQL63 网易云音乐推荐(网易校招笔试真题)\****

### 做什么

请你编写一个SQL，查询向user_id = 1 的用户，推荐其关注的人喜欢的音乐。

不要推荐该用户已经喜欢的音乐，并且按music的id升序排列。你返回的结果中不应当包含重复项

上面的查询结果如下:

| music_name |
| ---------- |
| kong       |
| MOM        |

- 查询user_id =1 用户关注的人及其 关注的人喜欢的音乐
- 注意：不要推荐用户已经喜欢的音乐（即筛掉user_id=1喜欢的音乐）
- 按照music的id升序排序
- 结果集中不能存在重复性 （去重）



### 怎么做

- 三表查询只需要并列join 即可
- 如何筛掉用户喜欢的音乐  可以对music_likes这张表 的music_id字段 进行 not in （select 查询）该select查询 查询出用户=1喜欢的音乐id
- 下面我们就能得到初步筛好的数据，接下来只剩下去重和排序

```
select
    m2.music_name
from
    follow f
    join music_likes m1 on f.follower_id = m1.user_id
    join music m2 on m1.music_id = m2.id
    #筛选出user_id=1关注的用户 和 去掉user_id喜欢的音乐
where
    f.user_id = 1
    and m1.music_id not in(
        select
            music_id
        from
            music_likes
        where
            user_id = 1
    )
```

- 但有个坑，如果我们直接使用distinct 和 order by m2.id 我们会报错（如下代码）

SQL_ERROR_INFO: "Expression #1of ORDER BY clause is not in SELECT list,

references column 'm.id'which is not in SELECT list; thisis incompatible with DISTINCT"

```
select
    distinct m2.music_name
from
    follow f
    join music_likes m1 on f.follower_id = m1.user_id
    join music m2 on m1.music_id = m2.id
    #筛选出user_id=1关注的用户 和 去掉user_id喜欢的音乐
where
    f.user_id = 1
    and m1.music_id not in(
        select
            music_id
        from
            music_likes
        where
            user_id = 1
    )
order by m2.id
```

- 因此我们采用group by 进行去重，并将结果用子查询包装

### 代码展示

```
select 
    ans.music_name
from (
    select
       m2.id,m2.music_name
    from 
        follow f
    join music_likes m1 on f.follower_id = m1.user_id
    join music m2 on m1.music_id = m2.id
    #筛选出user_id=1关注的用户 和 去掉user_id喜欢的音乐
    where f.user_id = 1 and  m1.music_id not in (
        select 
            music_id
        from music_likes
        where user_id = 1)
    group by m2.id,m2.music_name
) ans
order by ans.id
```