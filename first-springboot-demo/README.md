# Spring Boot 项目 Mac 环境配置指南

## 1. 安装 Maven

使用 Homebrew 安装 Maven：

```bash
# 安装 Homebrew（如果尚未安装）
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# 先升级brew
brew update

# 安装 Maven
brew install maven
```

安装完成后，验证安装：
```bash
mvn -v
```

## 2. 配置 Maven

### 2.1 创建 Maven 配置目录
```bash
mkdir -p ~/.m2
```

### 2.2 创建 Maven 配置文件
创建或编辑 `~/.m2/settings.xml` 文件，添加以下配置：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">
    
    <!-- 本地仓库路径配置 -->
    <localRepository>${user.home}/.m2/repository</localRepository>

    <!-- 配置阿里云镜像 -->
    <mirrors>
        <mirror>
            <id>aliyunmaven</id>
            <mirrorOf>*</mirrorOf>
            <name>阿里云公共仓库</name>
            <url>https://maven.aliyun.com/repository/public</url>
        </mirror>
    </mirrors>

    <!-- 配置默认使用的JDK版本 -->
    <profiles>
        <profile>
            <id>jdk-24</id>
            <activation>
                <activeByDefault>true</activeByDefault>
                <jdk>24</jdk>
            </activation>
            <properties>
                <maven.compiler.source>24</maven.compiler.source>
                <maven.compiler.target>24</maven.compiler.target>
                <maven.compiler.compilerVersion>24</maven.compiler.compilerVersion>
            </properties>
        </profile>
    </profiles>
</settings>
```

## 3. 启动 Spring Boot 项目

### 3.1 初始化springboot项目

进入 https://start.spring.io/初始化项目，选择maven，jdk17，然后下载zip包解压

```bash
cd 项目根目录
```

修改pom.xml中的jdk版本和机器匹配，	<java.version>8</java.version>


### 3.2 使用 Maven 启动项目
```bash
mvn spring-boot:run
```

### 3.3 验证项目运行
打开浏览器访问：
- http://localhost:8080/hello - 默认返回 "Hello World!"
- http://localhost:8080/hello?name=YourName - 返回自定义问候语

## 4. 常见问题解决

### 4.1 Maven 下载依赖过慢
- 检查 settings.xml 中的阿里云镜像配置是否正确
- 可以尝试清理 Maven 本地仓库：`mvn clean`

### 4.2 JDK 版本不匹配
- 确保本地安装的 JDK 版本与 pom.xml 中指定的版本一致
- 检查 JAVA_HOME 环境变量是否正确设置

### 4.3 端口占用
如果 8080 端口被占用，可以在 application.properties 中修改端口：
```properties
server.port=8081
```

## 5. 项目结构
```
demo/
├── src/
│   └── main/
│       └── java/
│           └── com/
│               └── example/
│                   └── demo/
│                       └── DemoApplication.java
├── pom.xml
└── README.md
```

## 6. IntelliJ IDEA Maven 配置

### 6.1 配置 Maven 路径
1. 打开 IDEA 偏好设置（Preferences）：
   - Mac：`Command + ,`
   - 或者点击 `IntelliJ IDEA -> Settings`

2. 在左侧导航栏找到 `Build, Execution, Deployment -> Build Tools -> Maven`

3. 设置以下配置：
   - Maven home path: `/opt/homebrew/Cellar/maven/3.9.10/libexec` (使用 `brew info maven` 查看具体路径)
   - User settings file: `/Users/<用户名>/.m2/settings.xml`
   - Local repository: `/Users/<用户名>/.m2/repository`

### 6.2 配置 Maven 自动导入
1. 在同一个 Maven 设置页面中：
   - 勾选 `Import Maven projects automatically`
   - 勾选 `Automatically download`
     - Sources
     - Documentation
     - Annotations

### 6.3 项目 Maven 设置
1. 右键点击项目根目录
2. 选择 `Maven -> Reload project`

### 6.4 Maven 工具窗口
1. 右侧工具栏点击 `Maven`
2. 展开项目 -> Lifecycle
   - clean：清理项目
   - compile：编译项目
   - test：运行测试
   - package：打包项目
   - install：安装到本地仓库

### 6.5 常用 Maven 命令快捷方式
在 IDEA 的 Maven 工具窗口中，可以直接双击执行以下命令：
```bash
clean：清理构建文件
compile：编译项目
test：运行测试
package：打包项目
install：安装到本地仓库
spring-boot:run：运行 Spring Boot 项目
```

### 6.6 IDEA Maven 常见问题
1. Maven 项目无法导入
   - 检查 Maven 版本是否匹配
   - 删除 .idea 文件夹和 *.iml 文件，重新导入项目

2. 依赖下载失败
   - 检查 settings.xml 中的镜像配置
   - 在 Maven 工具窗口点击 '刷新' 按钮
   - 手动删除未下载成功的依赖，重新下载

3. Maven 插件无法使用
   - 检查 IDEA 的 Maven 设置是否正确
   - 确保 Maven 相关缓存目录权限正确

4. 编译时 JDK 版本错误
   - 检查项目结构（Project Structure）中的 SDK 版本
   - 确保 pom.xml 中的 Java 版本与项目 JDK 一致

## 7. 配置多个 Git 账号

### 7.1 生成 SSH 密钥
```bash
# 为私人 GitHub 账号生成 SSH 密钥
ssh-keygen -t ed25519 -C "你的私人邮箱" -f ~/.ssh/id_ed25519_personal

# 为公司 GitHub 账号生成 SSH 密钥
ssh-keygen -t ed25519 -C "你的公司邮箱" -f ~/.ssh/id_ed25519_company
```

### 7.2 配置 SSH Config
编辑或创建 `~/.ssh/config` 文件：
```bash
# 私人 GitHub 账号
Host github.com-personal
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_personal
    
# 公司 GitHub 账号
Host github.com-company
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_company
```

### 7.3 添加 SSH 密钥到 GitHub
1. 复制私人 SSH 公钥：
```bash
cat ~/.ssh/id_ed25519_personal.pub
```
2. 登录私人 GitHub 账号
3. 进入 Settings -> SSH and GPG keys -> New SSH key
4. 粘贴公钥内容并保存

### 7.4 配置本地仓库
```bash
# 初始化 Git 仓库（如果还未初始化）
git init

# 配置本地仓库的用户信息
git config user.name "你的私人 GitHub 用户名"
git config user.email "你的私人邮箱"

# 添加远程仓库（使用 SSH 配置中定义的 Host）
git remote add origin git@github.com-personal:你的用户名/仓库名.git
```

### 7.5 推送代码到私人仓库
```bash
# 添加所有文件到暂存区
git add .

# 提交更改
git commit -m "Initial commit"

# 推送到主分支
git push -u origin main
```

### 7.6 验证 SSH 连接
```bash
# 测试私人账号连接
ssh -T github.com-personal

# 测试公司账号连接
ssh -T github.com-company
```

### 7.7 常见问题
1. 推送失败
   - 检查 SSH 密钥是否正确添加到 GitHub
   - 确认远程仓库 URL 是否正确
   - 验证 SSH 连接是否成功

2. 身份验证错误
   - 确保使用了正确的 SSH 配置
   - 检查 Git 配置的用户信息是否正确

3. 多账号切换
   - 使用 `git config user.name` 和 `git config user.email` 检查当前仓库配置
   - 需要时可以使用 `--global` 参数设置全局配置 