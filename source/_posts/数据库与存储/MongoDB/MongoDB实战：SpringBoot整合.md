---
title: MongoDB实战：SpringBoot整合
date: 2021-11-16 09:49:19
top_img: https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93%E4%B8%8E%E5%AD%98%E5%82%A8/MongoDB/MongoDB%E5%AE%9E%E6%88%98%EF%BC%9ASpringBoot%E6%95%B4%E5%90%88/preview.jpg
cover: https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93%E4%B8%8E%E5%AD%98%E5%82%A8/MongoDB/MongoDB%E5%AE%9E%E6%88%98%EF%BC%9ASpringBoot%E6%95%B4%E5%90%88/preview.jpg
tags:
  - 数据库与存储
  - MongoDB
categories: 数据库与存储
---



# 准备工作



1. 引入依赖

   ```xml
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-data-mongodb</artifactId>
   </dependency>
   ```

2. 添加配置文件

   ```yml
   spring:
     data:
       mongodb:
         uri: mongodb://username:password@ip:port/database?authSource=admin
   ```

3. 注入操作对象

   ```
   @Resoure
   private MongoTemplate mongoTemplate;
   ```

4. 创建实体类

   ```java
   @Data
   // 表示 user 集合中的一个文档
   @Document("user")
   public class User {
   
   	// 指定文档 id
   	@Id
   	private Integer id;
   	
   	// 指定字段
   	@Field("username")
   	private String name;
   	
   	// 表示该字段不参与序列化
   	@Transient
   	private String password;
   
   }
   ```



------

# 基础操作

## 添加文档



```java
User user = new User();
user.setName("Simon");
// id 不存在则插入，id 存在则更新
mongoTemplate.save(user);
// id 存在则抛出异常，支持批量操作
mongoTemplate.insert(user);
```



------

## 查询文档



Criteria 是标准查询的接口，可以引用静态的 Criteria.where 把多个条件组合在一起，就可以将多个方法和查询条件连接起来，方便我们操作查询语句



|                  Criteria                   | MongoDB |   说明   |
| :-----------------------------------------: | :-----: | :------: |
|          Criteria and(String key)           |  $and   |    且    |
| Criteria andOperator(Criteria ... criteria) |  $and   |    且    |
| Criteria orOperator(Criteria ... criteria)  |   $or   |    或    |
|            Criteria gt(Obejct o)            |   $gt   |   大于   |
|           Criteria gte(Obejct o)            |  $gte   | 大于等于 |
|          Criteria in(Obejct ... o)          |   $in   |   包含   |
|            Criteria is(Obejct o)            |   $is   |   等于   |
|            Criteria lt(Obejct o)            |   $lt   |   小于   |
|           Criteria lte(Obejct o)            |  $lte   | 小于等于 |
|         Criteria nin(Obejct ... o)          |  $nin   |  不包含  |



```java
@Test
void test() {
	// 查询所有文档
	List<UserVO> users = mongoTemplate.findAll(UserVO.class);
	users.forEach(System.out::println);

	// 根据 id 查询
	UserVO user = mongoTemplate.findById(1, UserVO.class);
	System.out.println(user);

	// 返回第一个文档
	UserVO one = mongoTemplate.findOne(new Query(), UserVO.class);
	System.out.println(one);

	// 多条件查询，查询姓年纪大于 18 小于 40 的文档
	Query query = new Query(Criteria.where("age").gt(18).lt(40));
	List<UserVO> users = mongoTemplate.find(query, UserVO.class);
	// 模糊查询，java 中正则不需要 //
	Query query = new Query(Criteria.where("name").regex("陈"));
	List<UserVO> users = mongoTemplate.find(query, UserVO.class);

	// and or 多条件查询
	Criteria criteria = new Criteria();
	criteria.andOperator(Criteria.where("name").is("Simon"), Criteria.where("age").gt(18));
	criteria.orOperator(Criteria.where("name").is("Simon"), Criteria.where("age").gt(18));
	Query query = new Query(criteria);

	// sort 排序
	query.with(Sort.by(Sort.Order.desc("age")));

	// 分页
	query
		// 指定跳过的记录数
		.skip(0)
		// 每页显示的数量
		.limit(4);

	// 也可以直接手写 Mongo sql 查询
	String sql = "{$or: [{age:{$lt:25}}, {age:{$gt:40}}]}";
	Query query = new BasicQuery(sql);

}
```



---

## 更新文档



在 MongoDB 中无论使用客户端 API 还是使用 Spring Data，更新返回结果一定是受影响行数。如果没有数据需要进行更新则返回 0

* updateFirst() 只更新满足条件的第一条记录
* updateMulti() 更新所有满足条件的记录
* upsert() 没有满足更新条件的数据则新增



```java
@Test
void test() {
	// 设置更新条件
	Query query = new Query(Criteria.where("age").gt(18).lt(40));
    // 设置需要更新的内容
    Update update = new Update();
    update.set("phone", "123123");
    
    UpdateResult result = mongoTemplate.updateFirst(query, update, UserVO.class);
    UpdateResult result = mongoTemplate.updateMulti(query, update, UserVO.class);
    UpdateResult result = mongoTemplate.upsert(query, update, UserVO.class);
    
    // 返回修改的记录数
    System.out.println(result.getModifiedCouunt());

}
```



