---
categories:
  - JAVA总结
  - JAVA（2020--2023）
---
# SQL行转列，列转行

## 行转列

1.左关联  

```sql
select a.specialCount, b.commonCount from 
(select '' as col ,count(1) as specialCount
from order_record 
where id >(select version from global_value where type=71) and dc_id=5 and store_id=222
)a
left join 
(select '' as col ,count(1) as commonCount
from order_record 
where id >(select version from global_value where type=71) and send_type=1 and ref_order_id is null 
)b
on a.col = b.col
```

2.if+聚合函数

```sql
select sum(if(uuid='专仓试用品',matchCount,0))as specialType,sum(if(uuid='同仓试用品',matchCount,0))as commonType,
sum(if(uuid='虚拟仓虚拟品',matchCount,0)) as virtualType from (
select uuid, sum(data_id)   as matchCount from temp_data
 where id>9939 and substring(create_by,1,10)  = date('2022-06-18') and uuid in ('专仓试用品','同仓试用品','虚拟仓虚拟品') 
 group by uuid 
 )t
```



## 列转行

1. union all 

   多个字段分别从一个表中获取，然后union all累积起来

2. group by  + case  when

   ```sql
   SELECT
     case
       when dc_id = 5 and store_id = 222 then '专仓' 
       when send_type=1 and ref_order_id is null then '同仓' 
       when dc_id = 999 and store_id = 999 then '虚拟仓'
       else '其他' end as type,
     count(1) cnt
   from order_record
   where id >911938094 and id <=911948094 
   group by
     type
   ```

   ![image-20221025173730741](https://raw.githubusercontent.com/ly1246621281/PicGo/main/img/image-20221025173730741.png)

引申：

```sql

  SELECT CASE 
            WHEN b.extend_value2 = 1 THEN '专仓试用品'
            WHEN b.extend_value2 = 0
                AND b.extend_value1 = 1
            THEN '同仓试用品'
            WHEN b.extend_value2 = 2 THEN '虚拟仓虚拟品'
        END AS type, sum(num * status_pre_code) AS money, 
        CASE 
            WHEN b.extend_value2 = 1 THEN 2
            WHEN b.extend_value2 = 0
                AND b.extend_value1 = 1
            THEN 1
            WHEN b.extend_value2 = 2 THEN 3 
        END AS rank 
    FROM (
        SELECT activity_id, count(1) AS num
        FROM order_record
        WHERE id > (
            SELECT version
            FROM global_value
            WHERE type = 71
        )
        GROUP BY activity_id
    ) a
        LEFT JOIN monitor_log b ON a.activity_id = b.`type`
    GROUP BY 
    type,rank
```

注意：

1.sum(num * status_pre_code) AS money,  和 sum(num)*  status_pre_code AS money,  区别

2.增加独立列去排序。