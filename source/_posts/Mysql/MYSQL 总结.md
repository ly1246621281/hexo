---
categories:
  - Mysql
---
# MYSQL 总结

## 1. tip

* mysql建表不能重复列名，表关联或者起别名可以重复列名，即 ```a.*,b.*（包括a.activityId,b.activityId）```是可以的。
* 

## 2.语法

1.  mybatis 分页加序号


> ```sql
> select (@mycnt := cast(@mycnt as SIGNED)+1) as rownum,
>      b.* 
> from(
> 	select * from xx
>   limit #{pageInfo.startRow}, #{pageInfo.pageSize}
>  )b , (select @mycnt := #{pageInfo.startRow})  num
> ```

2. 时间sql

   ```sql
   -- between and
   activity_dt between date('2020-09-01 00:00:00.0') and date('2020-09-29 00:00:00.0') and activity_dt between date(start_time) and date_add(end_time, interval 60 day)
   -- 格式化
   date_format(activity_dt, '%Y-%m-%d') startActivityDt 
   -- 按周统计，当前日期所在周的周一
   DATE_FORMAT(STR_TO_DATE(CONCAT(YEAR(activity_dt),weekofyear(activity_dt )-1,'Monday') , '%X %V %W %u'), '%Y-%m-%d') as startActivityDt
   DATE_FORMAT(STR_TO_DATE(CONCAT(YEAR(activity_dt),weekofyear(activity_dt ),'Sunday') , '%X %V %W %u'), '%Y-%m-%d') as endActivityDt ,
           
   -- 按月统计，月初以及月末日期
    DATE_ADD(activity_dt,interval -day(activity_dt)+1 day) as startActivityDt,
    last_day(activity_dt) as endActivityDt ,
   -- 按季统计
    -- 当前quarter的第一天：
   concat(date_format(LAST_DAY(MAKEDATE(EXTRACT(YEAR FROM  activity_dt),1) + interval QUARTER(activity_dt)*3-3 month),'%Y-%m-'),'01') as startActivityDt,
    -- 当前quarter的最后一天：
   LAST_DAY(MAKEDATE(EXTRACT(YEAR  FROM activity_dt),1) + interval QUARTER(activity_dt)*3-1 month)as endActivityDt,
   ```

3. 