# Maven生命周期

Maven 的内部有三个构建生命周期，分别是 clean, default, site

1、**clean 周期**：主要用于清理上一次构建产生的文件，可以理解为删除target目录 

2、**默认周期**， 
主要阶段包含:

process-resources 默认处理src/test/resources/下的文件，将其输出到测试的classpath目录中, 
compile 编译src/main/java下的java文件，产生对应的class, 
process-test-resources 默认处理src/test/resources/下的文件，将其输出到测试的classpath目录中, 
test-compile 编译src/test/java下的java文件，产生对应的class, 
test 运行测试用例, 
package 打包构件，即生成对应的jar, war等, 
install将构件部署到本地仓库, 
deploy 部署构件到远程仓库 
3、**site周期**

主要阶段包含

site 产生项目的站点文档 
site-deploy 将项目的站点文档部署到服务器



# Maven常见的依赖范围有哪些?

**compile**:编译依赖，默认的依赖方式，在编译（编译项目和编译测试用例），运行测试用例，运行（项目实际运行）三个阶段都有效，典型地有spring-core等jar。 

**test**:测试依赖，只在编译测试用例和运行测试用例有效，典型地有JUnit。 

**provided**:对于编译和测试有效，不会打包进发布包中，典型的例子为servlet-api,一般的web工程运行时都使用容器的servlet-api。 

**runtime**:只在运行测试用例和实际运行时有效，典型地是jdbc驱动jar包。 

**system**: 不从maven仓库获取该jar,而是通过systemPath指定该jar的路径。 

**import**: 用于一个dependencyManagement对另一个dependencyManagement的继承。

