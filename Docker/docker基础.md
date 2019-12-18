# Docker相关命令

## 容器生命周期管理

```properties
docker run :创建一个新的容器并运行一个命令
docker start: 启动一个或多个已经被停止的容器
docker stop: 停止一个运行中的容器
docker restart:重启容器
docker kill : 杀掉一个运行中的容器
docker rm :删除一个或多个容器
docker pause:暂停容器中所有的进程
docker unpause:恢复容器中所有的进程
docker create: 创建一个新的容器但不启动它

```

### 修改容器内容

> docker exec 

* 容器启动后，可以使用`docker exec -it xx bash`命令再次进行修改

### 在运行的容器中执行命令

## 容器操作

```properties
docker ps : 列出容器
docker inspect: 获取容器/镜像的元数据
docker top :查看容器中运行的进程信息,支持ps命令参数
docker attach: 连接到正在运行中的容器
docker events: 从服务器获取实时事件
docker logs:获取容器的日志
docker wait: 阻塞运行直到容器停止，然后打印出它的退出代码
docker export :将文件系统作为一个tar归档文件导出到STDOUT
docker port :列出指定的容器的端口映射，或者查找将PRIVATE_PORT NAT到面向公众的端口
```

## 容器rootfs命令

```properties
docker commit :从容器创建一个新的镜像（提交镜像）
docker cp :用于容器与主机之间的数据拷贝
docker diff : 检查容器里文件结构的更改
```

## 镜像仓库

```properties
docker login : 登陆到一个Docker镜像仓库，如果未指定镜像仓库地址，默认为官方仓库 Docker Hub
docker logout : 登出一个Docker镜像仓库，如果未指定镜像仓库地址，默认为官方仓库 Docker Hub
docker pull : 从镜像仓库中拉取或者更新指定镜像(下载镜像)
docker push : 将本地的镜像上传到镜像仓库,要先登陆到镜像仓库
docker search : 从Docker Hub查找镜像
```

## 本地镜像管理

```properties
docker images : 列出本地镜像
docker rmi : 删除本地一个或多少镜像
docker tag : 标记本地镜像，将其归入某一仓库
docker history : 查看指定镜像的创建历史
docker save : 将指定镜像保存成 tar 归档文件
docker import : 从归档文件中创建镜像
```

### 构建镜像 

>  docker build

* 根据 Dockerfile 文件及上下文构建新 Docker 镜像

* 通过`.dockerignore`文件排除上下文目录下不需要的文件和目录。

* Dockerfile 一般位于构建上下文的根目录下，也可以通过`-f`指定该文件的位置

  ```dockerfile
  docker build -f /path/to/a/Dockerfile . 
  ```

* 镜像标签

  * 如果存在多个仓库下，或使用多个镜像标签，就可以使用多个`-t`参数：

    ```
    docker build -t nginx/v3:1.0.2 -t nginx/v3:latest .
    ```

* 缓存

  * Docker 守护进程会一条一条的执行 Dockerfile 中的指令，而且会在每一步提交并生成一个新镜像，最后会输出最终镜像的ID。
  * 生成完成后，Docker 守护进程会自动清理你发送的上下文。
  * Docker 会重用已生成的中间镜像，以加速docker build的构建速度
  * 可以通过`--cache-from`指定缓存。指定后将不再使用本地生成的镜像链，而是从镜像仓库中下载。

## info|version

```properties
docker info : 显示 Docker 系统信息，包括镜像和容器数。。
docker version :显示 Docker 版本信息
```

# Dockerfile

* 基础镜像信息

* 维护者信息

* 镜像操作指令

* 容器启动执行指令

