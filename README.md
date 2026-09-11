# 快速开始

> 本仓库中每个 `lab-*` / `labx-*` 目录都是一个**可独立构建、独立启动**的 Maven 工程。下面以 [lab-23](./lab-23)（SpringMVC 入门）为例，给出可在本仓库内直接核验的「获取 → 安装 → 启动 → 调用」最小闭环；换成任意一个 `lab-*` 目录，步骤完全一致。

## 1. 环境要求

| 依赖 | 版本要求 | 校验命令 |
| --- | --- | --- |
| JDK | 8 及以上（Spring Boot 2.x） | `java -version` |
| Maven | 3.5 及以上 | `mvn -v` |
| MySQL、Redis、Nacos 等中间件 | 仅部分 lab 需要 | 见对应 `lab-xx/src/main/resources/application.yaml` |

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
ls ./pom.xml      # 根聚合 POM
ls -d ./lab-23    # 示例工程目录
```

## 3. 安装依赖与构建

```bash
cd lab-23                        # 进入任意一个 lab-* 工程
mvn clean package -DskipTests    # 下载依赖 + 编译打包，产物在 target/ 下
```

需要一次性聚合构建多个 lab 时，回到仓库根目录执行：

```bash
# 1）编辑 ./pom.xml，取消注释要用到的模块，例如：<module>lab-23</module>
# 2）在仓库根目录聚合构建
mvn clean install -DskipTests
```

## 4. 启动

```bash
# 在 lab-23 目录下，以下两种方式二选一
mvn spring-boot:run         # 方式一：直接运行
java -jar target/*.jar      # 方式二：运行第 3 步打出的 jar
```

启动成功的标志：控制台输出 `Started XxxApplication in x.xxx seconds`，并打印实际监听端口（默认 `8080`，以 `lab-23/src/main/resources/application.yaml` 中的 `server.port` 为准）。按 `Ctrl + C` 停止服务。

## 5. 调用与验证

```bash
# 将 /<path> 换成该 lab 控制器（controller 包下 @RequestMapping 声明的路径）
curl -i "http://127.0.0.1:8080/<path>"
```

需要现成的 HTTP 调用样例时，可直接使用 [lab-71-http-debug](./lab-71-http-debug) 目录下的 `.http` 脚本：用 IDEA 打开（IDEA HTTP Client），改掉端口后即可发送请求，等价于在浏览器或 Postman 中访问同一地址。

## 目录约定

| 路径 | 说明 |
| --- | --- |
| [./pom.xml](./pom.xml) | 根聚合 POM，默认注释了所有 `<module>`，按需放开 |
| `./lab-*` | 《Spring Boot 专栏》的实验工程，编号与下文教程一一对应，例如 [lab-23](./lab-23) |
| `./labx-*` | 《Spring Cloud 专栏》《Spring Cloud Alibaba 专栏》的实验工程，例如 [labx-01-spring-cloud-alibaba-nacos-discovery](./labx-01-spring-cloud-alibaba-nacos-discovery) |
| [lab-71-http-debug](./lab-71-http-debug) | IDEA HTTP Client 的接口调用示例 |

> 下文各栏目中教程条目后的「对应」链接均为**仓库内相对路径**（如 `./lab-23`），点击即可在本地打开源码核验；只有正文在 `iocoder.cn` 的教程需要联网阅读。

# Spring Boot 专栏

基于 Spring Boot 2.X 版本的**深度**入门教程。

市面上的 Spring Boot **基础**入门文章很多，但是**深度**入门文章却很少。对于很多开发者来说，入门即是其对某个技术栈的最终理解，一方面是开发者“比较懒”，另一方面是文章作者把 Spring Boot 入门写的太浅，又或者不够全面。

因此，艿艿开始了这个 Spring Boot 专栏，一个**深度**且**全面**的 Spring Boot 2.X 入门。
* 在带你快速学会 SpringMVC API 接口的编写的同时，我还想告诉你还有全局返回、全局异常、拦截器、跨域处理等等功能。
* 在带你快速学会 MQ 消息的发送与消费的同时，我还想告诉你 MQ 还有集群消费、广播消费、顺序消息、定时消息、事务消息、消费重试等等特性。
* 在带你快速学会 Job 任务的编写的同时，我还想告诉你还有 Quartz 单体、Quartz 集群、XXL-JOB 等等企业使用更多的调度平台。
