# Java开发环境配置（macOS）

_本文主要介绍JDK在macOS上的安装和环境变量的配置。_

## **1、JDK 安装**

#### 1）下载JDK

Oracle JDK： [https://www.oracle.com/technetwork/java/javase/downloads/index.html](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

OpenJDK： [http://jdk.java.net/](http://jdk.java.net/)\


例如，安装JDK12，在macOS上安装，文件下载地址：[https://download.java.net/java/GA/jdk12.0.1/69cfe15208a647278a19ef0990eea691/12/GPL/openjdk-12.0.1\_osx-x64\_bin.tar.gz](https://download.java.net/java/GA/jdk12.0.1/69cfe15208a647278a19ef0990eea691/12/GPL/openjdk-12.0.1\_osx-x64\_bin.tar.gz)

```
wget https://download.java.net/java/GA/jdk12.0.1/69cfe15208a647278a19ef0990eea691/12/GPL/openjdk-12.0.1_osx-x64_bin.tar.gz
```

#### 2）解压安装包

macOS系统中默认安装位置：`/Library/Java/JavaVirtualMachines/`

```
sudo tar -zxf  openjdk-12.0.1_osx-x64_bin.tar.gz -C /Library/Java/JavaVirtualMachines/
```

#### 3）验证是否安装成功

终端输入以下命令：

```
java -version
```

显示

```bash
openjdk 12.0.1 2019-04-16
OpenJDK Runtime Environment (build 12.0.1+12)
OpenJDK 64-Bit Server VM (build 12.0.1+12, mixed mode, sharing)
```

## **2、JDK多版本间切换（手动）**

安装成功JDK后，可能之前还有之前版本的JDK。下面看一下多版本JDK切换问题

#### 1）查看所有JDK的在系统中默认的安装位置

```
/usr/libexec/java_home  -V
```

如果有多个即显示如下：

```
Matching Java Virtual Machines (2):
    11.0.1, x86_64: "OpenJDK 11.0.1" /Library/Java/JavaVirtualMachines/jdk-11.0.1.jdk/Contents/Home
    9.0.1, x86_64: "Java SE 9.0.1" /Library/Java/JavaVirtualMachines/jdk-9.0.1.jdk/Contents/Home
/Library/Java/JavaVirtualMachines/jdk-11.0.1.jdk/Contents/Home
```

#### 2）查看指定版本JDK在系统中默认安装位置

```
/usr/libexec/java_home -v 9
```

显示对应目录：

```
/Library/Java/JavaVirtualMachines/jdk-9.0.1.jdk/Contents/Home
```

#### 3）手动切换JDK版本

通过修改 `~/.bash_profile`文件修改JAVA\_HOME，如果没有这个文件则需要新建一个。使用命令别名的方式alias是自定义命令别名

```
export JAVA_8_HOME=$(/usr/libexec/java_home -v1.8)
export JAVA_9_HOME=$(/usr/libexec/java_home -v9)
export JAVA_10_HOME=$(/usr/libexec/java_home -v10)
export JAVA_11_HOME=$(/usr/libexec/java_home -v11)

alias java8='export JAVA_HOME=$JAVA_8_HOME'
alias java9='export JAVA_HOME=$JAVA_9_HOME'
alias java10='export JAVA_HOME=$JAVA_10_HOME'
alias java11='export JAVA_HOME=$JAVA_11_HOME'
#默认是Java 11
```

执行下面命令，让文件生效

```
source ~/.bash_profile
```

切换JDK版本就执行对应命令别名，比如，我要切换成JDK9，就执行一下java9就可以了。

## **3、JDK多版本间切换（工具）**

可以使用JEnv进行切换

#### 1）安装JEnv（Homebrew）

```
 brew install jenv
```

#### 2）配置JEnv环境变量

bash配置中添加

```
echo 'export PATH="$HOME/.jenv/bin:$PATH"' >> ~/.bash_profile \
&&echo 'eval "$(jenv init -)"' >> ~/.bash_profile \
&&source ~/.bash_profile
```

报这个错`(ln: /Users/levizhong/.jenv/versions/oracle64-9.0.1: No such file or directory`或`ln: /Users/levizhong/.jenv/versions/openjdk64-11.0.1: No such file or directory)`

就是没有配置JEnv环境变量

#### 3）查看JEnv管理所有的JDK版本

```
jenv versions
```

显示：

```
* system (set by /Users/levizhong/.jenv/version)
  11.0
  11.0.1
  9.0
  9.0.1
  openjdk64-11.0.1
  oracle64-9.0.1
```

#### 4）切换成想要使用的JDK版

```
jenv global 11.0
```

更多Jenv命令请看jenv相关文档：[JEnv docs](https://github.com/gcuisinier/jenv/wiki)

#### 5）其它类似工具

SDKMAN：类似nvm工具，可以处理安装和切换不同的JDK版本。但会将已安装的JDK放入其自己的目录中，这通常是`~/.sdkman/candidates/java`。SDKMAN允许设置全局默认版本以及特定于当前shell的版本。使用文档：[https://sdkman.io/usage](https://sdkman.io/usage)

Jabba：可以安装和切换不同版本JDK。Jabba也是将已安装的JDK放入其自己的目录中，这通常是\~/.jabba/jdk，使用文档：[https://github.com/shyiko/jabba#usage](https://github.com/shyiko/jabba#usage)
