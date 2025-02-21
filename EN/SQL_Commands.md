# SQL Commands Guide

## Main SQL Structure

SQL is divided into 3 main parts:
1. **DQL (Data Query Language)** - Used for searching and retrieving data
2. **DML (Data Manipulation Language)** - Used for managing data in tables
3. **DDL (Data Definition Language)** - Used for managing database structure

## Basic Commands

### 1. SELECT - Data Retrieval
```sql
SELECT column1, column2 FROM table_name;
SELECT * FROM table_name;  -- Select all columns
```

#### Sorting Data (ORDER BY)
- `ORDER BY column ASC` - Sort ascending
- `ORDER BY column DESC` - Sort descending

#### Filtering Data (WHERE)
- Basic conditions: `=`, `<>`, `<`, `>`, `<=`, `>=`
- `BETWEEN` - Range of values
- `LIKE` - Pattern matching
- `IN` - Value in a set
- `AND`, `OR`, `NOT` - Logical operators

#### Grouping Data (GROUP BY)
```sql
SELECT column, COUNT(*) 
FROM table_name 
GROUP BY column
HAVING count > 1;
```

### 2. DML - Data Manipulation
- `INSERT` - Add data
- `UPDATE` - Modify data
- `DELETE` - Remove data

### 3. DDL - Structure Management
- `CREATE` - Create table/database
- `ALTER` - Modify structure
- `DROP` - Delete table/database

### 4. JOIN - Table Relationships
- `INNER JOIN` - Match records from both tables
- `LEFT JOIN` - All records from left table plus matches from right
- `RIGHT JOIN` - All records from right table plus matches from left
- `FULL JOIN` - All records from both tables

### 5. Common Functions
- Aggregate Functions:
  - `COUNT()` - Count rows
  - `SUM()` - Sum values
  - `AVG()` - Average value
  - `MAX()` - Maximum value
  - `MIN()` - Minimum value

- Window Functions:
  - `ROW_NUMBER()` - Row sequence number
  - `RANK()` - Ranking with gaps
  - `DENSE_RANK()` - Ranking without gaps
  - `LAG()` - Access previous row
  - `LEAD()` - Access next row

## Important Considerations

1. **Using SELECT * **
   - Specify needed columns instead of using *
   - Saves resources and prevents unnecessary data retrieval

2. **Using WHERE**
   - Be careful with conditions that may slow down queries
   - Use indexes on frequently searched columns

3. **Using JOIN**
   - Avoid unintended Cartesian Products
   - Specify join conditions clearly

4. **Using GROUP BY**
   - Include all non-aggregate columns from SELECT
   - Use HAVING for post-grouping conditions

5. **Using Transactions**
   - Use BEGIN TRANSACTION for data consistency
   - Don't forget to COMMIT or ROLLBACK

6. **Performance**
   - Use indexes appropriately
   - Avoid complex subqueries
   - Be careful with temporary tables or views that may slow queries

## SQL Best Practices

1. Use meaningful names
2. Format code for readability
3. Add comments for complex queries
4. Test queries with sample data
5. Check execution plans for optimization 