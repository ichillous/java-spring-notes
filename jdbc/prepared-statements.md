# JDBC: Prepared Statements

## Table of Contents
1. [Introduction to Prepared Statements](#introduction-to-prepared-statements)
2. [Why Use Prepared Statements](#why-use-prepared-statements)
3. [How Prepared Statements Work](#how-prepared-statements-work)
4. [Creating and Using Prepared Statements](#creating-and-using-prepared-statements)
5. [Parameter Setting Methods](#parameter-setting-methods)
6. [Executing Prepared Statements](#executing-prepared-statements)
7. [Prepared Statements vs. Statement](#prepared-statements-vs-statement)
8. [Preventing SQL Injection](#preventing-sql-injection)
9. [Performance Benefits](#performance-benefits)
10. [Best Practices](#best-practices)
11. [Common Mistakes and Pitfalls](#common-mistakes-and-pitfalls)
12. [Advanced Uses of Prepared Statements](#advanced-uses-of-prepared-statements)

## Introduction to Prepared Statements

Prepared Statements are a feature in JDBC that allows you to create precompiled SQL statements which can be executed multiple times with different parameters. They are represented by the `PreparedStatement` interface, which extends the `Statement` interface.

## Why Use Prepared Statements

1. **Security**: Prepared Statements help prevent SQL injection attacks.
2. **Performance**: They can improve performance for statements executed multiple times.
3. **Convenience**: They simplify the process of setting parameters in SQL statements.
4. **Type Safety**: They provide methods for setting parameters that ensure type safety.

## How Prepared Statements Work

1. An SQL statement template is created with placeholder parameters (usually represented by `?`).
2. The statement is sent to the database to be parsed, compiled, and optimized.
3. The database stores the execution plan for the statement.
4. When the statement is executed, only the parameters are sent to the database.

## Creating and Using Prepared Statements

Here's a basic example of creating and using a Prepared Statement:

```java
String sql = "INSERT INTO users (name, email) VALUES (?, ?)";

try (Connection conn = DriverManager.getConnection(url, user, password);
     PreparedStatement pstmt = conn.prepareStatement(sql)) {

    pstmt.setString(1, "John Doe");
    pstmt.setString(2, "john@example.com");
    
    int rowsAffected = pstmt.executeUpdate();
    System.out.println(rowsAffected + " row(s) inserted.");

} catch (SQLException e) {
    e.printStackTrace();
}
```

## Parameter Setting Methods

PreparedStatement provides various methods to set parameters:

- `setString(int parameterIndex, String x)`
- `setInt(int parameterIndex, int x)`
- `setLong(int parameterIndex, long x)`
- `setDouble(int parameterIndex, double x)`
- `setDate(int parameterIndex, Date x)`
- `setTimestamp(int parameterIndex, Timestamp x)`
- `setObject(int parameterIndex, Object x)`
- `setNull(int parameterIndex, int sqlType)`

Example:

```java
String sql = "SELECT * FROM products WHERE category = ? AND price < ?";

try (Connection conn = DriverManager.getConnection(url, user, password);
     PreparedStatement pstmt = conn.prepareStatement(sql)) {

    pstmt.setString(1, "Electronics");
    pstmt.setDouble(2, 500.00);

    try (ResultSet rs = pstmt.executeQuery()) {
        while (rs.next()) {
            // Process results
        }
    }

} catch (SQLException e) {
    e.printStackTrace();
}
```

## Executing Prepared Statements

Prepared Statements can be executed using different methods depending on the type of SQL statement:

- `executeQuery()`: For SELECT statements, returns a ResultSet.
- `executeUpdate()`: For INSERT, UPDATE, DELETE statements, returns the number of affected rows.
- `execute()`: For any SQL statement, returns a boolean indicating whether the result is a ResultSet.

## Prepared Statements vs. Statement

Prepared Statements offer several advantages over regular Statements:

1. **SQL Injection Prevention**: Parameters are treated as literal values, not part of the SQL.
2. **Performance**: Execution plan can be reused, beneficial for repeated executions.
3. **Readability**: SQL logic is separated from the data.
4. **Type Safety**: Parameter setting methods ensure correct data types.

Example comparison:

```java
// Using Statement (vulnerable to SQL injection)
String name = "O'Brien"; // This could break the query
String sql = "SELECT * FROM users WHERE name = '" + name + "'";
Statement stmt = conn.createStatement();
ResultSet rs = stmt.executeQuery(sql);

// Using PreparedStatement (safe and efficient)
String sql = "SELECT * FROM users WHERE name = ?";
PreparedStatement pstmt = conn.prepareStatement(sql);
pstmt.setString(1, name);
ResultSet rs = pstmt.executeQuery();
```

## Preventing SQL Injection

SQL Injection is a technique where malicious SQL statements are inserted into application queries. Prepared Statements prevent this by treating parameters as literal values:

```java
// Vulnerable to SQL injection
String username = "admin' --";
String password = "anything";
String sql = "SELECT * FROM users WHERE username = '" + username + "' AND password = '" + password + "'";

// Safe from SQL injection
String sql = "SELECT * FROM users WHERE username = ? AND password = ?";
PreparedStatement pstmt = conn.prepareStatement(sql);
pstmt.setString(1, username);
pstmt.setString(2, password);
```

## Performance Benefits

Prepared Statements can offer performance benefits, especially for statements executed multiple times:

1. **Precompilation**: The SQL is parsed and compiled only once.
2. **Execution Plan Caching**: The database can reuse the execution plan.
3. **Network Traffic Reduction**: Only parameter values are sent after the first execution.

Example of reusing a Prepared Statement:

```java
String sql = "INSERT INTO logs (message, timestamp) VALUES (?, ?)";

try (Connection conn = DriverManager.getConnection(url, user, password);
     PreparedStatement pstmt = conn.prepareStatement(sql)) {

    for (LogEntry log : logEntries) {
        pstmt.setString(1, log.getMessage());
        pstmt.setTimestamp(2, log.getTimestamp());
        pstmt.executeUpdate();
    }

} catch (SQLException e) {
    e.printStackTrace();
}
```

## Best Practices

1. **Always Use Prepared Statements for Dynamic SQL**: Especially when incorporating user input.
2. **Reuse Prepared Statements**: Create once and reuse for multiple executions.
3. **Use Parameter Markers Appropriately**: Don't use them for static parts of the query.
4. **Close Resources**: Use try-with-resources or explicitly close PreparedStatements.
5. **Use Batch Processing**: For multiple similar operations, use `addBatch()` and `executeBatch()`.
6. **Set Appropriate Fetch Size**: For large result sets, set an appropriate fetch size.

## Common Mistakes and Pitfalls

1. **Concatenating Strings**: Avoid concatenating strings to build SQL queries.
2. **Reusing Parameter Indices**: Be cautious when reusing a PreparedStatement with different parameters.
3. **Ignoring SQLExceptions**: Always handle or at least log SQLExceptions.
4. **Not Clearing Parameters**: Use `clearParameters()` when reusing a PreparedStatement with different values.
5. **Using PreparedStatement for Static SQL**: For completely static SQL, a regular Statement might be more efficient.

## Advanced Uses of Prepared Statements

1. **Batch Processing**:

```java
String sql = "INSERT INTO users (name, email) VALUES (?, ?)";

try (Connection conn = DriverManager.getConnection(url, user, password);
     PreparedStatement pstmt = conn.prepareStatement(sql)) {

    conn.setAutoCommit(false);

    for (User user : users) {
        pstmt.setString(1, user.getName());
        pstmt.setString(2, user.getEmail());
        pstmt.addBatch();
    }

    int[] results = pstmt.executeBatch();
    conn.commit();

} catch (SQLException e) {
    e.printStackTrace();
}
```

2. **Retrieving Generated Keys**:

```java
String sql = "INSERT INTO users (name, email) VALUES (?, ?)";

try (Connection conn = DriverManager.getConnection(url, user, password);
     PreparedStatement pstmt = conn.prepareStatement(sql, Statement.RETURN_GENERATED_KEYS)) {

    pstmt.setString(1, "Jane Doe");
    pstmt.setString(2, "jane@example.com");
    pstmt.executeUpdate();

    try (ResultSet generatedKeys = pstmt.getGeneratedKeys()) {
        if (generatedKeys.next()) {
            long id = generatedKeys.getLong(1);
            System.out.println("Generated ID: " + id);
        }
    }

} catch (SQLException e) {
    e.printStackTrace();
}
```

3. **Callable Statements for Stored Procedures**:

```java
String sql = "{call get_user_details(?, ?)}";

try (Connection conn = DriverManager.getConnection(url, user, password);
     CallableStatement cstmt = conn.prepareCall(sql)) {

    cstmt.setLong(1, userId);
    cstmt.registerOutParameter(2, java.sql.Types.VARCHAR);
    cstmt.execute();

    String userName = cstmt.getString(2);
    System.out.println("User Name: " + userName);

} catch (SQLException e) {
    e.printStackTrace();
}
```

Remember, while Prepared Statements offer many advantages, they're not a silver bullet. Always consider the specific requirements of your application and use the appropriate JDBC features accordingly.
