#### 字符串函数的特殊应用

##### SQL45 统计所有课程参加培训人次

###### 做什么   （对course这个字符串进行抽象统计，有一个逗号代表2个课以此类推）

问题：请统计该公司所有课程参加培训人次？

| info_id | staff_id | course                  |
| ------- | -------- | ----------------------- |
| 101     | 1        | course1,course2         |
| 102     | 2        | course2                 |
| 103     | 3        | course1,course3         |
| 104     | 4        | course1,course2,course3 |
| 105     | 5        | course3                 |
| 106     | 6        | NULL                    |
| 107     | 7        | course1,course2         |

示例数据结果如下：

| staff_nums |
| ---------- |
| 11         |

解释：course1课程共有员工1、3、4、7共4名员工培训；

course2课程共有员工1、2、4、7共4名员工培训；

course3课程共有员工3、4、5共3名员工培训。

###### 怎么做

- 使用length()函数得到字符串长度
- 使用replace()函数将',' 替换成 ''   再使用length()得到长度
- 将这两个长度相减并+1 就是答案

###### 代码展示

```SQL
select 
sum(length(course)-length(replace(course,',',''))+1) staff_nums
from cultivate_tb
```







