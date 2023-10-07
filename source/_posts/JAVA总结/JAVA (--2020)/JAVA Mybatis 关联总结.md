---
categories:
  - JAVA总结
  - JAVA (--2020)
---
# JAVA Mybatis 关联总结

## tip:

​	需求是三表关联，搞了三个实体类用activity_id做关联键，所以设计了一个实体类（包括基本类型活动号和三个表对应的三个实体类）。

用到了resultMap中的associate和实体类映射。

**association: 一对一关联(has one)**
**collection:一对多关联(has many)**

## 使用：

一对一（associate）的三种使用方式

 ### 1、第一种用法:association中使用select

```xml
<resultMap type="User" id="userMap">
    <result property="userName" column="user_name"/>
    <result property="age" column="age"/>
   <association property="card" column="card_id" select="com.zhu.ssm.dao.      CardDao.queryCardById">
   </association>
</resultMap>
 <select id="queryCardById" parameterType="int" resultType="Card">
        SELECT *
        FROM tb_card
        WHERE card_id=#{cardId}
 </select>
```

> 使用select是每次将 关联值 传给 column=“card_id”列
>
> - 嵌套查询:通过执行另外一个 SQL 映射语句来返回预期的复杂类型。会造成N+1现象，查询性能，不推荐
>
> - | 属性        | 描述                                                         |
>   | ----------- | ------------------------------------------------------------ |
>   | `column`    | 来自数据库的列名,或重命名的列标签。这和通常传递给 resultSet.getString(columnName)方法的字符串是相同的。 column 注 意 : 要 处 理 复 合 主 键 , 你 可 以 指 定 多 个 列 名 通 过 column= " {prop1=col1,prop2=col2} " 这种语法来传递给嵌套查询语 句。这会引起 prop1 和 prop2 以参数对象形式来设置给目标嵌套查询语句。 |
>   | `select`    | 另外一个映射语句的 ID,可以加载这个属性映射需要的复杂类型。获取的 在列属性中指定的列的值将被传递给目标 select 语句作为参数。表格后面 有一个详细的示例。 select 注 意 : 要 处 理 复 合 主 键 , 你 可 以 指 定 多 个 列 名 通 过 column= " {prop1=col1,prop2=col2} " 这种语法来传递给嵌套查询语 句。这会引起 prop1 和 prop2 以参数对象形式来设置给目标嵌套查询语句。 |
>   | `fetchType` | Optional. Valid values are `lazy` and `eager`. If present, it supersedes the global configuration parameter `lazyLoadingEnabled` for this mapping. |
>
>   [参考示例](https://www.w3cschool.cn/mybatis/f4uw1ilx.html)

### 2、第二种方法，嵌套resultMap

```xml
<resultMap type="Card" id="cardMap">
      <id property="cardId" column="card_id"/>
      <result property="cardNum" column="card_num"/>
      <result property="address" column="address"/>
</resultMap>

<resultMap type="User" id="userMap">
     <result property="userName" column="user_name"/>
     <result property="age" column="age"/>
     <association property="card" resultMap="cardMap">
     </association>
</resultMap>
```

第二种方法就是在UserDao.xml中先定义一个Card的resultMap，然后在User的resultMap的association标签中通过resultMap="cardMap"引用。这种方法相比于第一种方法较为简单。

**主要是可以resultmap 复用**

### 3、第三种方法:嵌套resultMap简化版

```xml
<resultMap type="User" id="userMap">
   <result property="userName" column="user_name"/>
   <result property="age" column="age"/>
   <association property="card" column="card_id" javaType="Card">
      <id property="cardId" column="card_id"/>
      <result property="cardNum" column="card_num"/>
      <result property="address" column="address"/>
    </association>
</resultMap> 
```

这种方法就把Card的resultMap定义在了association 标签里面，通过javaType来指定是哪个类的resultMap，个人认为这种方法最简单，缺点就是cardMap不能复用。具体用哪种方法，视情况而定。



作者：贪挽懒月
链接：https://www.jianshu.com/p/018c0f083501
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

### 4、Collection同 associate

```xml
<resultMap type="User" id="userMap">
    <result property="userName" column="user_name"/>
    <result property="age" column="age"/>
    <collection property="mobilePhone" column="user_id" ofType="MobilePhone">
      <id column="mobile_phone_id" property="mobilePhoneId" />
      <result column="brand" property="brand" />
      <result column="price" property="price" />
 	</collection>
</resultMap>
```

### 5、id & result

```
<id property="id" column="post_id"/>
<result property="subject" column="post_subject"/>
```

这些是结果映射最基本内容。id 和 result 都映射一个单独列的值到简单数据类型(字符 串,整型,双精度浮点数,日期等)的单独属性或字段。

这两者之间的唯一不同是 id 表示的结果将是当比较对象实例时用到的标识属性。这帮 助来改进整体表现,特别是缓存和嵌入结果映射(也就是联合映射) 。

## 总结：

使用过程模仿第三种嵌套方式，不一致的是将每个关联表均设置为一个实体类，使用关联键<id>标签关联。

* resultMap

![image-20201008164022322](C:\Users\zhangkai324\AppData\Roaming\Typora\typora-user-images\image-20201008164022322.png)

* sql

```xml
<select id="downloadTransform"  parameterType="map" resultMap="downloadEntity">
        select a.activity_id as 'activity_id','' as 'empty',
           a.*,b.*,c.*
        from
        (<include refid="downloadActivityInfo"></include>)a
        left join
        (<include refid="downloadTransformInfo"></include>)b
        on a.activity_id = b.activity_id
        left join
        (<include refid="getUserCountByActivity"></include>)c
        on a.activity_id = c.activity_id
 </select>
```

* 实体类

```java
@Data
@Builder
@AllArgsConstructor //全参构造函数
@NoArgsConstructor  //无参构造函数
public class DownloadTransformDetailsPO {
    private Long activityId;
    private String empty;
    
    DownloadActivityPO downloadActivityPO;
    DownloadTransformPO downloadTransformPO;
    DownloadUserPO downloadUserPO;
}
```

注意：

@Builder只会生成有参的构造方法，会取消默认的无参构造方法。

![image-20201008201207621](C:\Users\zhangkai324\AppData\Roaming\Typora\typora-user-images\image-20201008201207621.png)

若关联的实体类未提供无参构造函数，可能出现indexOutofBounds异常  或者 类型不匹配，走的构造函数是有参构造函数那块。