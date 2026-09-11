# 快速开始

> 本仓库中每个 `lab-*` / `labx-*` 目录都是一个**可独立构建、独立启动**的 Maven 工程。但其中部分 `lab-*` 是 POM **多模块聚合器**（如 [lab-23](./lab-23)），真正的 Spring Boot 应用位于它的子模块（如 `lab-23/lab-springmvc-23-01`）中；其余 `lab-*` 自身就是单个可运行模块。下面以 [lab-23](./lab-23)（SpringMVC 入门）为例，给出可在本仓库内直接核验的「获取 → 安装 → 启动 → 调用」最小闭环；**若遇到聚合器，请先 `cd` 进具体的子模块再构建/启动**，其余单模块 lab 步骤完全一致。

## 1. 环境要求

| 依赖 | 版本要求 | 校验命令 |
| --- | --- | --- |
| JDK | 8 及以上（Spring Boot 2.x） | `java -version` |
| Maven | 3.5 及以上 | `mvn -v` |
| MySQL、Redis、Nacos 等中间件 | 仅部分 lab 需要 | 见[「6. 配置说明」](#6-配置说明) |

## 2. 获取代码

```bash
# 方式一：克隆仓库
git clone https://github.com/YunaiV/SpringBoot-Labs.git
cd SpringBoot-Labs

# 方式二：使用本地已有仓库，直接进入仓库根目录
cd <仓库根目录>
```

进入根目录后，可以先确认后续命令依赖的文件都在：

```bash
ls ./pom.xml                           # 根聚合 POM
ls -d ./lab-23                        # 示例工程目录（聚合器）
ls -d ./lab-23/lab-springmvc-23-01    # 真正的 Spring Boot 子模块
```

## 3. 安装依赖与构建

```bash
cd lab-23                                 # 聚合器工程：构建会同时编译其全部子模块
mvn clean package -DskipTests             # 下载依赖 + 编译打包，子模块的 jar 在各自 target/ 下
```

需要一次性聚合构建多个 lab 时，回到仓库根目录执行：

```bash
# 1）编辑 ./pom.xml，取消注释要用到的模块，例如：<module>lab-23</module>
# 2）在仓库根目录聚合构建
mvn clean install -DskipTests
```

## 4. 启动

```bash
# 聚合器 lab-23 自身是 packaging=pom，不能直接运行；必须进入具体的子模块
cd lab-23/lab-springmvc-23-01          # 子模块一（也可改用 lab-springmvc-23-02）
mvn spring-boot:run                    # 方式一：直接运行该子模块
java -jar target/*.jar                 # 方式二：运行第 3 步在该子模块 target/ 下打出的 jar
```

启动成功的标志：控制台输出 `Started Application in x.xxx seconds`，并打印实际监听端口（默认 `8080`，以 `lab-23/lab-springmvc-23-01/src/main/resources/application.yaml` 中的 `server.port` 为准）。按 `Ctrl + C` 停止服务。

## 5. 调用与验证

```bash
# 将 /<path> 换成该 lab 控制器（controller 包下 @RequestMapping 声明的路径）
curl -i "http://127.0.0.1:8080/<path>"
```

需要现成的 HTTP 调用样例时，可直接使用 [lab-71-http-debug](./lab-71-http-debug) 目录下的 `.http` 脚本：用 IDEA 打开（IDEA HTTP Client），改掉端口后即可发送请求，等价于在浏览器或 Postman 中访问同一地址。

## 6. 配置说明

各 lab 都是独立工程，配置集中在自己的 `src/main/resources/` 目录，**改配置就是改这些文件**。配置优先级与 Spring Boot 一致：`application.yaml` 基础配置 → `application-{profile}.yaml` 覆盖 → 命令行参数 / 环境变量覆盖（越高优先级越高）。

### 6.1 找到某个 lab 的配置文件

```bash
ls -R lab-23/lab-springmvc-23-01/src/main/resources   # 聚合器：配置在子模块里，不在 lab-23 根目录
grep -rn "server.port" lab-23/lab-springmvc-23-01/src  # Linux/macOS：全局搜索某个配置项
findstr /s "server.port" lab-23\lab-springmvc-23-01\src\*  # Windows：等价搜索
```

常见文件与用途：

| 文件 | 是否业务配置 | 用途 |
| --- | --- | --- |
| `application.yaml` / `application.properties` | 是 | 端口、数据源、中间件地址等 |
| `application-{profile}.yaml`（如 `-dev`） | 是 | 按环境覆盖上述配置 |
| `*.sql` | 是（一次性） | 该 lab 的建表 / 初始化脚本，首次运行前需导入 |
| `logback-spring.xml` | 否 | 仅日志格式与级别 |

> 注意：聚合器 lab（如 `lab-23`）的配置文件在**子模块**目录内，例如 `lab-23/lab-springmvc-23-01/src/main/resources/`，而非 `lab-23` 根目录。单模块 lab 则在 `lab-xx/src/main/resources/`。

### 6.2 需要配置的项与参考值

| 场景（对应 lab） | 配置项 | 参考值 |
| --- | --- | --- |
| 所有 lab | `server.port` | `8080` |
| 关系数据库（[lab-12-mybatis](./lab-12-mybatis)、[lab-13-spring-data-jpa](./lab-13-spring-data-jpa)、[lab-14-spring-jdbc-template](./lab-14-spring-jdbc-template)、[lab-17](./lab-17)、[lab-18](./lab-18)、[lab-20](./lab-20)） | `spring.datasource.url` = `jdbc:mysql://127.0.0.1:3306/<库名>?useUnicode=true&characterEncoding=utf8&useSSL=false&serverTimezone=Asia/Shanghai`；`username` = `root`；`password` = `123456` |
| 连接池（[lab-19](./lab-19)） | `spring.datasource.druid.initial-size` / `max-active` | `5` / `20` |
| Redis / Cache（[lab-11-spring-data-redis](./lab-11-spring-data-redis)、[lab-21](./lab-21)） | `spring.redis.host` / `port` / `database` | `127.0.0.1` / `6379` / `0` |
| MongoDB / Elasticsearch（[lab-16-spring-data-mongo](./lab-16-spring-data-mongo)、[lab-15-spring-data-es](./lab-15-spring-data-es)） | `spring.data.mongodb.uri` / `spring.elasticsearch.rest.uris` | `mongodb://127.0.0.1:27017/<库名>` / `127.0.0.1:9200` |
| 注册 / 配置中心（[lab-44](./lab-44)、[lab-45](./lab-45)、[labx-01-spring-cloud-alibaba-nacos-discovery](./labx-01-spring-cloud-alibaba-nacos-discovery)、[labx-05-spring-cloud-alibaba-nacos-config](./labx-05-spring-cloud-alibaba-nacos-config)、[labx-09-spring-cloud-apollo](./labx-09-spring-cloud-apollo)） | `spring.cloud.nacos.discovery.server-addr` / `config.server-addr` / `apollo.meta` | `127.0.0.1:8848` / `http://127.0.0.1:8080` |
| 消息队列（[lab-03-kafka](./lab-03-kafka)、[lab-04-rabbitmq](./lab-04-rabbitmq)、[lab-31](./lab-31)、[lab-32](./lab-32)） | `rocketmq.name-server` / `spring.kafka.bootstrap-servers` / `spring.rabbitmq.host` | `127.0.0.1:9876` / `127.0.0.1:9092` / `127.0.0.1` |
| 服务容错（[lab-46](./lab-46)、[lab-57](./lab-57)、[lab-59](./lab-59)、[labx-04-spring-cloud-alibaba-sentinel](./labx-04-spring-cloud-alibaba-sentinel)） | `spring.cloud.sentinel.transport.dashboard` 等 | `127.0.0.1:8080` |
| 分布式事务（[lab-52](./lab-52)、[lab-53](./lab-53)、[labx-17](./labx-17)） | `seata.registry.type` / `seata.service.vgroup-mapping.*` / `seata.service.grouplist.default` | `nacos` / `default: default` / `127.0.0.1:8091` |
| 安全（[lab-01-spring-security](./lab-01-spring-security)、[lab-33](./lab-33)、[lab-68-spring-security-oauth](./lab-68-spring-security-oauth)） | `spring.security.user.name` / `password` | `admin` / `admin` |

> 上表为**参考值**。最终以该 lab 现有 `application.yaml` 中的键名为准：键已存在就直接改值，键不存在再按上表补充；库名 / 密码等按本机环境填写。

### 6.3 示例一：改端口并验证（无需任何中间件）

```yaml
# lab-23/lab-springmvc-23-01/src/main/resources/application.yaml
server:
  port: 8081
```

```bash
cd lab-23/lab-springmvc-23-01 && mvn spring-boot:run
# 日志出现 Tomcat started on port(s): 8081 (http) 即配置已生效
curl -i "http://127.0.0.1:8081/<path>"
```

### 6.4 示例二：接入本地 MySQL（以 [lab-13-spring-data-jpa](./lab-13-spring-data-jpa) 为例）

```yaml
# lab-13-spring-data-jpa/src/main/resources/application.yaml
spring:
  datasource:
    url: jdbc:mysql://127.0.0.1:3306/lab_13?useUnicode=true&characterEncoding=utf8&useSSL=false&serverTimezone=Asia/Shanghai
    username: root
    password: 123456
    driver-class-name: com.mysql.cj.jdbc.Driver
```

步骤：① 起一个 MySQL；② 建库 `lab_13`（库名按该 lab 的 `*.sql` 脚本或实际环境填写）；③ 导入该 lab 目录内的初始化 SQL；④ 按上面键值改配置；⑤ 启动并调用接口。可用 Docker 快速起中间件：

```bash
docker run -d --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 -e TZ=Asia/Shanghai mysql:8.0
docker run -d --name redis -p 6379:6379 redis:6.2
docker run -d --name nacos -p 8848:8848 -e MODE=standalone nacos/nacos-server:2.0.3
```

### 6.5 示例三：多环境切换与临时覆盖

```bash
# 以下命令均在该子模块目录内执行（如 lab-23/lab-springmvc-23-01）
# 1）激活 application-dev.yaml
mvn spring-boot:run -Dspring-boot.run.profiles=dev
java -jar target/*.jar --spring.profiles.active=dev

# 2）命令行覆盖单个配置项（优先级最高，适合临时指向另一台中间件）
java -jar target/*.jar --server.port=9090 --spring.datasource.url=jdbc:mysql://192.168.1.10:3306/lab_13

# 3）环境变量覆盖（Linux/macOS）
SPRING_APPLICATION_JSON='{"server":{"port":9090}}' java -jar target/*.jar
# 3）环境变量覆盖（Windows PowerShell 等价写法）
$env:SPRING_APPLICATION_JSON='{"server":{"port":9090}}'; java -jar target/*.jar
```

### 6.6 配置是否生效怎么核验

```bash
# 启动日志会打印生效 profile 与端口：
#   The following profiles are active: dev
#   Tomcat started on port(s): 8081 (http)

# 需要查看全部生效配置时，用 [lab-34](./lab-34)（Actuator）端点
curl -s http://127.0.0.1:8080/actuator/env | head -n 20
curl -s http://127.0.0.1:8080/actuator/configprops | head -n 20
```

> 中间件未就绪时：只有用到该中间件的 lab 才会启动失败。可把地址改指向已有实例，或按 6.4 起一个本地容器；与中间件无关的 lab（如 `lab-23/lab-springmvc-23-01`、[lab-47](./lab-47)）无需任何中间件，仅改 `server.port` 即可运行。

## 目录约定

| 路径 | 说明 |
| --- | --- |
| [./pom.xml](./pom.xml) | 根聚合 POM，默认注释了所有 `<module>`，按需放开 |
| `./lab-*/src/main/resources/` | 各 lab 的配置文件目录，包含 `application.yaml`、`application-{profile}.yaml`、初始化 `*.sql`；聚合器 lab 的路径为 `./lab-xx/<子模块>/src/main/resources/` |
| `./lab-*` | 《Spring Boot 专栏》的实验工程，编号与下文教程一一对应，例如 [lab-23](./lab-23) |
| `./labx-*` | 《Spring Cloud 专栏》《Spring Cloud Alibaba 专栏》的实验工程，例如 [labx-01-spring-cloud-alibaba-nacos-discovery](./labx-01-spring-cloud-alibaba-nacos-discovery) |
| [lab-71-http-debug](./lab-71-http-debug) | IDEA HTTP Client 的接口调用示例 |

> 下文各栏目中教程条目后的「对应」链接均为**仓库内相对路径**（如 `./lab-23`），点击即可在本地打开源码核验；只有正文在 `iocoder.cn` 的教程需要联网阅读。

# Spring Boot 专栏

基于 Spring Boot 2.X 版本的**深度**入门教程。

市面上的 Spring Boot **基础**入门文章很多，但是**深度**入门文章却很少。对于很多开发者来说，入门即是其对某个技术栈的最终理解，一方面是开发者“比较懒”，另一方面是文章作者把 Spring Boot 入门写的太浅，又或者不够全面。

因此，我开始了这个 Spring Boot 专栏，一个**深度**且**全面**的 Spring Boot 2.X 入门。
* 在带你快速学会 SpringMVC API 接口的编写的同时，我还想告诉你还有全局返回、全局异常、拦截器、跨域处理等等功能。
* 在带你快速学会 MQ 消息的发送与消费的同时，我还想告诉你 MQ 还有集群消费、广播消费、顺序消息、定时消息、事务消息、消费重试等等特性。
* 在带你快速学会 Job 任务的编写的同时，我还想告诉你还有 Quartz 单体、Quartz 集群、XXL-JOB 等等企业使用更多的调度平台。
