# GitHub常用操作

* 查询关键词

* `keyword in:name,readme,description`

* 范围查询
  * 按点赞数`springboot starts:>=5000`
  * 按forks数`springboot forks:>=500`
  * 组合使用`springboot forks:100..200 stars:80..100`
* 加增搜索
  * `awesome redis`
* 关键代码高亮
  * `URL+#L13-L23`13-23行高亮显示
* 项目内搜索
  * `t`

# rebase（变基）

> 在当前分支外另一点上重新应用当前分支的提交历史

![img](https://segmentfault.com/img/remote/1460000005937418)

> 在对feature分支rebase，等价于创建了新的提交，并且老的提交也没有被销毁，只是不再被访问或使用

* rebase会改变server端的commit历史



# reset

> git reset --hard HEAD^^^^^^

