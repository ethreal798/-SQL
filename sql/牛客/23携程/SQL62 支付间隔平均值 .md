# 格式处理

## SQL62 支付间隔平均值  将浮点数->整数

### 做什么

【问题】从订单链路出发，找出同一个order_id下order_log与select_log相差时间的绝对值的平均值(logtime，以s为单位)，以整数形式返回

【示例结果】

返回列：

gap

60

【示例解析】

1.基于order_id关联出，order_log和select_log日志在同一订单号order_id下的order_log中的logtime与select_log中的logtime的间隔

2.对间隔时间求绝对值再求平均值最后返回订单号与间隔时间(s)

注：logtime为日志采集时间，可能局部乱序；无需考虑为空



- 就是让我们根据order_id关联两表求时间的差值 以秒为单位  最后求平均值  并以整数输出



### 怎么做

- 求时间差值 秒为单位  使用timestampdiff函数
- avg求平均值  但是注意 这里得到的结果是浮点数 
- cast(num as signed) 转化成整数
- join on两表即可



### 代码展示

```
select
cast(avg(abs(timestampdiff(second,o.logtime,s.logtime)))as signed ) gap
from 
    order_log o
join 
    select_log s
on o.order_id = s.order_id
```

 