---
title: MacOS 配置JDK和Gradle环境变量
date: 2020-01-02 20:46:41
tags: mac
---

## MacOS JDK & Gradle & Maven Settings

# 一. 背景

* mac系统下配置开发环境变量

<!--more-->
# 二. 准备条件
* [下载`jdk 1.8`](https://www.oracle.com/technetwork/java/javase/downloads/jdk13-downloads-5672538.html)
* 国内镜像地址 [https://injdk.cn/](https://injdk.cn/)
* [下载`gradle 4.10.3`](https://gradle.org/next-steps/?version=4.10.3&format=bin)
* [下载`maven 3.6.3`](http://mirror.bit.edu.cn/apache/maven/maven-3/3.6.3/binaries/apache-maven-3.6.3-bin.zip)

# 三. 步骤
* `vim /etc/profile `然后按住`shift +G ` 添加以下脚本后执行`source /etc/profile`
```shell
  JAVA_HOME="/Library/Java/JavaVirtualMachines/jdk1.8.0_221.jdk/Contents/Home/"
  CLASS_PATH="$JAVA_HOME/lib"
  PATH=".:$PATH:$JAVA_HOME/bin"
```
* `vim ~/.bash_profile` 配置gradle 和maven
```shell
  export M2_HOME=/usr/local/maven3.6.3/
  export PATH=$PATH:$M2_HOME/bin
  GRADLE_HOME=/usr/local/gradle4.10.3
  export GRADLE_HOME
  export PATH=$PATH:$GRADLE_HOME/bin
```
* `vim ~/.zshrc` 在底部新增`source ~/.bash_profile`

  ```shell
  #gradle 配置阿里云加速
  allprojects {
      repositories {
          maven { url 'https://maven.aliyun.com/repository/public/' }
          mavenLocal()
          mavenCentral()
      }
  }
  #在maven 的settings.xml文件中配置jar包仓库本地地址 
  <localRepository>/usr/local/xl_repo</localRepository>
  #然后找到mirror配置以下
  <mirror>
      <id>aliyunmaven</id>
      <mirrorOf>*</mirrorOf>
      <name>阿里云公共仓库</name>
      <url>https://maven.aliyun.com/repository/public</url>
  </mirror>
  ```

  
# 四. 参考
* [aliyun mirror manual](https://help.aliyun.com/document_detail/102512.html?spm=a2c40.aliyun_maven_repo.0.0.361865e9b6VaFh)
* [https://javalibs.com/](https://javalibs.com/) maven repository
* [huaweiCloud mirrors](https://mirrors.huaweicloud.com/java/jdk/8u172-b11/)