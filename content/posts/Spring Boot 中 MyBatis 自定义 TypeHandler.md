+++
title= "Spring Boot 中 MyBatis 自定义 TypeHandler"
draft = false
date= "2019-02-20 17:15:55"
+++

有了 Spring-Boot 全家桶之后，很多配置都不用写 xml 了，MyBatis 我也比较喜欢通过纯注解方式实现所有功能，感觉方便很多，所以这篇文章里不涉及任何的 xml 配置。

### 应用配置中增加 type-handlers-package 配置项

mybatis.type-handlers-package=com.xxx.handlers

### 写具体的实现类

自定义的 TypeHandler 类可以继承 `BaseTypeHandler`，或者实现 `TypeHandler`。

可以通过 `MappedJdbcTypes` 注解设置映射的 Jdbc 数据类型，通过 `MappedTypes` 注解设置映射到的 Java 数据类型。

由于 MyBatis 默认的 TypeHandler 在把数据库中的 timestamp 类型映射到 String 的时候会带上毫秒，而我在项目中需要的格式不需要毫秒，所以我自己写了一个 TypeHandler。完整的实例如下：

```java
package com.xxx.type;

import lombok.extern.slf4j.Slf4j;
import org.apache.commons.lang3.StringUtils;
import org.apache.ibatis.type.JdbcType;
import org.apache.ibatis.type.MappedJdbcTypes;
import org.apache.ibatis.type.MappedTypes;
import org.apache.ibatis.type.TypeHandler;

import java.sql.CallableStatement;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

/**
 * @author zhangzhoufei
 */
@MappedJdbcTypes(JdbcType.TIMESTAMP)
@MappedTypes(String.class)
@Slf4j
public class TimeStringTypeHandler implements TypeHandler {

    @Override
    public void setParameter(PreparedStatement preparedStatement, int i, Object o, JdbcType jdbcType) throws SQLException {
        String value = (String) o;
        preparedStatement.setString(i, value);
    }

    @Override
    public Object getResult(ResultSet resultSet, String s) throws SQLException {
        return this.formatTime(resultSet.getString(s));
    }

    @Override
    public Object getResult(ResultSet resultSet, int i) throws SQLException {
        return this.formatTime(resultSet.getString(i));
    }

    @Override
    public Object getResult(CallableStatement callableStatement, int i) throws SQLException {
        return this.formatTime(callableStatement.getString(i));
    }

    private String formatTime(String timeStr) {
        if (StringUtils.isEmpty(timeStr)) {
            return timeStr;
        }

        // 2019-02-20 10:00:00
        try {
            return timeStr.substring(0, 19);
        } catch (Exception e) {
            e.printStackTrace();
        }

        return timeStr;
    }
}

```

参考文档中的实例大多是枚举值的映射，在实际项目中也很常用。

### 配置 Mapper 类结果集映射中使用的 TypeHandler

```java
@Results(id = "entity", value = {
        @Result(column = "created_at", property = "createdAt", typeHandler = TimeStringTypeHandler.class),
})
```

### 参考文档

- [mybatis-自定义TypeHandler](https://www.jianshu.com/p/93de918655eb)
- [Mybatis TypeHandler](https://zhuanlan.zhihu.com/p/43165846)
- [浅析 mybatis 中 TypeHandler 类型转换器 + 自定义 TypeHandler](https://juejin.im/entry/59225b9f8d6d810058e39536)