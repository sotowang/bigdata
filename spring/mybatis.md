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

RowBounds 是一次性查询全部结果吗？为什么？

mybatis 逻辑分页和物理分页的区别是什么？

mybatis 是否支持延迟加载？延迟加载的原理是什么？

说一下 mybatis 的一级缓存和二级缓存？

mybatis 和 hibernate 的区别有哪些？

mybatis 有哪些执行器（Executor）？

mybatis 分页插件的实现原理是什么？

mybatis 如何编写一个自定义插件？



