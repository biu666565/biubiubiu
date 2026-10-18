## JDBC 学习成果总结

### 一、JDBC 操作的标准五步流程

```
① 注册驱动 → ② 获取连接 → ③ 获取执行对象 → ④ 执行SQL → ⑤ 释放资源
```

| 步骤 | 关键代码 | 说明 |
|------|----------|------|
| ① | `Class.forName("com.mysql.cj.jdbc.Driver")` | 加载 MySQL 驱动类 |
| ② | `DriverManager.getConnection(url, username, password)` | URL 格式：`jdbc:mysql://主机:端口/库名` |
| ③ | `Statement` 或 `PreparedStatement` | 两种执行方式 |
| ④ | `executeUpdate()` 或 `executeQuery()` | 根据 SQL 类型选择 |
| ⑤ | `close()` 写在 `finally` 里 | Connection → Statement → ResultSet |

---

### 二、两种执行对象对比

| | Statement | PreparedStatement |
|---|-----------|-------------------|
| 创建 | `connection.createStatement()` | `connection.prepareStatement(sql)` |
| SQL 传入时机 | 执行时 `executeXxx(sql)` | 创建时就传入 SQL 模板 |
| 参数设置 | 字符串拼接（有注入风险） | `setXxx(位置, 值)` 安全预编译 |
| 返回值 | 相同 | 相同 |

> **核心区别**：`PreparedStatement` 用 `?` 占位符预编译，防 SQL 注入、性能更高，实际开发优先使用。

---

### 三、两种执行方法的区别

| 方法 | 返回值 | 适用 SQL |
|------|--------|----------|
| `executeUpdate()` | `int` 受影响行数 | INSERT / UPDATE / DELETE（增删改） |
| `executeQuery()` | `ResultSet` 结果集 | SELECT（查询） |

---

### 四、ResultSet 结果集处理

```java
while (resultSet.next()) {           // 光标逐行下移
    resultSet.getInt("id");          // 按列名取值
    resultSet.getString("username");
}
```

---


### 一句话概括

> JDBC 就是用 Java 代码按照 **"注册驱动 → 建连接 → 发 SQL → 拿结果 → 关资源"** 这套标准流程去操作数据库，开发中优先用 `PreparedStatement` 预编译方式。
# 预编译SQL优点
1. 防止SQL注入，参数会被当作纯文本处理，不会被解析成SQL语句。
2. 一次编译、多次执行，数据库可复用执行计划，提升重复查询性能。