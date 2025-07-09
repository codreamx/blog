---
title: Thymeleaf pagination
categories:
  - Software
  - Java
  - Spring boot
excerpt: Thymeleaf 分页模板
abbrlink: 7ce9e0be
---

# Thymeleaf 分页模板

> url 拼接函数

```java

import org.springframework.stereotype.Component;
import org.springframework.util.StringUtils;

@Component("pagination")
public class Pagination {
    static final String Q = "?";
    static final String PAGE = "page=";
    static final String SIZE = "size=";

    public String link(String queryString, int page, int size) {
        if (page < 0) {
            page = 0;
        }
        if (!StringUtils.hasText(queryString)) {
            return String.format("?page=%d&size=%d", page, size);
        }
        if (queryString.contains(PAGE)) {
            queryString = queryString.replaceFirst("page=[^&]*", PAGE + page);
        } else {
            queryString = queryString + "&page=" + page;
        }
        if (size > 0) {
            if (queryString.contains(SIZE)) {
                queryString = queryString.replaceFirst("size=[^&]*", SIZE + size);
            } else {
                queryString = queryString + "&size=" + size;
            }
        }
        return Q + queryString;
    }
}

```

> 加入模型参数 

```java
modelAndView.addObject("page", paging);//Page<T> paging
```

> pagination.html

```xml
<!DOCTYPE html SYSTEM "http://www.thymeleaf.org/dtd/xhtml1-strict-thymeleaf-4.dtd">
<html xmlns:th="http://www.thymeleaf.org" lang="zh">
<nav th:fragment="pagination" class="fixed-bottom mx-4" aria-label="Page Navigation" th:if="${page != null && page.totalPages gt 0}" th:with="queryString=${#httpServletRequest.getQueryString()}">
    <ul th:if="${page.totalPages le 7}" class="pagination justify-content-end">
        <li class="page-item" th:classappend="${page.number eq 0} ? 'disabled' : ''">
            <a class="page-link" th:href="${@pagination.link(queryString, page.number - 1, page.size)}" aria-label="Previous"><span aria-hidden="true">&laquo;</span></a>
        </li>
        <li class="page-item" th:each="i : ${#numbers.sequence(0, page.totalPages - 1)}" th:classappend="${page.number eq i} ? 'active' : ''">
            <a class="page-link" th:href="${@pagination.link(queryString, i, page.size)}" th:text="${i + 1}"></a>
        </li>
        <!--th:unless="${page.number + 1 == page.totalPages}"-->
        <li class="page-item" th:classappend="${(page.number + 1) eq page.totalPages} ? 'disabled' : ''">
            <a class="page-link" th:href="${@pagination.link(queryString, page.number + 1, page.size)}" aria-label="Next"><span aria-hidden="true">&raquo;</span></a>
        </li>
        <li class="page-item disabled">
            <a class="page-link">
                <span aria-hidden="true" th:text="|共(${page.totalElements})条|"></span>
            </a>
        </li>
    </ul>
    <ul th:if="${page.totalPages gt 7}" class="pagination justify-content-end">
        <!-- 上一页 th:if="${page.number > 0}"-->
        <li class="page-item" th:classappend="${page.number eq 0} ? 'disabled' : ''">
            <a class="page-link" th:href="${@pagination.link(queryString, page.number - 1, page.size)}" aria-label="Previous"><span aria-hidden="true">&laquo;</span></a>
        </li>
        <li class="page-item" th:classappend="${page.number eq 0} ? 'active' : ''">
            <a class="page-link" th:href="${@pagination.link(queryString, 0, page.size)}">1</a>
        </li>
        <li class="page-item" th:if="${(page.number + 1) > 4}">
            <a class="page-link" href="javascript:void(0);" aria-label="···">
                <span aria-hidden="true">···</span>
            </a>
        </li>
        <!-- ================================== 固定头 ================================-->
        <li class="page-item" th:each="i:${#numbers.sequence(1, page.totalPages - 2)}" th:if="${i >= (page.number - 2) and i <= (page.number + 2)}" th:classappend="${page.number eq i} ? 'active' : ''">
            <a class="page-link" th:href="${@pagination.link(queryString, i, page.size)}">
                <span th:text="${i + 1}"></span>
            </a>
        </li>
        <!-- ================================== 固定尾 ================================-->
        <li class="page-item" th:if="${(page.number + 1) < page.totalPages - 3}">
            <a class="page-link" href="javascript:void(0);" aria-label="···">
                <span aria-hidden="true">···</span>
            </a>
        </li>
        <!-- 最后一页 -->
        <li class="page-item" th:classappend="${page.number eq (page.totalPages - 1)} ? 'active' : ''">
            <a class="page-link" th:href="${@pagination.link(queryString, page.totalPages - 1, page.size)}" th:text="${page.totalPages}">latest</a>
        </li>
        <!-- 下一页 -->
        <li class="page-item" th:classappend="${page.number eq (page.totalPages - 1)} ? 'disabled' : ''">
            <a class="page-link" th:href="${@pagination.link(queryString, page.number + 1, page.size)}" aria-label="Next"><span aria-hidden="true">&raquo;</span></a>
        </li>
        <li class="page-item disabled">
            <a class="page-link">
                <span aria-hidden="true" th:text="|共(${page.totalElements})条|"></span>
            </a>
        </li>
    </ul>
</nav>

</html>

```

> 在模板页面中引用 

```html
<div th:replace="pagination :: pagination"></div>
```