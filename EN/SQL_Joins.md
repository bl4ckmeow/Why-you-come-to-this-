# SQL JOINs Guide

## Aliases

### Table and Column Aliases
Aliases provide temporary names for tables or columns in SQL statements

```sql
-- Example of using aliases
SELECT c.name AS cat_name,
       o.name AS owner_name
FROM cat AS c
JOIN owner AS o ON c.owner_id = o.id;
```

**Note**: The `AS` keyword is optional but recommended for better readability

## Types of JOINs

### INNER JOIN
Returns only the matching rows from both tables
```sql
SELECT t.toy_name, c.cat_name,
       o.name AS owner_name
FROM toy t
JOIN cat c ON t.cat_id = c.cat_id
JOIN owner o ON c.owner_id = o.id;
```

### LEFT JOIN
Returns all rows from the left table and matching rows from the right table
```sql
SELECT t.toy_name, c.cat_name,
       o.name AS owner_name
FROM toy t
LEFT JOIN cat c ON t.cat_id = c.cat_id
LEFT JOIN owner o ON c.owner_id = o.id;
```

### SELF JOIN
Joins a table to itself, useful for hierarchical relationships
```sql
SELECT child.cat_name AS child_name,
       mom.cat_name AS mom_name
FROM cat AS child
JOIN cat AS mom ON child.mom_id = mom.cat_id;
```

### NON-EQUI SELF JOIN
Uses conditions other than equality to join tables
```sql
SELECT a.toy_name AS toy_a,
       b.toy_name AS toy_b
FROM toy a
JOIN toy b ON a.cat_id < b.cat_id;
```

## Working with Multiple Tables

### Multiple Joins
You can join more than two tables by chaining the joins
```sql
SELECT t.toy_name, c.cat_name, o.name
FROM toy t
JOIN cat c ON t.cat_id = c.cat_id
JOIN owner o ON c.owner_id = o.id;
```

### JOIN with Multiple Conditions
Use multiple conditions in JOIN using `AND`
```sql
SELECT c.cat_name,
       o.name AS owner_name,
       c.age AS cat_age,
       o.age AS owner_age
FROM cat c
JOIN owner o 
  ON c.owner_id = o.id
  AND c.age < o.age;
```

## Important Considerations

1. **Using Aliases**
   - Once you define a table alias, you must use it throughout the query
   - Choose meaningful and unique alias names

2. **Multiple Table Joins**
   - Be careful with join order as it can affect performance
   - Use indexes on columns used in JOIN conditions

3. **NULL Values**
   - INNER JOIN excludes rows with NULL values in join columns
   - LEFT/RIGHT JOIN preserves NULL values from the respective table

## Best Practices

1. Specify JOIN conditions clearly
2. Use meaningful aliases
3. Be cautious with multiple joins that might create duplicate data
4. Always verify that results include all required data
5. Consider using indexes for better performance 