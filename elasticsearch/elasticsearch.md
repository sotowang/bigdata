# Solr与ES

* 区别
  * 当实时建立索引时，Solr会产生io阻塞，查询性能较差，ES具有明显优势
  * 随着数据量增加，Solr的搜索效率会变低，而ES却无明显变化
  * Solr支持更多格式数据，ES仅支持json

# Logstash

* 是ELK的中央数据流引擎，用于从不同目标（文件/数据存储/MQ)的收集的不同格式数据，经过过滤后支持输出到不同目的地（文件/MQ/redis/elasticsearch/kafka等）。

# Kibana

* 可以将es的数据通过友好的界面展示出来

* 提供实时分析的功能

* 测试

  ```json 
  GET _analyze
  {
    "analyzer": "ik_smart",
     "text": "测试文档"
  }
  GET _analyze
  {
    "analyzer": "ik_max_word",
    "text": "测试文档"
  }
  ```

> IKAnalyzer.cfg.xml

```xml
?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE properties SYSTEM "http://java.sun.com/dtd/properties.dtd">
<properties>
        <comment>IK Analyzer 扩展配置</comment>
        <!--用户可以在这里配置自己的扩展字典 -->
        <entry key="ext_dict"></entry>
         <!--用户可以在这里配置自己的扩展停止词字典-->
        <entry key="ext_stopwords"></entry>
        <!--用户可以在这里配置远程扩展字典 -->
        <!-- <entry key="remote_ext_dict">words_location</entry> -->
        <!--用户可以在这里配置远程扩展停止词字典-->
        <!-- <entry key="remote_ext_stopwords">words_location</entry> -->
</properties>
```



# ElasticSearch

* 面向文档，索引即为一个数据库，文档即为库中的数据
* Types 逐渐被弃用

## 与关系型数据库对比

| 关系型DB           | ES              |
| ------------------ | --------------- |
| 数据库（database） | 索引（indices） |
| 表（tables）       | types（弃用）   |
| 行（rows)          | documents       |
| 字段（columns）    | fields          |

## 倒排索引

采用Lucene倒排索引作为底层，这种结构适用于快速的全文搜索，一个索引由文档中所有不重复的列表构成。对于每一个词，都有一个词包含它的文档列表。

例如，现在2个文档，每个文档包含的内容如下：

```yml
study every day, good good up to forever # 文档1包含内容
To forever, study every day,good good up # 文档2包含内容
```

为了创建倒排索引，我们首先要将每个文档拆分成独立的词（或称为词条或者tokens），然后创建一个包含所有不重复的词条的排序列表，然后列出每个词条出现在哪个文档

| Term  | Doc_1 | Doc_2 |
| ----- | ----- | ----- |
| study | V     | X     |
| To    | X     | X     |
| every | V     | V     |
| day   | v     | v     |

## IK分词器

> elasticsearch-plugin install https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v6.8.8/elasticsearch-analysis-ik-6.8.8.zip

* 分词
  * 把一段中文或别的词划分成一个个关键字，我们在搜索时会把信息进行分词，会把数据库中或者索引库中的数据进行分词，然后进行一个匹配操作，默认的中文分词是将每个字看成一个词。显然是不够用的，我们需要安装中文分记词器IK来解决这个问题
  * 如果要使用中文，建议使用中文分词器
* IK提供两个分词算法
  * ik_smart 为最少切分
  * ik_max_word 为最细粒度切分  

## 增删改查

| method         | url                                             | 描述                   |
| -------------- | ----------------------------------------------- | ---------------------- |
| PUT            | localhost:9200/索引名称/类型名称/文档id         | 创建文档（指定文档id） |
| POST           | localhost:9200/索引名称/类型名称                | 创建文档（随机文档id） |
| POST(更新推荐) | localhost:9200/索引名称/类型名称/文档id/_update | 修改文档               |
| DELETE         | localhost:9200/索引名称/类型名称/文档id         | 删除文档               |
| GET            | localhost:9200/索引名称/类型名称/文档id         | 查询文档通过文档id     |
| POST           | localhost:9200/索引名称/类型名称/文档id/_search | 查询所有数据           |

* 创建一个索引

  ```json 
   PUT /索引名称/类型名称/文档id
  {请求体}
  ---
  PUT /test/type/doc1
  {
    "name":"王**",
    "age":100
  }
  ```

## 复杂查询

* _source 结果过滤
* sort 排序
* 分页
  * from（从第几个数据开始），size（返回数据量）

* 布尔值bool 查询(多条件查询) 
  * must  ，should，must_not
* 过滤器filter
  * range ，gte，gt，lt，lte
* 多条件查询
* 精确查询 term
* 分词
  * term 直接查询精确查询
  * match 会使用分词器解析（先分析文档，再在分析后的文档中查询）
* 高亮查询
  * highlight，fields，pre_tags，post_tags

## 集成SpringBoot

* 批处理插入

  ```java
  for(int i=0;i<userList.size();i++){
    bulkRequest.add(new IndexRequest("aaa_index")
                   .id(""+(i+1))
                   .source(JSON.toJSONString(userList.get(i)),XContentType.JSON));
  }
  BulkResponse bulkResponse = client.bulk(bulkRequest,RequestOptions.DEFAULT);
  ```

* 查询

  ```java
  SearchRequest searchRequest = new SearchRequest("aaa_oindex");
  //构建搜索条件
  SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
  //查询条件，TermQueryBuilder精确匹配
  TermQueryBuilder termQueryBuilder = QueryBuilders.termQuery("name","aaa");
  sourceBuilder.query(termQueryBuilder);
  
  sourceBuilder.timeOut(new TimeValue(60,TimeUnit,SECONDS));
  searchRequest.source(sourceBuilder);
  
  ```

# Elasticsearch中数据是如何存储的