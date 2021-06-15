---
title: idea默认gitignore文件
author: Yangself
date: 2021-06-15 13:43:00 +0800
categories: [解决方案, Java]
tags: [Java]
---
下面是一个IDEA自动生成的.gitignore文件，贴在这里留着创建项目时使用

<!-- more -->

```bash
HELP.md
target/
!.mvn/wrapper/maven-wrapper.jar
!**/src/main/**/target/
!**/src/test/**/target/

### STS ###
.apt_generated
.classpath
.factorypath
.project
.settings
.springBeans
.sts4-cache

### IntelliJ IDEA ###
.idea
*.iws
*.iml
*.ipr

### NetBeans ###
/nbproject/private/
/nbbuild/
/dist/
/nbdist/
/.nb-gradle/
build/
!**/src/main/**/build/
!**/src/test/**/build/

### VS Code ###
.vscode/
```
