# mongo template简单API

mongoTemplate简单API
不足之处欢迎留言
# 1、介绍
​​​​​​monTemplate基于​spring-data-mongo支持，springboot项目中引入核心依赖

<!--mongo-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-mongodb</artifactId>
</dependency>

# 2、简单API
## （1）查询
简单条件查询：BasicDBObject来封装

复杂条件查询：Query query用来封装查询条件

（1）查询全部=====db.集合名.find()

mongoTemplate.findAll(Class<T> entityClass, String collectionName) 

（2）查询符合条件中的第一条=====db.集合名.findOne(条件)

mongoTemplate.findOne(Query query, Class<T> entityClass, String collectionName)

（3）带条件查询=====db.集合名.find(条件)

mongoTemplate.find(Query query, Class<T> entityClass, String collectionName)

Query使用

1、创建一个query对象（用来封装所有条件对象），

   再创建一个criteria对象（用来构建条件）

2、精准条件：criteria.and(“key”).is(“条件”)

   模糊条件：criteria.and(“key”).regex(“条件”)

3、封装条件：query.addCriteria(criteria)

4、大于（创建新的criteria）：Criteria gt = Criteria.where(“key”).gt（“条件”）
   小于（创建新的criteria）：Criteria lt = Criteria.where(“key”).lt（“条件”）

5、Query.addCriteria(new Criteria().andOperator(gt,lt));

6、一个query中只能有一个andOperator()。其参数也可以是Criteria数组。

7、排序  ：query.with（new Sort(Sort.Direction.ASC, "age"). and(new Sort(Sort.Direction.DESC, "date")))

## （2）新增
MongoTemplate实现了MongoOperations接口

1、mongoTemplate.insert()-------------（MongoTemplate中）

2、mongoTemplate.save()--------------（MongoOperations中）

区别
insert: 若新增数据的主键已经存在，则会抛 org.springframework.dao.DuplicateKeyException 异常提示主键重复，不保存当前数据。
save: 若新增数据的主键已经存在，则会对当前已经存在的数据进行修改操作。

insert: 可以一次性插入一整个列表，而不用进行遍历操作，效率相对较高
save: 需要遍历列表，进行一个个的插入。

## （3）删除
mongoTemplate.remove(Query query,Class<T> entityClass)

## （4）修改
Update update = Update.update("要更新的字段", "更新的值");

1、方式一
mongoTemplate.upsert(query, update, Class<T> entityClass);

2、方式二
更新满足条件的第一条数据
mongoTemplate.updateFirst(query, update, Class<T> entityClass);

更新所有满足条件的数据
mongoTemplate.updateMulti(query, update, Class<T> entityClass);

区别
upsert：如果query条件没有筛选出对应的数据，那么upsert会插入一条新的数据。
update：如果query条件没有筛选出对应的数据，那么什么都不做。

原文链接：https://blog.csdn.net/weixin_44004647/article/details/88580208