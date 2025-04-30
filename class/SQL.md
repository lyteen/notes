##  SQL

### **关键词:** 
相关子查询  不相关子查询

### **摘要:** 


### **情形**

1.  **相关子查询** 查询中使用外部查询的字段

- 方法： ..

```sql
SELECT employee_id, name
FROM employees e
WHERE salary > (
    SELECT AVG(salary)
    FROM employees
    WHERE department_id = e.department_id
);
```
- 在 WHERE department_id = e.department_id 中子查询使用了外包查询字段 "e"

<details>
    <summary>补充</summary>
        <ul>
	      <li><strong>FROM employees e</strong>： e 为employees 的一个别名 (alias)</li>
        </ul>
</details>


2. **不相关子查询** 子查询完全独立于外部查询，不使用外部查询的任何字段。

```sql
SELECT employee_id, name
FROM employees
WHERE salary > (
    SELECT AVG(salary)
    FROM employees
);
```
子查询计算整个表的平均薪水，并且它不依赖于外部查询的任何字段，因此它只执行一次。

<details>
    <summary>= 和 IN</summary>
        <ul>
	      <li><strong>=</strong>： 比较某个字段是否等于一个单一的值</li>
        <li><strong>IN</strong>： 判断某个字段是否在指定的一组值中
        </ul>
</details>

- "="
```sql
SELECT * FROM employees
WHERE department_id = 5;
```

- "IN"
```sql
SELECT * FROM employees
WHERE department_id IN (1, 2, 3);

# same to "="
SELECT * FROM employees
WHERE department_id = 1 OR department_id = 2 OR department_id = 3;
```
这个查询会返回所有 department_id 在 1、2 或 3 之间的员工记录