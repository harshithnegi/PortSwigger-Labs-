# Lab 1 — SQL Injection Vulnerability in WHERE Clause

> **Platform:** PortSwigger Web Security Academy
> **Category:** SQL Injection
> **Lab:** SQL injection vulnerability in WHERE clause allowing retrieval of hidden data
> **Status:** ✅ Solved

---

## Objective

The objective of this lab is to perform a **SQL Injection** attack that causes the application to display one or more **unreleased products**.

---

## 1. Understanding the Application

The application contains products divided into different categories.

When a category is selected, the application uses the selected category as a parameter in the URL.

![Initial application](./images/image.png)

---

## 2. Identifying the SQL Injection Point

For example, when selecting the **Tech Gifts** category, the application generates a request containing the category parameter.

![Tech Gifts category](./images/image%202.png)

The category parameter is user-controlled and is therefore a potential location for testing SQL Injection.

---

## 3. Understanding the SQL Query

The application is using a SQL query similar to:

```sql
SELECT *
FROM products
WHERE category = 'Tech Gifts'
AND released = 1;
```

The important part of the query is:

```sql
WHERE category = 'Tech Gifts'
AND released = 1
```

The condition:

```sql
released = 1
```

ensures that only products marked as released are returned.

The objective is to manipulate this query so that the application also returns products that have not been released.

---

## 4. SQL Injection Payload

The following payload can be supplied through the `category` parameter:

```text
' OR 1=1--
```

The payload changes the logic of the application's SQL query.

### Payload Breakdown

#### `'` — Close the Existing String

The single quote terminates the original string value:

```sql
category = 'Tech Gifts'
```

This allows additional SQL syntax to be introduced into the query.

#### `OR` — Add an Alternative Condition

The `OR` operator allows another condition to be added to the query.

```sql
OR 1=1
```

#### `1=1` — Always True

The condition:

```sql
1=1
```

is always true.

Therefore, the injected condition evaluates to:

```text
TRUE
```

#### `--` — Comment Out the Remaining Query

The `--` sequence starts a SQL comment in the relevant SQL syntax.

This causes the remaining portion of the original query to be ignored.

This is important because it can remove the effect of the original:

```sql
AND released = 1
```

condition.

---

## 5. Query Manipulation

### Original Query

```sql
SELECT *
FROM products
WHERE category = 'Tech Gifts'
AND released = 1;
```

### Injected Input

```text
' OR 1=1--
```

The resulting query is effectively manipulated so that the attacker-controlled condition evaluates to true while the remaining part of the query is commented out.

Conceptually, the important transformation is:

```sql
WHERE category = 'Tech Gifts'
AND released = 1
```

into logic containing:

```sql
OR 1=1
```

Because `1=1` is always true, the application can return products that would normally be excluded by the original filtering conditions.

---

## 6. Exploitation

The payload is inserted into the `category` parameter of the URL:

```text
' OR 1=1--
```

The application processes the manipulated input as part of its SQL query.

The injected condition evaluates to true, while the remainder of the original query is commented out.

As a result, the application's original restriction on released products is bypassed.

---

## 7. Result

After submitting the SQL Injection payload, the application displays products that were previously hidden.

![Successful SQL Injection](./images/image%203.png)

The application now displays both **released and unreleased products**, demonstrating that the SQL Injection attack was successful.

---

## 8. Why the Vulnerability Exists

The vulnerability exists because user-controlled input is being incorporated directly into a SQL query without being safely separated from the SQL syntax.

Instead of treating the category value strictly as data, the application allows the supplied input to influence the structure and logic of the SQL statement.

This enables an attacker to inject SQL syntax and modify the intended query.

---

## 9. Key Takeaways

* SQL Injection occurs when untrusted user input can influence the structure of a SQL query.
* URL parameters should always be considered potentially attacker-controlled input.
* Boolean conditions such as `1=1` can be used to manipulate SQL query logic.
* SQL comments can be used to neutralize the remainder of a vulnerable query.
* SQL Injection can allow attackers to access data that the application intended to hide.
* Applications should use **parameterized queries / prepared statements** instead of directly concatenating user input into SQL queries.

---

## 10. Lab Summary

| Item            | Details                                 |
| --------------- | --------------------------------------- |
| Vulnerability   | SQL Injection                           |
| Injection Point | `category` parameter                    |
| Attack Type     | Boolean-based SQL Injection             |
| Payload         | `' OR 1=1--`                            |
| Impact          | Retrieval of hidden/unreleased products |
| Result          | ✅ Lab Solved                            |

---

## 11. References

* [PortSwigger Web Security Academy — SQL Injection](https://portswigger.net/web-security/sql-injection)

---

**Lab Status: Solved ✅**
