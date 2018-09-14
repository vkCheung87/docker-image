> [jboss wildfly](https://hub.docker.com/r/jboss/wildfly/) 是一个docker版本的jboss，提供纯净的jboss功能。
> 
> 在实际的使用过程中，需要安装一些其他的module，其中比较常用的一个是配置JNDI。
> 
> 这个Dockerfile是用来创建一个standalone的jboss，并添加mysql的数据库驱动和配置相应的JNDI。

# Dependencies

使用的jboss wildfly 版本是[14.0.0.Final](https://github.com/jboss-dockerfiles/wildfly/releases/tag/14.0.0.Final)。
使用的MySQL数据库的链接依赖，版本为[5.2.31](https://repo1.maven.org/maven2/mysql/mysql-connector-java/5.1.31/)，从Maven中央仓库获取。可以修改Dockerfile中的参数 `MYSQL_DIRVER_VERSION` 来改变使用的版本信息。

# How to build

`docker build --build-arg DB_HOST=localhost --build-arg DB_PORT=4306 --build-arg DB_NAME=test --build-arg DB_USER=root --build-arg DB_PASS=root --tag=jboss/wildfly_mysql . `

Dockerfile 中设置了几个参数，来控制设置的JNDI的信息

| 字段名称    | 字段含义 |
| :--------------- | :----------------------------------- |
| DB_HOST  | 数据库的host                    |
| DB_PORT  | 数据库的端口                    |
| DB_NAME | 需要连接的数据库schema |
| DB_USER  | 数据库的登录用户名          |
| DB_PASS  | 数据库的登录密码              |

# How to start up

`docker run -p 8080:8080 -p 9990:9990 -d --name jboss jboss/wildfly_mysql:latest`

启动docker镜像，并把8080和9990端口映射到本机的8080和9990端口。

启动后，就可以通过http://localhost:9990/console/index.html 访问了。登录账户和密码都为admin（在Dockerfile，设置的）。

