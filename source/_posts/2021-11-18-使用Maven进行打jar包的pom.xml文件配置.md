---
title: Harbor仓库主从同步
author: Yangself
date: 2021-11-18 09:28:00 +0800
categories: [解决方案, Java]
tags: [Java]
---

## 使用Maven进行打jar包的pom.xml文件配置

```xml
<build>
      <plugins>
        <plugin>
          <!-- NOTE: 不须要设置groupId,由于默认为org.apache.maven.plugins -->
          <artifactId>maven-assembly-plugin</artifactId>
          <version>3.2.0</version>
          <configuration>
            <descriptorRefs>
              <descriptorRef>jar-with-dependencies</descriptorRef>
            </descriptorRefs>
            <!-- 指定jar包名称，需appendAssemblyId和finalName配合使用 -->
            <appendAssemblyId>false</appendAssemblyId>
            <finalName>${project.artifactId}-full</finalName>
            <archive>
              <manifest>
                <!-- 设置主类-->
                <mainClass>tech.nosql.MainTask</mainClass>
                <!-- 将版本信息写入MANIFEST.MF -->
                <addDefaultImplementationEntries>true</addDefaultImplementationEntries>
              </manifest>
            </archive>
          </configuration>
          <executions>
            <execution>
              <!-- this is used for inheritance merges -->
              <id>make-assembly</id>
              <!-- 将assembly绑定到package阶段 -->
              <phase>package</phase>
              <goals>
                <goal>single</goal>
              </goals>
            </execution>
          </executions>
        </plugin>
      </plugins>
  </build>
```
