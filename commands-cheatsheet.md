# Maven 命令速查表

## 📦 构建命令

```bash
# 清理项目
mvn clean

# 编译主代码
mvn compile

# 编译测试代码
mvn test-compile

# 运行测试
mvn test

# 打包项目 (JAR/WAR)
mvn package

# 安装到本地仓库
mvn install

# 部署到远程仓库
mvn deploy

# 完整构建流程
mvn clean install

# 跳过测试
mvn clean install -DskipTests

# 跳过测试和检查
mvn clean install -Dmaven.test.skip=true
```

## 🔍 依赖命令

```bash
# 查看依赖树
mvn dependency:tree

# 查看完整依赖树（含冲突）
mvn dependency:tree -Dverbose

# 查看特定依赖
mvn dependency:tree -Dincludes=org.springframework:*

# 分析依赖使用
mvn dependency:analyze

# 列出未使用的依赖
mvn dependency:analyze -DusedDependencies=unused

# 复制依赖到目录
mvn dependency:copy-dependencies -DoutputDirectory=lib

# 解析依赖
mvn dependency:resolve
```

## 🔌 插件命令

```bash
# 查看有效 POM
mvn help:effective-pom

# 查看有效配置
mvn help:effective-settings

# 查看系统信息
mvn help:system

# 查看插件信息
mvn plugin:help

# 查看特定插件信息
mvn help:describe -Dplugin=compiler

# 查看插件目标
mvn help:describe -Dplugin=compiler -Ddetail
```

## 🏗️ 项目命令

```bash
# 创建新项目
mvn archetype:generate

# 创建简单 Java 项目
mvn archetype:generate -DgroupId=com.example -DartifactId=my-app -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

# 创建 Web 项目
mvn archetype:generate -DgroupId=com.example -DartifactId=my-webapp -DarchetypeArtifactId=maven-archetype-webapp -DinteractiveMode=false

# 更新项目版本
mvn versions:set -DnewVersion=2.0.0

# 回滚版本更改
mvn versions:revert

# 提交版本更改
mvn versions:commit

# 显示依赖更新
mvn versions:display-dependency-updates

# 显示插件更新
mvn versions:display-plugin-updates
```

## 📊 报告命令

```bash
# 生成项目报告
mvn site

# 生成 Javadoc
mvn javadoc:javadoc

# 生成测试报告
mvn surefire-report:report

# 生成检查报告
mvn checkstyle:check
```

## 🔧 实用命令

```bash
# 离线模式
mvn -o clean install

# 更新快照依赖
mvn -U clean install

# 调试模式
mvn -X clean install

# 错误输出
mvn -e clean install

# 并行构建 (4 线程)
mvn -T 4 clean install

# 仅构建特定模块
mvn -pl module-name clean install

# 构建模块及其依赖
mvn -pl module-name -am clean install

# 排除模块
mvn -pl !module-name clean install

# 激活 Profile
mvn -Pprod clean install

# 设置属性
mvn clean install -DskipTests -Dmaven.javadoc.skip=true
```

## 🎯 常用组合

```bash
# 快速构建（跳过测试和文档）
mvn clean package -DskipTests -Dmaven.javadoc.skip=true

# 完整构建并安装
mvn clean install

# 构建并运行
mvn clean package spring-boot:run

# 构建可执行 JAR
mvn clean package

# 多模块项目构建
mvn clean install -T 4

# 生产环境构建
mvn clean install -Pprod

# 仅运行特定测试
mvn test -Dtest=MyTestClass

# 仅运行特定测试方法
mvn test -Dtest=MyTestClass#myTestMethod
```

## 🐛 故障排查

```bash
# 查看 Maven 版本
mvn -v

# 查看本地仓库位置
mvn help:effective-settings | grep localRepository

# 清理本地仓库特定依赖
rm -rf ~/.m2/repository/com/problematic/dependency

# 强制更新依赖
mvn -U clean install

# 查看构建日志
mvn clean install -X > build.log
```
