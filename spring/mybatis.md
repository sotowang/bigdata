# mybatis 中 #{}和 ${}的区别是什么？

 [第五章：Mybatis中的 #{} 和 ${}](https://zhuanlan.zhihu.com/p/40468976)

## **#{} 和 ${} 区别**

1、在 MyBatis 中提供了两种方式读取参数的内容到SQL语句中,分别是：

（1）#{参数名} :实体类对象或则 Map 集合读取内容

（2）${参数名} :实体类对象或则 Map 集合读取内容

2、两者的区别：

（1）#{} : 采用预编译方式，可以防止 SQL 注入

（2）${}:  采用直接赋值方式，无法阻止 SQL 注入攻击

在大多数情况下，我们都是采用 #{} 读取参数内容。但是在一些特殊的情况下，我们还是需要使用 ${} 读取参数的。比如有两张表，分别是 dept 和 dept_01。如果需要在查询语句中动态指定表名，就只能使用 ${} 。再比如：需要动态的指定查询中的排序字段，此时也只能使用 ${}，简单来说，在 JDBC 不支持使用占位符的地方，都可以使用 ${}。

3、既然我们已经明白了两者的区别，下面我们就对 ${} 的使用场景“动态指定表名”进行一下演示：

我在数据库中将 dept 拷贝了一份，并修改了表中的数据，然后我们看一下具体的语句：

```sql
<select id="chooseTable" parameterType="map" resultType="Dept">
	select * from ${tableName}
	where deptno = #{deptno}
</select>
```

这里数据库表名使用了 ${}，查询条件使用了 ${}，这样能够直观的看到两者在 sql 层面上的区别。我们看一下测试情况：

```sql
@Test
public void test02() throws IOException {
	DeptMapper deptMapper = session.getMapper(DeptMapper.class);
	Map<String, String> map = new HashMap<>();
	map.put("tableName", "dept_01");
	map.put("deptno", "2");
	Dept dept = deptMapper.chooseTable(map);
	System.out.println(dept);
}
```

![img](https://pic3.zhimg.com/v2-b2a48d2e1aac6e0a172b99786bc16b16_b.jpg)

虽然 ${} 的使用是不安全的，但是在平时的业务开发中还是有用武之地的，只要能够合理的运用这两种读取参数的方式，我相信你的 Mybatis 代码会写的十分灵活。

---

[Mybatis中foreach用法及#{}和${}的区别](https://zhuanlan.zhihu.com/p/39427928)

**Mybatis中用#{}和${}获取输入参数的区别**

1.“#{}“和“${}”都可以从接口输入中的map对象或者pojo对象中获取输入的参数值。例如：

```
<mapper namespace="com.hh.dao.UserDao">

<select id="selectByIdList" resultType="com.hh.domain.SysUser">

select * from user where id=#{id} and username=#{username}

</select>

</mapper>
或者
<mapper namespace="com.hh.dao.UserDao">

<select id="selectByIdList" resultType="com.hh.domain.SysUser">

select * from user where id=${id} and username=${username}

</select>

</mapper>
```

上面两种形式都可以通过id和username条件查询用户信息，但是这两种形式是有本质的区别：mybatis在处理${}形式时，会直接吧{id}和{username}获取的值拼接到sql中。例如{id}的值为“11”,{username}的值为“hh”,即mybatis直接处理sql语句为：

```
select * from user where id="11" and username="hh"
```

mybatis在处理#{}形式时，会通过jdbc中的PreparedStatement先预编译sql语句为下列形式：

```
select * from user where id=? and username=?
```

然后在用PreparedStatement把对应占位符处的值代替占位符。 
2.#{}比${}的优势 
id和username取出来的值会直接拼接到sql语句中，会有安全问题。 
例如：id对应的值为“11”#，则{id}获取到的值为“11”#，此时根本不用管username是什么了，例如username的值为“hh”,则{username}获取到的值为“hh”,mybatis会拼接到sql为：

```
select * from use where id="11"# and username="hh"
```

由于sql中#表示注释的意思，所以mybatis就把上面的语句翻译为

```
select * from user where id="11"
```

执行词条语句，根本不关心username，因此会有安全问题。 
但是用#{}的形式就不会出现这种情况，对于#{}的形式，mybatis会先提前预编译sql语句，然后再将参数设置到sql语句中，防止sql注入。



# mybatis 有几种分页方式？

# RowBounds 是一次性查询全部结果吗？为什么？

# mybatis 逻辑分页和物理分页的区别是什么？

# mybatis 是否支持延迟加载？延迟加载的原理是什么？

# 说一下 mybatis 的一级缓存和二级缓存？

<<Mybatis从入门到精通>>

先通过一个简单示例来看看 MyBatis 一级缓存如何起作用 。在 src.mybatis.simple.mapper 包
下 ， 新建如下测试类。

```java
public class CacheTest extends BaseMapperTest {
@Test
public void testLlCache() {
//获取 SqlSession
SqlSession sqlSession = getSqlSession() ;
SysUser userl = null ;
try {
//获取 UserMapper 接口
UserMapper userMapper = sqlSession . getMapper (UserMapper . class) ;
//调用 selectByid 方法，查询 id = 1 的用户
userl = userMapper.selectByid(ll);
//对当前获取的对象重新赋值
userl . setUserName (” New Name” ) ;
//再次查询获取 id 相同的用户
SysUser user2 = userMapper.selectByid(ll) ;
//虽然没有更新数据库，但是这个用户名和 userl 重新赋值的名字相同
Assert.assertEquals (” New Name”, user2 . getUserName()) ;
// 无论如何， user 2 和 userl 完全就是 同一个实例
Assert.assertEquals(userl , user2) ;
} finally {
//关 闭当前的 sqlSession
sqlSession.close() ;
System.out . println （ ”开启新的 sqlSession ”）；
//开始另一个新的 session
sqlSession = getSqlSession();
try {
//获取 UserMapper 接 口
UserMapper userMapper = sqlSession . getMapper (UserMapper . class) ;
//调 用 selectByid 方法，查询 id = 1 的用户
SysUser user2 = userMapper . selectByid(ll) ;
// ；第二个 session 获取的用户名仍然是 admin
Assert . assertNotEquals (” New Name”, user2.getUserName()) ;
//这里的 us er2 和前一个 session 查询的结采是两个不同的实例
Assert . assertNotEquals(userl , user2) ;
//执行7i11J 除操作
userMapper.deleteByid (2L);
//获取 user3
SysUser user3 = userMapper . selectByid(ll) ;
//这里的 user2 和 user3 是两个不同的实例
Assert . assertNotEquals(user2 , user3);
} finally {
//关闭 sqlSession
sqlSession.close () ;

```

先执行该测试输出日志，然后结合日志一起来看以上代码。输出日志如下。

```bash
DEBUG [main] - ==> Preparing: select * from sys user where id = ?
DEBUG [main] - ==>Parameters: l(Long)
TRACE [main] - <==Columns: id, user name , user password , user email ,
user l口 fo , head img , create time
TRACE [main] - <==Row : 1, admin , 123456 , admin@mybatis . tk ,
< BLOB> , < BLOB> , 2016-06-07 01:11 : 12.0
DEBUG [main] - <== Total : 1
开启新的 sqlSess 工on
DEBUG [ma 工n J . - ==>Preparing : select* from sys_user where id=?
DEBUG [main] - ==>Parameters : l(Long)
TRACE [main] - <== Columns ：工 d , user name , user password , user email ,
user info , head img , create time
TRACE [main] - <== Row: 1 , admin , 123456 , admin@mybatis . tk ,
< BLOB> , < BLOB> , 2016-06 - 07 01 : 11:12.0
DEBUG [main] - <==Total : 1
DEBUG [main] - ==> Preparing: delete from sys user where id = ?
DEBUG [main] - ==>Parameters : 2(Long)
DEBUG [main] - <== Updates: 0
DEBUG [main] - ==> Prepar 工口g: select * from sys_user where id = ?
DEBUG [main] - ==>Parameters : l(Long)
TRACE [main] - <== Columns: id , user name , user password, user ema 工 1 ,
user info , head img , create time
TRACE [main ］一＜＝＝ Row : 1 , adm工 n , 123456 , admin@mybatis . tk,
< BLOB> , < BLOB > , 2 016 -0 6-07 01:11:12 . 0
DEBUG [main] - <== Total : 1
```

在第一次执行 selectByid 方法获取 SysUser 数据时，真正执行了数据库查询，得到了user1 的结果。第二次执获取 user2 的时候，从日志可以看到 ， 在“开启新的 sqlSession ”这行日志上面，只有一次查询，也就是说第二次查询并没有执行数据库操作 。

从测试代码来看，获取 userl 后重新设置了 userName 的值，之后没有进行任何更新数据库的操作。在获取 user2 对象后 ， 发现 user2 对象的 userName 值竟然和 userl 重新设置后的值一样。再往下可以发现 ， 原来userl 和 user2 竟然是同一个对象，之所以这样就是因为 MyBatis 的一级缓存。

MyBatis 的一级缓存存在于 SqlSession 的生命周期中，在同一个 SqlSession 中查询时， MyBatis 会把执行的方法和参数通过算法生成缓存的键值，将键值和查询结果存入一个 Map对象中。如果同一个 SqlSession 中执行的方法和参数完全一致，那么通过算法会生成相同的键值，当 Map 缓存对象中己经存在该键值时，则会返回缓存中的对象。

缓存中的对象和我们得到的结果是同一个对象，反复使用相同参数执行同一个方法时， 总是返回同一个对象，因此就会出现上面测试代码中的情况。在使用 MyBatis 的过程中，要避免在使用如上代码中的 user2 时出现的错误。我们可能以为获取的 user2 应该是数据库中的数据，却不知道 userl 的一个重新赋值会影响到 user2 。 如果不想让 selectByid 方法使用 一级缓存 ，可以对该方法做如下修改。

```sql
<select id=” selectByid” flushCache=” true ” resultMap=”userMap” >
select * from sys user where id = #{id)
</select>
```

该修改在原来方法的基础上增加了 flushCache= " true ” ，这个属性配置为 true 后， 会在查询数据前清空当前的一级缓存，因此该方法每次都会重新从数据库中查询数据，此时的user2 和 user1 就会成为两个不同的实例 ， 可以避免上面的问题 。 但是由于这个方法清空了一级缓存 ， 会影响当前 SqlSession 中所有缓存的查询，因此在需要反复查询获取只读数据的情况下，会增加数据库的查询次数，所以要避免这么使用 。

在关闭第一个 SqlSessio口后 ，又重新获取了 一个 SqlSession ，因此又重新查询了user2 ，这时在日志中输出了数据库查询 SQL. user2 是一个新的实例，和 userl 没有任何关系 。 这是因为一级缓存是和 SqlSession 绑定的，只存在于 SqlSessio口的生命周期中。

接下来执行了一个 deleteByid 操作 ，然后使用相同的方法和参数获取了 user3 实例，从日志和结果来看， user3 和 user2 也是完全不同的两个对象。这是因为任何的 INSERT 、UPDATE 、 DELETE 操作都会清空一级缓存，所以查询 user3 的时候由于缓存不存在，就会再次执行数据库查询获取数据。

关于一级缓存中的各种情况，通过上面的测试都己经介绍完了 ，由 于一级缓存是在默默地
工作 ，因此要避免在使用过程中由于不了解而发生觉察不到的错误。



* 二级缓存

MyBatis 的 二级缓存非常强大，它不同于一级缓存只存在于 SqlSession 的生命周期中，而是可以理解为存在于 SqlSessionFactory 的生命周期中 。虽然目 前还没接触过同时存在多个 S qlSessionFactory 的情况，但可以知道，当存在多个 SqlSessioηFactory 时 ，它们的缓存都是绑定在各自对象上的，缓存数据在一般情况下是不相通的。只有在使用如 Redis这样的缓存数据库时，才可以共s享缓存。



# mybatis 和 hibernate 的区别有哪些？

mybatis 有哪些执行器（Executor）？

mybatis 分页插件的实现原理是什么？

mybatis 如何编写一个自定义插件？



