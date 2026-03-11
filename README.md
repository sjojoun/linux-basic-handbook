# Maven 完全教程 - 从入门到精通

> 📚 一本全面的 Maven 学习指南，涵盖从基础概念到高级实践的所有内容

![Maven](https://img.shields.io/badge/Maven-3.9+-C71A36?style=for-the-badge&logo=apachemaven&logoColor=white)
![Java](https://img.shields.io/badge/Java-17+-007396?style=for-the-badge&logo=openjdk&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)

---

## 📑 目录

- [Maven 完全教程 - 从入门到精通](#maven-完全教程---从入门到精通)
  - [📑 目录](#-目录)
  - [📖 第一部分：Maven 基础](#-第一部分maven-基础)
    - [第一章：Maven 简介](#第一章maven-简介)
      - [1.1 什么是 Maven？](#11-什么是-maven)
      - [1.2 Maven 的历史](#12-maven-的历史)
      - [1.3 为什么使用 Maven？](#13-为什么使用-maven)
      - [1.4 Maven vs 其他构建工具](#14-maven-vs-其他构建工具)
    - [第二章：Maven 核心概念](#第二章maven-核心概念)
      - [2.1 POM (Project Object Model)](#21-pom-project-object-model)
      - [2.2 Maven 坐标](#22-maven-坐标)
      - [2.3 仓库体系](#23-仓库体系)
      - [2.4 依赖范围](#24-依赖范围)
      - [2.5 传递依赖](#25-传递依赖)
    - [第三章：安装与配置](#第三章安装与配置)
      - [3.1 Windows 安装](#31-windows-安装)
      - [3.2 macOS 安装](#32-macos-安装)
      - [3.3 Linux 安装](#33-linux-安装)
      - [3.4 配置阿里云镜像](#34-配置阿里云镜像)
      - [3.5 验证安装](#35-验证安装)
  - [🏗️ 第二部分：项目构建](#️-第二部分项目构建)
    - [第四章：项目结构](#第四章项目结构)
      - [4.1 标准目录结构](#41-标准目录结构)
      - [4.2 创建新项目](#42-创建新项目)
      - [4.3 导入现有项目](#43-导入现有项目)
    - [第五章：POM 文件详解](#第五章pom-文件详解)
      - [5.1 POM 基础结构](#51-pom-基础结构)
      - [5.2 项目信息配置](#52-项目信息配置)
      - [5.3 构建配置](#53-构建配置)
      - [5.4 完整 POM 示例](#54-完整-pom-示例)
    - [第六章：依赖管理](#第六章依赖管理)
      - [6.1 添加依赖](#61-添加依赖)
      - [6.2 依赖版本管理](#62-依赖版本管理)
      - [6.3 依赖排除](#63-依赖排除)
      - [6.4 可选依赖](#64-可选依赖)
      - [6.5 查看依赖树](#65-查看依赖树)
  - [⚙️ 第三部分：高级用法](#️-第三部分高级用法)
    - [第七章：Maven 生命周期](#第七章maven-生命周期)
      - [7.1 三大生命周期](#71-三大生命周期)
      - [7.2 默认生命周期详解](#72-默认生命周期详解)
      - [7.3 生命周期阶段绑定](#73-生命周期阶段绑定)
    - [第八章：插件系统](#第八章插件系统)
      - [8.1 插件配置基础](#81-插件配置基础)
      - [8.2 常用核心插件](#82-常用核心插件)
      - [8.3 自定义插件配置](#83-自定义插件配置)
      - [8.4 插件执行时机](#84-插件执行时机)
    - [第九章：多模块项目](#第九章多模块项目)
      - [9.1 多模块项目结构](#91-多模块项目结构)
      - [9.2 父 POM 配置](#92-父-pom-配置)
      - [9.3 模块间依赖](#93-模块间依赖)
      - [9.4 反应堆构建](#94-反应堆构建)
      - [9.5 模块构建顺序](#95-模块构建顺序)
    - [第十章：Profile 与环境管理](#第十章profile-与环境管理)
      - [10.1 Profile 基础](#101-profile-基础)
      - [10.2 Profile 激活方式](#102-profile-激活方式)
      - [10.3 多环境配置实战](#103-多环境配置实战)
  - [🚀 第四部分：实战与最佳实践](#-第四部分实战与最佳实践)
    - [第十一章：常用命令速查](#第十一章常用命令速查)
      - [11.1 构建命令](#111-构建命令)
      - [11.2 依赖命令](#112-依赖命令)
      - [11.3 插件命令](#113-插件命令)
      - [11.4 实用命令组合](#114-实用命令组合)
    - [第十二章：最佳实践](#第十二章最佳实践)
      - [12.1 版本管理策略](#121-版本管理策略)
      - [12.2 依赖管理最佳实践](#122-依赖管理最佳实践)
      - [12.3 构建优化技巧](#123-构建优化技巧)
      - [12.4 CI/CD 集成](#124-cicd-集成)
    - [第十三章：常见问题解决](#第十三章常见问题解决)
      - [13.1 依赖下载失败](#131-依赖下载失败)
      - [13.2 内存不足问题](#132-内存不足问题)
      - [13.3 构建速度慢](#133-构建速度慢)
      - [13.4 版本冲突解决](#134-版本冲突解决)
      - [13.5 插件问题](#135-插件问题)
  - [📎 附录](#-附录)
    - [附录 A：Maven 常用仓库](#附录-amaven-常用仓库)
    - [附录 B：常用依赖坐标](#附录 b-常用依赖坐标)
    - [附录 C：Maven 插件大全](#附录-cmaven-插件大全)
    - [附录 D：学习资源](#附录 d-学习资源)

---

## 📖 第一部分：Maven 基础

### 第一章：Maven 简介

#### 1.1 什么是 Maven？

**Maven** 是一个 Apache 基金会维护的**项目管理工具**，主要用于 Java 项目的构建、依赖管理和项目信息管理。

Maven 的核心思想是**约定优于配置**（Convention over Configuration），通过提供标准的项目结构和构建流程，减少开发者的配置工作。

**Maven 的三大核心功能：**

1. **依赖管理**：自动下载和管理项目所需的第三方库
2. **项目构建**：统一的编译、测试、打包流程
3. **信息管理**：集中管理项目元数据（版本、开发者、许可证等）

#### 1.2 Maven 的历史

- **2002 年**：Maven 最初由 Jason van Zyl 创建，目的是简化 Jakarta Turbine 项目的构建过程
- **2004 年**：Maven 1.0 发布
- **2005 年**：Maven 2.0 发布，引入了 POM 2.0 和插件系统
- **2010 年**：Maven 3.0 发布，改进了性能和可靠性
- **2023 年**：Maven 3.9.x 成为稳定版本

#### 1.3 为什么使用 Maven？

**传统构建方式的问题：**

```
❌ 手动管理 JAR 文件
❌ 依赖冲突难以解决
❌ 构建过程不统一
❌ 项目结构混乱
❌ 团队协作困难
```

**Maven 带来的优势：**

```
✅ 自动依赖下载和管理
✅ 解决依赖冲突
✅ 标准化构建流程
✅ 统一项目结构
✅ 易于团队协作
✅ 丰富的插件生态
✅ 与 CI/CD 无缝集成
```

#### 1.4 Maven vs 其他构建工具

| 特性 | Maven | Gradle | Ant |
|------|-------|--------|-----|
| **配置文件** | XML (pom.xml) | Groovy/Kotlin DSL | XML (build.xml) |
| **学习曲线** | ⭐⭐ 较低 | ⭐⭐⭐ 中等 | ⭐⭐ 较低 |
| **构建速度** | ⭐⭐ 较慢 | ⭐⭐⭐⭐ 快 | ⭐⭐ 较慢 |
| **灵活性** | ⭐⭐ 中等 | ⭐⭐⭐⭐⭐ 高 | ⭐⭐⭐ 中等 |
| **约定优于配置** | ✅ 强 | ✅ 中等 | ❌ 无 |
| **社区支持** | ✅ 成熟稳定 | ✅ 快速增长 | ⚠️ 逐渐减少 |
| **适用场景** | 标准 Java 项目 | 复杂/多语言项目 | 遗留项目 |

**选择建议：**

- **选择 Maven**：标准 Java 项目、企业级应用、需要稳定性和成熟生态
- **选择 Gradle**：Android 项目、多语言项目、需要高度定制化
- **选择 Ant**：维护遗留项目、特殊构建需求

---

### 第二章：Maven 核心概念

#### 2.1 POM (Project Object Model)

POM 是 Maven 的核心，是一个 XML 文件 (`pom.xml`)，包含项目的所有配置信息。

**POM 的基本结构：**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
         http://maven.apache.org/xsd/maven-4.0.0.xsd">
  
  <modelVersion>4.0.0</modelVersion>
  
  <!-- 项目坐标 -->
  <groupId>com.example</groupId>
  <artifactId>my-project</artifactId>
  <version>1.0.0</version>
  
</project>
```

#### 2.2 Maven 坐标

每个 Maven 构件由以下坐标唯一标识：

```xml
<groupId>com.example</groupId>
<artifactId>my-project</artifactId>
<version>1.0.0</version>
<packaging>jar</packaging>
```

**坐标详解：**

| 元素 | 说明 | 示例 | 命名规范 |
|------|------|------|----------|
| **groupId** | 组织或项目的唯一标识 | `com.example` `org.springframework` | 反向域名 |
| **artifactId** | 项目名称 | `my-project` `spring-core` | 小写，短横线分隔 |
| **version** | 版本号 | `1.0.0` `2.1.0-SNAPSHOT` | 语义化版本 |
| **packaging** | 打包方式 | `jar` `war` `pom` | 默认 jar |

**版本命名规范：**

```
主版本。次版本。修订版本 [-里程碑/快照]

示例：
1.0.0           # 正式版本
1.0.0-RC1       # 候选版本 1
1.0.0-BETA      # 测试版本
1.0.0-SNAPSHOT  # 开发快照版本
```

#### 2.3 仓库体系

Maven 的仓库分为三类：

```
┌─────────────────────────────────────────────────────────┐
│                    Maven 仓库体系                        │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  ┌─────────────┐      ┌─────────────┐                  │
│  │  本地仓库   │ ←──→ │  远程仓库   │                  │
│  │  (Local)    │      │  (Remote)   │                  │
│  │  ~/.m2/     │      │  Central    │                  │
│  │  repository │      │  阿里云等   │                  │
│  └─────────────┘      └─────────────┘                  │
│         ↑                    ↑                          │
│         │                    │                          │
│         └──────────┬─────────┘                          │
│                    ↓                                    │
│           ┌─────────────┐                               │
│           │  镜像仓库   │                               │
│           │  (Mirror)   │                               │
│           │  阿里云镜像 │                               │
│           └─────────────┘                               │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

**1. 本地仓库 (Local Repository)**

- 位置：`~/.m2/repository` (Windows: `C:\Users\用户名\.m2\repository`)
- 作用：缓存从远程仓库下载的依赖
- 管理：无需手动管理，Maven 自动维护

**2. 远程仓库 (Remote Repository)**

- **Maven Central**：官方中央仓库 (https://repo.maven.apache.org/maven2/)
- **JCenter**：Bintray 的仓库 (已停止服务)
- **Google Maven**：Android 相关依赖
- **私有仓库**：Nexus、Artifactory 等

**3. 镜像仓库 (Mirror)**

用于加速下载，配置在 `settings.xml` 中：

```xml
<mirrors>
  <mirror>
    <id>aliyun</id>
    <mirrorOf>central</mirrorOf>
    <name>Aliyun Maven Mirror</name>
    <url>https://maven.aliyun.com/repository/central</url>
  </mirror>
</mirrors>
```

#### 2.4 依赖范围

依赖范围 (Scope) 控制依赖在哪些阶段可用：

| Scope | 编译 | 测试 | 运行 | 说明 | 典型用途 |
|-------|------|------|------|------|----------|
| **compile** | ✅ | ✅ | ✅ | 默认，所有阶段可用 | Spring、工具库 |
| **provided** | ✅ | ✅ | ❌ | 由运行环境提供 | Servlet API、Lombok |
| **runtime** | ❌ | ✅ | ✅ | 仅测试和运行时需要 | JDBC 驱动 |
| **test** | ❌ | ✅ | ❌ | 仅测试时需要 | JUnit、Mockito |
| **system** | ✅ | ✅ | ❌ | 本地系统路径 | 本地 JAR |
| **import** | - | - | - | 仅用于 dependencyManagement | BOM 导入 |

**使用示例：**

```xml
<dependencies>
  <!-- compile: 默认范围，始终可用 -->
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter</artifactId>
    <version>3.0.0</version>
  </dependency>
  
  <!-- provided: 容器会提供，不需要打包 -->
  <dependency>
    <groupId>jakarta.servlet</groupId>
    <artifactId>jakarta.servlet-api</artifactId>
    <version>5.0.0</version>
    <scope>provided</scope>
  </dependency>
  
  <!-- runtime: 运行时才需要 -->
  <dependency>
    <groupId>com.mysql</groupId>
    <artifactId>mysql-connector-j</artifactId>
    <version>8.0.33</version>
    <scope>runtime</scope>
  </dependency>
  
  <!-- test: 仅测试使用 -->
  <dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter</artifactId>
    <version>5.9.2</version>
    <scope>test</scope>
  </dependency>
</dependencies>
```

#### 2.5 传递依赖

Maven 会自动解析依赖的依赖，这就是**传递依赖**。

```
你的项目
    └── spring-boot-starter (A)
            └── spring-core (B)
                    └── spring-jcl (C)
```

在这个例子中：
- A 是你的直接依赖
- B 和 C 是传递依赖

**查看传递依赖：**

```bash
# 查看完整依赖树
mvn dependency:tree

# 查看特定依赖的传递依赖
mvn dependency:tree -Dincludes=org.springframework:*

# 详细输出，显示冲突
mvn dependency:tree -Dverbose
```

**排除传递依赖：**

```xml
<dependency>
  <groupId>com.example</groupId>
  <artifactId>some-lib</artifactId>
  <version>1.0.0</version>
  <exclusions>
    <exclusion>
      <groupId>conflicting-group</groupId>
      <artifactId>conflicting-artifact</artifactId>
    </exclusion>
  </exclusions>
</dependency>
```

---

### 第三章：安装与配置

#### 3.1 Windows 安装

**步骤 1：下载 Maven**

访问官网下载：https://maven.apache.org/download.cgi

下载 `apache-maven-3.9.x-bin.zip`

**步骤 2：解压安装**

```
解压到目录，例如：C:\Program Files\Apache\maven
```

**步骤 3：配置环境变量**

1. 右键"此电脑" → "属性" → "高级系统设置"
2. 点击"环境变量"
3. 新建系统变量：
   - 变量名：`MAVEN_HOME`
   - 变量值：`C:\Program Files\Apache\maven`
4. 编辑 `Path` 变量，添加：`%MAVEN_HOME%\bin`

**步骤 4：验证安装**

打开命令提示符：

```cmd
mvn -v
```

预期输出：

```
Apache Maven 3.9.6 (bc0240f3c744dd6b6ec2920b3cd08dcc295161ae)
Maven home: C:\Program Files\Apache\maven
Java version: 17.0.1, vendor: Oracle Corporation
Default locale: zh_CN, platform encoding: UTF-8
OS name: "windows 10", version: "10.0", arch: "amd64", family: "windows"
```

#### 3.2 macOS 安装

**方式一：Homebrew (推荐)**

```bash
# 安装 Maven
brew install maven

# 验证安装
mvn -v

# 更新 Maven
brew upgrade maven
```

**方式二：手动安装**

```bash
# 下载并解压
cd /opt
wget https://dlcdn.apache.org/maven/maven-3/3.9.6/binaries/apache-maven-3.9.6-bin.tar.gz
tar -xzf apache-maven-3.9.6-bin.tar.gz

# 配置环境变量 (添加到 ~/.zshrc 或 ~/.bash_profile)
export MAVEN_HOME=/opt/apache-maven-3.9.6
export PATH=$MAVEN_HOME/bin:$PATH

# 生效配置
source ~/.zshrc
```

#### 3.3 Linux 安装

**Ubuntu/Debian：**

```bash
# 使用 apt 安装
sudo apt update
sudo apt install maven

# 验证安装
mvn -v
```

**CentOS/RHEL：**

```bash
# 使用 yum 安装
sudo yum install maven

# 或使用 dnf
sudo dnf install maven

# 验证安装
mvn -v
```

**手动安装 (所有 Linux 发行版)：**

```bash
# 下载
cd /opt
sudo wget https://dlcdn.apache.org/maven/maven-3/3.9.6/binaries/apache-maven-3.9.6-bin.tar.gz

# 解压
sudo tar -xzf apache-maven-3.9.6-bin.tar.gz

# 配置环境变量
echo 'export MAVEN_HOME=/opt/apache-maven-3.9.6' | sudo tee -a /etc/profile.d/maven.sh
echo 'export PATH=$MAVEN_HOME/bin:$PATH' | sudo tee -a /etc/profile.d/maven.sh

# 生效
source /etc/profile.d/maven.sh
```

#### 3.4 配置阿里云镜像

**编辑 settings.xml：**

位置：`~/.m2/settings.xml` (如果不存在则创建)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 
          http://maven.apache.org/xsd/settings-1.0.0.xsd">
  
  <!-- 本地仓库配置 (可选) -->
  <localRepository>D:\maven-repository</localRepository>
  
  <!-- 镜像配置 -->
  <mirrors>
    <mirror>
      <id>aliyun</id>
      <mirrorOf>central</mirrorOf>
      <name>Aliyun Maven Mirror</name>
      <url>https://maven.aliyun.com/repository/central</url>
    </mirror>
    
    <!-- 可选：配置其他镜像 -->
    <mirror>
      <id>huaweicloud</id>
      <mirrorOf>central</mirrorOf>
      <name>Huawei Cloud Maven Mirror</name>
      <url>https://repo.huaweicloud.com/repository/maven/</url>
    </mirror>
  </mirrors>
  
  <!-- 服务器认证配置 (部署到私有仓库时使用) -->
  <servers>
    <server>
      <id>nexus-releases</id>
      <username>admin</username>
      <password>your-password</password>
    </server>
  </servers>
  
  <!-- 仓库配置 -->
  <profiles>
    <profile>
      <id>jdk-17</id>
      <activation>
        <activeByDefault>true</activeByDefault>
        <jdk>17</jdk>
      </activation>
      <properties>
        <maven.compiler.source>17</maven.compiler.source>
        <maven.compiler.target>17</maven.compiler.target>
      </properties>
    </profile>
  </profiles>
  
</settings>
```

**阿里云镜像地址：**

| 仓库 | 镜像地址 |
|------|----------|
| central | https://maven.aliyun.com/repository/central |
| google | https://maven.aliyun.com/repository/google |
| gradle | https://maven.aliyun.com/repository/gradle-plugin |
| spring | https://maven.aliyun.com/repository/spring |
| apache | https://maven.aliyun.com/repository/apache-snapshots |

#### 3.5 验证安装

```bash
# 查看 Maven 版本
mvn -v

# 查看 Maven 配置信息
mvn help:system

# 查看本地仓库位置
mvn help:effective-settings | grep localRepository

# 创建测试项目
mvn archetype:generate -DgroupId=test -DartifactId=test-project -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

---

## 🏗️ 第二部分：项目构建

### 第四章：项目结构

#### 4.1 标准目录结构

Maven 遵循**约定优于配置**的原则，定义了标准的项目结构：

```
my-project/
├── pom.xml                    # Maven 配置文件
├── src/
│   ├── main/
│   │   ├── java/              # 主源代码
│   │   │   └── com/
│   │   │       └── example/
│   │   │           └── App.java
│   │   ├── resources/         # 主资源文件
│   │   │   ├── application.properties
│   │   │   └── config/
│   │   └── webapp/            # Web 应用目录 (WAR 项目)
│   │       ├── WEB-INF/
│   │       └── index.html
│   └── test/
│       ├── java/              # 测试源代码
│       │   └── com/
│       │       └── example/
│       │           └── AppTest.java
│       └── resources/         # 测试资源文件
│           └── test.properties
├── target/                    # 构建输出目录
│   ├── classes/               # 编译后的类文件
│   ├── test-classes/          # 测试类文件
│   ├── my-project-1.0.0.jar   # 打包产物
│   └── maven-archiver/        # 构建信息
├── .mvn/                      # Maven 配置目录 (可选)
│   └── wrapper/               # Maven Wrapper
└── .gitignore                 # Git 忽略文件
```

**目录说明：**

| 目录 | 说明 |
|------|------|
| `src/main/java` | 存放主程序的 Java 源代码 |
| `src/main/resources` | 存放主程序使用的资源文件（配置文件、模板等） |
| `src/main/webapp` | Web 应用目录，仅 WAR 项目需要 |
| `src/test/java` | 存放测试代码 |
| `src/test/resources` | 存放测试使用的资源文件 |
| `target` | 构建输出目录，由 Maven 自动生成，不应手动修改 |

#### 4.2 创建新项目

**方式一：使用命令行**

```bash
# 创建简单 Java 项目
mvn archetype:generate \
  -DgroupId=com.example \
  -DartifactId=my-app \
  -DarchetypeArtifactId=maven-archetype-quickstart \
  -DarchetypeVersion=1.4 \
  -DinteractiveMode=false

# 创建 Web 项目
mvn archetype:generate \
  -DgroupId=com.example \
  -DartifactId=my-webapp \
  -DarchetypeArtifactId=maven-archetype-webapp \
  -DarchetypeVersion=1.4 \
  -DinteractiveMode=false

# 创建 Spring Boot 项目
mvn archetype:generate \
  -DgroupId=com.example \
  -DartifactId=my-spring-boot-app \
  -DarchetypeGroupId=org.springframework.boot \
  -DarchetypeArtifactId=spring-boot-starter-archetype \
  -DinteractiveMode=false
```

**方式二：交互式创建**

```bash
mvn archetype:generate
```

然后按照提示选择：

1. 选择 archetype（项目模板）
2. 输入 groupId
3. 输入 artifactId
4. 输入 version（默认 1.0-SNAPSHOT）
5. 输入 package
6. 确认配置

**方式三：使用 IDE**

- **IntelliJ IDEA**：File → New → Project → Maven → 选择 archetype
- **Eclipse**：File → New → Maven Project → 选择 archetype
- **VS Code**：使用 Maven for Java 扩展

#### 4.3 导入现有项目

**导入非 Maven 项目：**

```bash
# 在项目根目录初始化 Maven
mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

# 手动创建 pom.xml
```

**pom.xml 基础模板：**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
         http://maven.apache.org/xsd/maven-4.0.0.xsd">
  
  <modelVersion>4.0.0</modelVersion>
  
  <groupId>com.example</groupId>
  <artifactId>existing-project</artifactId>
  <version>1.0.0</version>
  <packaging>jar</packaging>
  
  <properties>
    <maven.compiler.source>17</maven.compiler.source>
    <maven.compiler.target>17</maven.compiler.target>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  </properties>
  
</project>
```

---

### 第五章：POM 文件详解

#### 5.1 POM 基础结构

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
         http://maven.apache.org/xsd/maven-4.0.0.xsd">
  
  <!-- 必需的元素 -->
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.example</groupId>
  <artifactId>my-project</artifactId>
  <version>1.0.0</version>
  
  <!-- 可选但推荐的元素 -->
  <packaging>jar</packaging>
  <name>My Project</name>
  <description>项目描述</description>
  
</project>
```

#### 5.2 项目信息配置

```xml
<project>
  <!-- 基本信息 -->
  <groupId>com.example</groupId>
  <artifactId>my-application</artifactId>
  <version>1.0.0-SNAPSHOT</version>
  <packaging>jar</packaging>
  
  <!-- 项目元数据 -->
  <name>My Application</name>
  <description>这是一个示例 Maven 项目，用于演示 POM 配置</description>
  <url>https://github.com/example/my-application</url>
  
  <!-- 许可证 -->
  <licenses>
    <license>
      <name>MIT License</name>
      <url>https://opensource.org/licenses/MIT</url>
      <distribution>repo</distribution>
    </license>
  </licenses>
  
  <!-- 开发者信息 -->
  <developers>
    <developer>
      <id>dev1</id>
      <name>张三</name>
      <email>zhangsan@example.com</email>
      <organization>Example Inc.</organization>
      <roles>
        <role>developer</role>
        <role>maintainer</role>
      </roles>
      <timezone>Asia/Shanghai</timezone>
    </developer>
  </developers>
  
  <!-- SCM (源代码管理) -->
  <scm>
    <connection>scm:git:git@github.com:example/my-application.git</connection>
    <developerConnection>scm:git:git@github.com:example/my-application.git</developerConnection>
    <url>https://github.com/example/my-application</url>
    <tag>HEAD</tag>
  </scm>
  
  <!-- 问题追踪 -->
  <issueManagement>
    <system>GitHub Issues</system>
    <url>https://github.com/example/my-application/issues</url>
  </issueManagement>
  
  <!-- CI/CD -->
  <ciManagement>
    <system>GitHub Actions</system>
    <url>https://github.com/example/my-application/actions</url>
  </ciManagement>
</project>
```

#### 5.3 构建配置

```xml
<project>
  <build>
    <!-- 最终构建名称 -->
    <finalName>my-app</finalName>
    
    <!-- 源代码目录 (默认 src/main/java) -->
    <sourceDirectory>src/main/java</sourceDirectory>
    
    <!-- 测试代码目录 (默认 src/test/java) -->
    <testSourceDirectory>src/test/java</testSourceDirectory>
    
    <!-- 资源目录配置 -->
    <resources>
      <resource>
        <directory>src/main/resources</directory>
        <includes>
          <include>**/*.properties</include>
          <include>**/*.xml</include>
          <include>**/*.yml</include>
        </includes>
        <excludes>
          <exclude>**/secret.properties</exclude>
        </excludes>
        <filtering>true</filtering>
      </resource>
    </resources>
    
    <!-- 测试资源目录配置 -->
    <testResources>
      <testResource>
        <directory>src/test/resources</directory>
      </testResource>
    </testResources>
    
    <!-- 输出目录 -->
    <outputDirectory>target/classes</outputDirectory>
    <testOutputDirectory>target/test-classes</testOutputDirectory>
    
    <!-- 插件配置 -->
    <plugins>
      <!-- 编译插件 -->
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.11.0</version>
        <configuration>
          <source>17</source>
          <target>17</target>
          <encoding>UTF-8</encoding>
          <showWarnings>true</showWarnings>
          <showDeprecation>true</showDeprecation>
        </configuration>
      </plugin>
      
      <!-- 打包插件 -->
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-jar-plugin</artifactId>
        <version>3.3.0</version>
        <configuration>
          <archive>
            <manifest>
              <mainClass>com.example.Main</mainClass>
              <addClasspath>true</addClasspath>
              <classpathPrefix>lib/</classpathPrefix>
            </manifest>
          </archive>
        </configuration>
      </plugin>
      
      <!-- 测试插件 -->
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-surefire-plugin</artifactId>
        <version>3.1.2</version>
        <configuration>
          <includes>
            <include>**/*Test.java</include>
          </includes>
          <excludes>
            <exclude>**/Abstract*.java</exclude>
          </excludes>
        </configuration>
      </plugin>
    </plugins>
    
    <!-- 插件管理 (统一版本) -->
    <pluginManagement>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.11.0</version>
        </plugin>
      </plugins>
    </pluginManagement>
  </build>
</project>
```

#### 5.4 完整 POM 示例

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
         http://maven.apache.org/xsd/maven-4.0.0.xsd">
  
  <modelVersion>4.0.0</modelVersion>
  
  <!-- 项目坐标 -->
  <groupId>com.example</groupId>
  <artifactId>complete-demo</artifactId>
  <version>1.0.0-SNAPSHOT</version>
  <packaging>jar</packaging>
  
  <!-- 项目信息 -->
  <name>Complete Maven Demo</name>
  <description>完整的 Maven 项目示例</description>
  <url>https://github.com/example/complete-demo</url>
  
  <!-- 属性配置 -->
  <properties>
    <java.version>17</java.version>
    <maven.compiler.source>${java.version}</maven.compiler.source>
    <maven.compiler.target>${java.version}</maven.compiler.target>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    
    <!-- 依赖版本 -->
    <spring-boot.version>3.0.0</spring-boot.version>
    <mysql.version>8.0.33</mysql.version>
    <lombok.version>1.18.28</lombok.version>
    <junit.version>5.9.2</junit.version>
  </properties>
  
  <!-- 依赖管理 -->
  <dependencies>
    <!-- Spring Boot -->
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
      <version>${spring-boot.version}</version>
    </dependency>
    
    <!-- MySQL -->
    <dependency>
      <groupId>com.mysql</groupId>
      <artifactId>mysql-connector-j</artifactId>
      <version>${mysql.version}</version>
      <scope>runtime</scope>
    </dependency>
    
    <!-- Lombok -->
    <dependency>
      <groupId>org.projectlombok</groupId>
      <artifactId>lombok</artifactId>
      <version>${lombok.version}</version>
      <scope>provided</scope>
    </dependency>
    
    <!-- 测试 -->
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-test</artifactId>
      <version>${spring-boot.version}</version>
      <scope>test</scope>
    </dependency>
    
    <dependency>
      <groupId>org.junit.jupiter</groupId>
      <artifactId>junit-jupiter</artifactId>
      <version>${junit.version}</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
  
  <!-- 构建配置 -->
  <build>
    <plugins>
      <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
        <version>${spring-boot.version}</version>
        <executions>
          <execution>
            <goals>
              <goal>repackage</goal>
            </goals>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
  
</project>
```

---

### 第六章：依赖管理

#### 6.1 添加依赖

**基本依赖声明：**

```xml
<dependencies>
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <version>3.0.0</version>
  </dependency>
</dependencies>
```

**常用依赖坐标：**

```xml
<dependencies>
  <!-- Spring Boot -->
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <version>3.0.0</version>
  </dependency>
  
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
    <version>3.0.0</version>
  </dependency>
  
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
    <version>3.0.0</version>
  </dependency>
  
  <!-- 数据库驱动 -->
  <dependency>
    <groupId>com.mysql</groupId>
    <artifactId>mysql-connector-j</artifactId>
    <version>8.0.33</version>
    <scope>runtime</scope>
  </dependency>
  
  <dependency>
    <groupId>org.postgresql</groupId>
    <artifactId>postgresql</artifactId>
    <version>42.6.0</version>
    <scope>runtime</scope>
  </dependency>
  
  <!-- 连接池 -->
  <dependency>
    <groupId>com.zaxxer</groupId>
    <artifactId>HikariCP</artifactId>
    <version>5.0.1</version>
  </dependency>
  
  <!-- JSON 处理 -->
  <dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.15.2</version>
  </dependency>
  
  <dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>fastjson</artifactId>
    <version>2.0.32</version>
  </dependency>
  
  <!-- 工具库 -->
  <dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.28</version>
    <scope>provided</scope>
  </dependency>
  
  <dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-lang3</artifactId>
    <version>3.12.0</version>
  </dependency>
  
  <dependency>
    <groupId>com.google.guava</groupId>
    <artifactId>guava</artifactId>
    <version>31.1-jre</version>
  </dependency>
  
  <!-- 日志 -->
  <dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.4.7</version>
  </dependency>
  
  <dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-api</artifactId>
    <version>2.0.7</version>
  </dependency>
  
  <!-- 测试框架 -->
  <dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter</artifactId>
    <version>5.9.2</version>
    <scope>test</scope>
  </dependency>
  
  <dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-core</artifactId>
    <version>5.3.1</version>
    <scope>test</scope>
  </dependency>
  
  <dependency>
    <groupId>org.assertj</groupId>
    <artifactId>assertj-core</artifactId>
    <version>3.24.2</version>
    <scope>test</scope>
  </dependency>
</dependencies>
```

#### 6.2 依赖版本管理

**方式一：使用 properties**

```xml
<properties>
  <spring-boot.version>3.0.0</spring-boot.version>
  <mysql.version>8.0.33</mysql.version>
</properties>

<dependencies>
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter</artifactId>
    <version>${spring-boot.version}</version>
  </dependency>
  
  <dependency>
    <groupId>com.mysql</groupId>
    <artifactId>mysql-connector-j</artifactId>
    <version>${mysql.version}</version>
  </dependency>
</dependencies>
```

**方式二：使用 dependencyManagement (推荐用于多模块项目)**

```xml
<dependencyManagement>
  <dependencies>
    <!-- Spring Boot BOM -->
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-dependencies</artifactId>
      <version>3.0.0</version>
      <type>pom</type>
      <scope>import</scope>
    </dependency>
    
    <!-- 其他依赖版本 -->
    <dependency>
      <groupId>com.mysql</groupId>
      <artifactId>mysql-connector-j</artifactId>
      <version>8.0.33</version>
    </dependency>
  </dependencies>
</dependencyManagement>

<dependencies>
  <!-- 继承 dependencyManagement 中的版本，无需指定 version -->
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter</artifactId>
  </dependency>
  
  <dependency>
    <groupId>com.mysql</groupId>
    <artifactId>mysql-connector-j</artifactId>
  </dependency>
</dependencies>
```

#### 6.3 依赖排除

**排除传递依赖：**

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-web</artifactId>
  <exclusions>
    <!-- 排除 Tomcat，使用 Jetty -->
    <exclusion>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-tomcat</artifactId>
    </exclusion>
    
    <!-- 排除某个有问题的依赖 -->
    <exclusion>
      <groupId>commons-logging</groupId>
      <artifactId>commons-logging</artifactId>
    </exclusion>
  </exclusions>
</dependency>

<!-- 使用 Jetty -->
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-jetty</artifactId>
</dependency>
```

#### 6.4 可选依赖

**标记依赖为可选：**

```xml
<dependency>
  <groupId>org.projectlombok</groupId>
  <artifactId>lombok</artifactId>
  <version>1.18.28</version>
  <optional>true</optional>
</dependency>
```

可选依赖不会被传递到其他依赖此项目的项目中。

#### 6.5 查看依赖树

```bash
# 查看完整依赖树
mvn dependency:tree

# 输出到文件
mvn dependency:tree -DoutputFile=deps.txt

# 查看特定依赖
mvn dependency:tree -Dincludes=org.springframework:*

# 查看依赖哪些库使用了特定依赖
mvn dependency:tree -Dincludes=*:commons-logging

# 详细模式，显示冲突
mvn dependency:tree -Dverbose

# 仅显示直接依赖
mvn dependency:tree -Ddepth=0
```

**分析依赖：**

```bash
# 分析依赖使用情况
mvn dependency:analyze

# 列出未使用的依赖
mvn dependency:analyze-only -DusedDependencies=unused

# 列出使用但未声明的依赖
mvn dependency:analyze-only -DusedDependencies=usedUndeclared
```

---

## ⚙️ 第三部分：高级用法

### 第七章：Maven 生命周期

#### 7.1 三大生命周期

Maven 有三个独立的生命周期：

```
┌────────────────────────────────────────────────────────┐
│                  Maven 三大生命周期                    │
├────────────────────────────────────────────────────────┤
│                                                        │
│  1. clean 生命周期 - 清理项目                         │
│     ├── pre-clean                                      │
│     ├── clean           ← 删除 target 目录             │
│     └── post-clean                                     │
│                                                        │
│  2. default 生命周期 - 构建项目 (主要使用)            │
│     ├── validate          ← 验证项目配置             │
│     ├── initialize                                     │
│     ├── generate-sources                               │
│     ├── process-sources                                │
│     ├── generate-resources                             │
│     ├── process-resources                              │
│     ├── compile           ← 编译源代码               │
│     ├── process-classes                                │
│     ├── generate-test-sources                          │
│     ├── process-test-sources                           │
│     ├── generate-test-resources                        │
│     ├── process-test-resources                         │
│     ├── test-compile      ← 编译测试代码             │
│     ├── process-test-classes                           │
│     ├── test              ← 运行测试                 │
│     ├── prepare-package                                │
│     ├── package           ← 打包 (JAR/WAR)           │
│     ├── pre-integration-test                           │
│     ├── integration-test                               │
│     ├── post-integration-test                          │
│     ├── verify                                         │
│     ├── install           ← 安装到本地仓库           │
│     └── deploy            ← 部署到远程仓库           │
│                                                        │
│  3. site 生命周期 - 生成项目站点                       │
│     ├── pre-site                                       │
│     ├── site              ← 生成站点文档             │
│     ├── post-site                                      │
│     └── site-deploy       ← 部署站点                 │
│                                                        │
└────────────────────────────────────────────────────────┘
```

#### 7.2 默认生命周期详解

**常用阶段说明：**

| 阶段 | 说明 | 常用命令 |
|------|------|----------|
| `validate` | 验证项目配置是否正确 | `mvn validate` |
| `compile` | 编译主代码 | `mvn compile` |
| `test` | 运行单元测试 | `mvn test` |
| `package` | 打包项目 | `mvn package` |
| `verify` | 运行集成测试 | `mvn verify` |
| `install` | 安装到本地仓库 | `mvn install` |
| `deploy` | 部署到远程仓库 | `mvn deploy` |

**阶段依赖关系：**

执行后面的阶段会自动执行前面的所有阶段。

```bash
# 执行 package 会先执行 validate → compile → test → package
mvn package

# 执行 install 会执行到 install 的所有阶段
mvn install

# 执行 deploy 会执行完整流程
mvn deploy
```

#### 7.3 生命周期阶段绑定

**将插件目标绑定到生命周期阶段：**

```xml
<build>
  <plugins>
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-compiler-plugin</artifactId>
      <version>3.11.0</version>
      <executions>
        <execution>
          <id>default-compile</id>
          <phase>compile</phase>
          <goals>
            <goal>compile</goal>
          </goals>
        </execution>
      </executions>
    </plugin>
  </plugins>
</build>
```

---

### 第八章：插件系统

#### 8.1 插件配置基础

**插件基本结构：**

```xml
<plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-compiler-plugin</artifactId>
  <version>3.11.0</version>
  <configuration>
    <source>17</source>
    <target>17</target>
  </configuration>
  <executions>
    <execution>
      <id>compile</id>
      <phase>compile</phase>
      <goals>
        <goal>compile</goal>
      </goals>
    </execution>
  </executions>
</plugin>
```

#### 8.2 常用核心插件

**1. maven-compiler-plugin (编译插件)**

```xml
<plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-compiler-plugin</artifactId>
  <version>3.11.0</version>
  <configuration>
    <source>17</source>
    <target>17</target>
    <encoding>UTF-8</encoding>
    <showWarnings>true</showWarnings>
    <showDeprecation>true</showDeprecation>
    <compilerArgs>
      <arg>-parameters</arg>
    </compilerArgs>
  </configuration>
</plugin>
```

**2. maven-surefire-plugin (测试插件)**

```xml
<plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-surefire-plugin</artifactId>
  <version>3.1.2</version>
  <configuration>
    <includes>
      <include>**/*Test.java</include>
      <include>**/*Tests.java</include>
    </includes>
    <excludes>
      <exclude>**/Abstract*.java</exclude>
    </excludes>
    <testFailureIgnore>false</testFailureIgnore>
    <forkCount>1</forkCount>
    <reuseForks>true</reuseForks>
  </configuration>
</plugin>
```

**3. maven-jar-plugin (JAR 打包插件)**

```xml
<plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-jar-plugin</artifactId>
  <version>3.3.0</version>
  <configuration>
    <archive>
      <manifest>
        <mainClass>com.example.Main</mainClass>
        <addClasspath>true</addClasspath>
        <classpathPrefix>lib/</classpathPrefix>
      </manifest>
    </archive>
  </configuration>
</plugin>
```

**4. maven-war-plugin (WAR 打包插件)**

```xml
<plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-war-plugin</artifactId>
  <version>3.4.0</version>
  <configuration>
    <failOnMissingWebXml>false</failOnMissingWebXml>
    <warSourceDirectory>src/main/webapp</warSourceDirectory>
  </configuration>
</plugin>
```

**5. maven-source-plugin (源码插件)**

```xml
<plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-source-plugin</artifactId>
  <version>3.3.0</version>
  <executions>
    <execution>
      <id>attach-sources</id>
      <goals>
        <goal>jar-no-fork</goal>
      </goals>
    </execution>
  </executions>
</plugin>
```

**6. maven-javadoc-plugin (文档插件)**

```xml
<plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-javadoc-plugin</artifactId>
  <version>3.5.0</version>
  <configuration>
    <encoding>UTF-8</encoding>
    <charset>UTF-8</charset>
    <docencoding>UTF-8</docencoding>
  </configuration>
  <executions>
    <execution>
      <id>attach-javadocs</id>
      <goals>
        <goal>jar</goal>
      </goals>
    </execution>
  </executions>
</plugin>
```

**7. maven-assembly-plugin (装配插件)**

```xml
<plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-assembly-plugin</artifactId>
  <version>3.6.0</version>
  <configuration>
    <descriptorRefs>
      <descriptorRef>jar-with-dependencies</descriptorRef>
    </descriptorRefs>
    <archive>
      <manifest>
        <mainClass>com.example.Main</mainClass>
      </manifest>
    </archive>
  </configuration>
  <executions>
    <execution>
      <id>make-assembly</id>
      <phase>package</phase>
      <goals>
        <goal>single</goal>
      </goals>
    </execution>
  </executions>
</plugin>
```

**8. maven-shade-plugin (阴影插件，创建 fat JAR)**

```xml
<plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-shade-plugin</artifactId>
  <version>3.5.0</version>
  <executions>
    <execution>
      <phase>package</phase>
      <goals>
        <goal>shade</goal>
      </goals>
      <configuration>
        <transformers>
          <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
            <mainClass>com.example.Main</mainClass>
          </transformer>
        </transformers>
      </configuration>
    </execution>
  </executions>
</plugin>
```

#### 8.3 自定义插件配置

**配置插件执行参数：**

```xml
<plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-surefire-plugin</artifactId>
  <version>3.1.2</version>
  <configuration>
    <!-- 跳过测试 -->
    <skipTests>true</skipTests>
    
    <!-- 并行执行测试 -->
    <parallel>methods</parallel>
    <threadCount>4</threadCount>
    
    <!-- 测试超时 -->
    <forkedProcessTimeoutInSeconds>300</forkedProcessTimeoutInSeconds>
    
    <!-- 系统属性 -->
    <systemPropertyVariables>
      <java.awt.headless>true</java.awt.headless>
      <test.env>ci</test.env>
    </systemPropertyVariables>
  </configuration>
</plugin>
```

#### 8.4 插件执行时机

**手动执行插件：**

```bash
# 执行特定插件目标
mvn compiler:compile
mvn surefire:test
mvn javadoc:javadoc

# 执行多个插件目标
mvn compiler:compile surefire:test
```

**绑定到生命周期：**

```xml
<executions>
  <execution>
    <id>custom-execution</id>
    <phase>package</phase>
    <goals>
      <goal>single</goal>
    </goals>
  </execution>
</executions>
```

---

### 第九章：多模块项目

#### 9.1 多模块项目结构

```
parent-project/
├── pom.xml                    # 父 POM
├── module-core/               # 核心模块
│   ├── pom.xml
│   └── src/
├── module-service/            # 服务模块
│   ├── pom.xml
│   └── src/
├── module-web/                # Web 模块
│   ├── pom.xml
│   └── src/
└── module-api/                # API 模块
    ├── pom.xml
    └── src/
```

#### 9.2 父 POM 配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0">
  <modelVersion>4.0.0</modelVersion>
  
  <groupId>com.example</groupId>
  <artifactId>parent-project</artifactId>
  <version>1.0.0</version>
  <packaging>pom</packaging>
  <name>Parent Project</name>
  <description>多模块项目的父 POM</description>
  
  <!-- 子模块列表 -->
  <modules>
    <module>module-core</module>
    <module>module-api</module>
    <module>module-service</module>
    <module>module-web</module>
  </modules>
  
  <!-- 统一属性配置 -->
  <properties>
    <java.version>17</java.version>
    <maven.compiler.source>${java.version}</maven.compiler.source>
    <maven.compiler.target>${java.version}</maven.compiler.target>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    
    <!-- 依赖版本 -->
    <spring-boot.version>3.0.0</spring-boot.version>
    <mysql.version>8.0.33</mysql.version>
  </properties>
  
  <!-- 统一依赖版本管理 -->
  <dependencyManagement>
    <dependencies>
      <!-- Spring Boot BOM -->
      <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-dependencies</artifactId>
        <version>${spring-boot.version}</version>
        <type>pom</type>
        <scope>import</scope>
      </dependency>
      
      <!-- 内部模块 -->
      <dependency>
        <groupId>com.example</groupId>
        <artifactId>module-core</artifactId>
        <version>${project.version}</version>
      </dependency>
      
      <dependency>
        <groupId>com.example</groupId>
        <artifactId>module-api</artifactId>
        <version>${project.version}</version>
      </dependency>
    </dependencies>
  </dependencyManagement>
  
