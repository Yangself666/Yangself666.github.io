---
title: 返回json空值去掉null和long型数据精度丢失问题
author: Yangself
date: 2021-06-18 11:01:00 +0800
categories: [解决方案, Java]
tags: [Java]
---
一个SpringBoot的Config文件

全局配置Long转String

接收时忽略实体类中不存在的字段

去除序列化中的null和空字符串

防止Long型数据丢失精度

<!-- more -->

```java
 /**
     * 全局配置Long转String
     * 接收时忽略实体类中不存在的字段
     * 去除序列化中的null和空字符串
     * 防止Long型数据丢失精度
     *
     * @author changwei
     * @date 2020/8/20 9:05 上午
     */
    @EnableWebMvc
    @Configuration
    public class WebDataConvertConfig implements WebMvcConfigurer {
        @Override
        public void configureMessageConverters(List<HttpMessageConverter<?>> converters) {
            MappingJackson2HttpMessageConverter jackson2HttpMessageConverter = new MappingJackson2HttpMessageConverter();
            ObjectMapper objectMapper = new ObjectMapper();
            //去除序列化中的null和空字符串
            objectMapper.setSerializationInclusion(JsonInclude.Include.NON_ABSENT);
            //全局忽略实体类中不存在的字段
            objectMapper.configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES, false);
            /**
             * 序列换成json时,将所有的long变成string
             * 因为js中得数字类型不能包含所有的java long值
             */
            SimpleModule simpleModule = new SimpleModule();
            simpleModule.addSerializer(Long.class, ToStringSerializer.instance);
            simpleModule.addSerializer(Long.TYPE, ToStringSerializer.instance);
            objectMapper.registerModule(simpleModule);
            jackson2HttpMessageConverter.setObjectMapper(objectMapper);
            converters.add(jackson2HttpMessageConverter);
        }
    }
```
