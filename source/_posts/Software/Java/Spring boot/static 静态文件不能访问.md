---
title: Spring boot static 静态文件不能访问
categories:
  - Software
  - Java
  - Spring boot
abbrlink: 8f126aab
---
# static 静态文件不能访问

## Spring boot static 静态文件不能访问原因记录

1、Spring boot 2.7+ 配置文件 继承 extends WebMvcConfigurationSupport 后不能访问

When developing a Spring Boot application, it's important to be able to serve static content such as images, HTML, CSS, and JavaScript files. However, sometimes these static files may not be accessible even if they are placed in the correct directory.

One common reason for this issue is that Spring Boot serves static content from the `/static` or `/public` directory by default. If the static files are located in a different directory, they may not be accessible.

To solve this issue, you can configure the location of the static files by adding the following code to your `application.properties` file:

```yaml
spring:
  mvc:
    static-path-pattern: "/static/**"
  web:
    resources:
      static-locations: classpath:/static/, classpath:/public/, file:/home/static/

```

This code tells Spring Boot to look for static files in the `/static` directory within the classpath.

Another reason why static files may not be accessible is if they are not included in the build process. Make sure that the static files are included in the `src/main/resources/static` directory and that they are added to the build process.

In conclusion, if you're having trouble accessing static files in your Spring Boot application, make sure that they are located in the correct directory and that they are included in the build process. Additionally, you can configure the location of the static files in the `application.properties` file to ensure that they are accessible.

在Spring cloud 环境下 要访问单个服务的静态资源需要 在静态资源路径加路径标识 如: resources/static/cms/assets/***