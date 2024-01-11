---
title: Spring MVC 重定向跳转空白
categories:
  - Software
  - Java
  - Spring boot
abbrlink: 4c76964e
---
# Spring MVC 重定向跳转空白

```java
public ModelAndView del(@PathVariable Long id) {
    ModelAndView modelAndView = new ModelAndView("redirect:/t/index?sort=sort");
    modelAndView.setStatus(HttpStatus.MOVED_PERMANENTLY);
    if (id == 0L) {
        return modelAndView;
    }
    templateRepository.deleteById(id);
    return modelAndView;
}
```

注意返回码：301，返回200就空白