* Dockerfile 指令

  * FROM

    * dockerfile中第一条非注释命令
    * 创建多个镜像时FROM可以多次出现,需在每个新命令FROM之前,记录提交上次的镜像ID
    * tag或digest可选,默认使用latest版本基础镜像

  * RUN

    * RUN创建的中间镜像会被缓存,并在下次构建中使用

    * 如果不想使用缓存镜像,`--no-cache`参数

      > docker build --no-cache

  * COPY 复制文件

    * 源文件的各种元数据都会保留(读,写,执行权限,文件变更时间)

  * ADD 更高级的复制文件

    * 在COPY基础上增加功能,如`<源路径>可以是URL`

  * ENV设置环境变量

    ```
    ENV VERSION=1.0 DEBUG=on \
        NAME="Happy Feet"
    ```

  * EXPOSE

    * 为构建的镜像设置监听端口，使容器在运行时监听
    * EXPOSE 指令并不会让容器监听 host 的端口，在 docker run 时使用 `-p`、`-P` 参数来发布容器端口到 host 的某个端口上。

  * VOLUME 定义匿名卷

    * VOLUME 让我们可以将源代码、数据或其它内容添加到镜像中，而又不并提交到镜像中，并使我们可以多个容器间共享这些内容。 

  * WORKDIR 指定工作目录

    * 通过WORKDIR设置工作目录后，Dockerfile 中其后的命令 RUN、CMD、ENTRYPOINT、ADD、COPY 等命令都会在该目录下执行。

  * USER 指定当前用户

    * 使用USER指定用户时，可以使用用户名、UID 或 GID，或是两者的组合
    * 使用USER指定用户后，Dockerfile 中其后的命令 RUN、CMD、ENTRYPOINT 都将使用该用户。镜像构建完成后，通过 docker run 运行容器时，可以通过 `-u` 参数来覆盖所指定的用户。

  * CMD

    * CMD用于指定在容器启动时所要执行的命令

  * ENTRYPOINT

    * ENTRYPOINT 用于给容器配置一个可执行程序。每次使用镜像创建容器时，通过 ENTRYPOINT 指定的程序都会被设置为默认程序。
    * ENTRYPOINT 与 CMD 非常类似，不同的是通过`docker run`执行的命令不会覆盖 ENTRYPOINT，而`docker run`命令中指定的任何参数，都会被当做参数再次传递给 ENTRYPOINT

# Spring Boot 项目添加Docker支持

* 在 `pom.xml-properties`中添加 Docker 镜像名称

  ```xml
  <properties>
    <docker.image.prefix>springboot</docker.image.prefix>
  </properties>
  ```

* plugins 中添加Docker构建插件

  ```xml
  <build>
      <plugins>
          <plugin>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-maven-plugin</artifactId>
          </plugin>
          <!-- Docker maven plugin -->
          <plugin>
              <groupId>com.spotify</groupId>
              <artifactId>docker-maven-plugin</artifactId>
              <version>1.0.0</version>
              <configuration>
                  <imageName>${docker.image.prefix}/${project.artifactId}</imageName>
                  <dockerDirectory>src/main/docker</dockerDirectory>
                  <resources>
                      <resource>
                          <targetPath>/</targetPath>
                          <directory>${project.build.directory}</directory>
                          <include>${project.build.finalName}.jar</include>
                      </resource>
                  </resources>
              </configuration>
          </plugin>
          <!-- Docker maven plugin -->
      </plugins>
  </build>
  ```

* 在目录`src/main/docker`下创建Dockerfile文件

  ```dockerfile
  FROM openjdk:8-jdk-alpine
  VOLUME /tmp
  ADD spring-boot-docker-1.0.jar app.jar
  ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
  ```

  * FROM ，表示使用 Jdk8 环境 为基础镜像，如果镜像不是本地的会从 DockerHub 进行下载
  * VOLUME ，VOLUME 指向了一个`/tmp`的目录，由于 Spring Boot 使用内置的Tomcat容器，Tomcat 默认使用`/tmp`作为工作目录。这个命令的效果是：在宿主机的`/var/lib/docker`目录下创建一个临时文件并把它链接到容器中的`/tmp`目录
  * ADD ，拷贝文件并且重命名
  * ENTRYPOINT ，为了缩短 Tomcat 的启动时间，添加`java.security.egd`的系统属性指向`/dev/urandom`作为 ENTRYPOINT

# 镜像精简、加速

镜像精简、加速分发部署的关键就在于巧妙利用这种增量机制，主要有两种途径：

- 减小镜像体积
  - 缩减层的体积。在**同一条命令**的最后，尽可能删除所有中间步骤的遗留文件。见下节示例-1。
  - 减少层的数量。因为是增量存储，所以即便是删除操作，也不会有负体积的层出现。主要作用在于拉取镜像时，减少处理层与层关系的时间。见下节示例-2。
- 充分复用已经存在下层镜像
  - 使用公共的基础镜像。我们在 library 命名空间下提供了经过修改的标准镜像，包括 YUM 源与时区的本地化等，比如 docker-registry.***。见下节示例-3。
  - Dockerfile 语句按修改频率排序。这样，不但可以减少拉镜像时的工作量，而且在制作镜像时也可以充分利用之前的缓存，达到加速效果。也可以把不易变的部分提取出来，做自己的基础镜像。见下节示例-4。

**以上只是优化的原则，如果这些操作影响到应用部署的复杂性，请酌情采纳**

1. 缩减层体积

2. 减少层数量

3. 使用公共基础镜像

4. Dockerfile 重排序