------

## 删除文档



```java
@Test
void test() {
	// 删除所有文档
	mongoTemplate.remove(new Query(), UserVO.class);
    // 删除满足条件的文档
	mongoTemplate.remove(new Query((Criteria.where("id").is(18)), UserVO.class);

}
```



---

# 聚合操作

## 实体类

```java
@Data
public class Zips {

    private Double[] loc;

    private Integer pop;

    private String state;
    
    private String city;

}
```



## 返回人口超过 100 万的州

```
db.zips.aggregate([
	{$group: {_id: "$state", totalPop: {$sum: $pop}}},
	{$match: {totalPop: {$gt: 10 * 1000 * 1000}}}
])
```

```java
@Test
void test() {
        
	// $group
	GroupOperation groupOperation = Aggregation.group("state").sum("pop").as("totalPop");
	// $match
	MatchOperation matchOperation = Aggregation.match(Criteria.where("totalPop").gte(10 * 1000 * 1000));
	// 按顺序组合每一个步骤
	TypedAggregation<Zips> typedAggregation = Aggregation.newAggregation(Zips.class, groupOperation, matchOperation);
	// 执行聚合操作，如果不使用 Map，也可以使用实体类来接收数据
	AggregationResults<Map> aggregationResults = mongoTemplate.aggregate(typedAggregation, Map.class);
	// 取出最终结果
	List<Map> maps = aggregationResults.getMappedResults();
	maps.forEach(System.out::println);

}
```



------

## 返回各州的平均城市人口



```
db.zips.aggregate([
	{$group: {_id: {state: "$state", city: $city}, cityPop: {$sum: $pop}}},
	{$group: {_id: "$_id.state", avgCityPop: {$avg: $cityPop}},
	{$sort: {avgCityPop: -1}}
])
```

```java
@Test
void test() {

	// $group
    GroupOperation groupOperation1 = Aggregation.group("state", "city").sum("pop").as("cityPop");
    // $group
    GroupOperation groupOperation2 = Aggregation.group("_id.state").avg("cityPop").as("avgCityPop");
    // $sort
    SortOperation sortOperation = Aggregation.sort(Sort.Direction.DESC, "avgCityPop");
    // 按顺序组合每一个步骤
    TypedAggregation<Zips> typedAggregation = Aggregation.newAggregation(Zips.class, groupOperation1, groupOperation2, sortOperation);
    // 执行聚合操作，如果不使用 Map，也可以使用实体类来接收数据
    AggregationResults<Map> aggregationResults = mongoTemplate.aggregate(typedAggregation, Map.class);
    // 取出最终结果
    List<Map> maps = aggregationResults.getMappedResults();
    maps.forEach(System.out::println);
        
}
```



------

## 按州返回最大的和最小的城市



```js
db.zips.aggregate([
	{$group: 
		{
			_id: {state: "$state", city: "$city"},
			pop: {$sum: "$pop"}
		}
	},
	{$sort: {pop: 1}},
	{$group:
		{
			_id: "$_id.state",
			biggestCity: {$last: "$_id.city"},
			biggestPop: {$last: "$pop"},
			smallestCity: {$first: "$_id.city"},
			smallestPop: {$first: "$pop"}
		}
	},
	{$project:
		{
			_id: 0,
			state: "$_id",
			biggestCity: {name: "$biggestCity", pop: "$biggestPop"},
			smallestCity: {name: "$smallestCity", pop: "$smallestPop"}
		}
	},
	{$sort: {state: 1}}
])
```

```java
@Test
void test() {

    // $group
    GroupOperation groupOperation1 = Aggregation.group("state", "city").sum("pop").as("pop");
    // $sort
    SortOperation sortOperation1 = Aggregation.sort(Sort.Direction.DESC, "pop");
    // $group
    GroupOperation groupOperation2 = Aggregation.group("_id.state")
            .last("_id.city").as("biggestCity")
            .last("pop").as("biggestPop")
            .first("_id.city").as("smallestCity")
            .first("pop").as("smallestPop");
    // $project
    ProjectionOperation projectionOperation = Aggregation.project("biggestCIty", "smallCity", "state")
            .andExclude("_id")
            .andExpression("{name: \"$biggestCity\", pop: \"$biggestPop\"}")
            .as("biggestCity")
            .andExpression("{name: \"$smallestCity\", pop: \"$smallestPop\"}")
            .as("smallestCity");
    // $sort
    SortOperation sortOperation2 = Aggregation.sort(Sort.Direction.DESC, "state");
    // 按顺序组合每一个步骤
    TypedAggregation<Zips> typedAggregation = Aggregation.newAggregation(Zips.class, groupOperation1, sortOperation1, groupOperation2, projectionOperation, sortOperation2);
    // 执行聚合操作，如果不使用 Map，也可以使用实体类来接收数据
    AggregationResults<Map> aggregationResults = mongoTemplate.aggregate(typedAggregation, Map.class);
    // 取出最终结果
    List<Map> maps = aggregationResults.getMappedResults();
    maps.forEach(System.out::println);

}
```

