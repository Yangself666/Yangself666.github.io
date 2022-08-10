---
title: SpringCloudGateway和微服务多重跨域问题解决
author: Yangself
date: 2022-08-10 11:26:00 +0800
categories: [解决方案, Java]
tags: [Java]
---

## GateWay跨域问题解决(双重)

原文链接：[https://www.jianshu.com/p/bf997c524a58](https://www.jianshu.com/p/bf997c524a58)

gateway中全局的跨域问题解决两种方式，一种是创建配置类不推荐，如有需要可以自己查询相关资料；另一种是使用配置文件，相对简单，如下：

```yaml
spring:
  cloud:
    gateway:
      # gateway的全局跨域请求配置
      globalcors:
        corsConfigurations:
          '[/**]':
            allowedHeaders: "*"
            allowedOrigins: "*"
            allowCredentials: true
            allowedMethods: "*"
```

上述属性值可以根据自己的需要配置，"*"表示所有。

正常情况下基于以上配置即可，但是由于目前的项目的下游微服务也配置了可以跨域的相关配置，这就导致返回的ResponseHeader中有多重属性，这个多重属性浏览器是不认的。所以基于此的处理方法，把下游的所有配置都取消，但是下游服务数量又太多，所以通过查询找到了以下的方案，参考[https://github.com/spring-cloud/spring-cloud-gateway/issues/728](https://github.com/spring-cloud/spring-cloud-gateway/issues/728)

```yaml
spring:
  cloud:
    gateway:
      # gateway的全局跨域请求配置
      globalcors:
        corsConfigurations:
          '[/**]':
            allowedHeaders: "*"
            allowedOrigins: "*"
            allowCredentials: true
            allowedMethods: "*"
      default-filters:
      - DedupeResponseHeader=Access-Control-Allow-Origin Access-Control-Allow-Credentials Vary, RETAIN_UNIQUE
```

在配置文件中添加上面的过滤器，这个过滤器的作用是剔除重复的响应头。gateway的内置的过滤器可以参考这篇文章：[https://blog.csdn.net/tuyong1972873004/article/details/107123254](https://blog.csdn.net/tuyong1972873004/article/details/107123254)。
内置的过滤器有很多，可以根据自己的实际需求去配置。

正常情况下以上配置就可以解决上述的问题，但是发现后台报错，错误信息提示无法找到DedupeResponseHeaderGateWayFilter。后来查询资料才发现，这个过滤器是GreenwichSR2版本提供的新特性，当前的springcloud版本太低。所以最后没有办法，只能手动实现一个和DedupeResponseHeaderGateWayFilter功能相同的过滤器。代码如下：

```java
import org.springframework.cloud.gateway.filter.GatewayFilterChain;
import org.springframework.cloud.gateway.filter.GlobalFilter;
import org.springframework.cloud.gateway.filter.NettyWriteResponseFilter;
import org.springframework.core.Ordered;
import org.springframework.http.HttpHeaders;
import org.springframework.stereotype.Component;
import org.springframework.web.server.ServerWebExchange;
import reactor.core.publisher.Mono;
import java.util.ArrayList;


@Component("corsResponseHeaderFilter")
public class CorsResponseHeaderFilter implements GlobalFilter, Ordered {

    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        return chain.filter(exchange).then(Mono.defer(() -> {
            exchange.getResponse().getHeaders().entrySet().stream()
                    .filter(kv -> (kv.getValue() != null && kv.getValue().size() > 1))
                    .filter(kv -> (kv.getKey().equals(HttpHeaders.ACCESS_CONTROL_ALLOW_ORIGIN)
                            || kv.getKey().equals(HttpHeaders.ACCESS_CONTROL_ALLOW_CREDENTIALS)))
                    .forEach(kv -> {
                        kv.setValue(new ArrayList<String>() {{
                            add(kv.getValue().get(0));
                        }});
                    });

            return chain.filter(exchange);
        }));
    }

    @Override
    public int getOrder() {
        // 指定此过滤器位于NettyWriteResponseFilter之后
        // 即待处理完响应体后接着处理响应头
        return NettyWriteResponseFilter.WRITE_RESPONSE_FILTER_ORDER + 1;
    }
}
```

加上以上配置类后，问题解决。
