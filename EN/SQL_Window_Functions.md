# SQL Window Functions Guide

## Aggregate Functions vs. Window Functions

- **Aggregate Functions** collapse multiple rows into a single value
- **Window Functions** compute results based on a set of rows related to the current row

## Basic Syntax
```sql
SELECT column1, column2,
       window_function() OVER (
           PARTITION BY column_x
           ORDER BY column_y
           frame_clause
       ) AS window_result
FROM table_name;
```

## Window Frame

A window frame defines a set of rows related to the current row. The frame is evaluated separately within each partition.

### Frame Options
- `UNBOUNDED PRECEDING` - from first row of partition
- `n PRECEDING` - n rows before current row
- `CURRENT ROW` - current row
- `n FOLLOWING` - n rows after current row
- `UNBOUNDED FOLLOWING` - to last row of partition

### Frame Types
```sql
-- Examples of frame specification
ROWS BETWEEN n PRECEDING AND CURRENT ROW
RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
GROUPS BETWEEN 1 PRECEDING AND 1 FOLLOWING
```

## Partitioning and Ordering

### PARTITION BY
Divides rows into groups (partitions) for window function calculation
```sql
-- Example of PARTITION BY usage
SELECT city, month, sold,
       SUM(sold) OVER (PARTITION BY city) as city_total
FROM sales;
```

### ORDER BY
Specifies the order of rows within each partition for calculation
```sql
-- Example of ORDER BY usage
SELECT city, month, sold,
       ROW_NUMBER() OVER (PARTITION BY city ORDER BY sold DESC) as rank
FROM sales;
```

## Window Functions Examples

### Ranking Functions
```sql
-- Different types of ranking
SELECT city, sold,
       ROW_NUMBER() OVER (ORDER BY sold DESC) as continuous_rank,
       RANK() OVER (ORDER BY sold DESC) as rank_with_gaps,
       DENSE_RANK() OVER (ORDER BY sold DESC) as rank_without_gaps
FROM sales;
```

### Row Comparison Functions
```sql
-- Access previous and next row values
SELECT city, month, sold,
       LAG(sold) OVER (ORDER BY month) as prev_month_sales,
       LEAD(sold) OVER (ORDER BY month) as next_month_sales
FROM sales;
```

### Running Totals
```sql
-- Calculate running sum by month
SELECT month, sold,
       SUM(sold) OVER (ORDER BY month) as running_total
FROM sales;
```

## Important Considerations

1. **Order of Operations**
   - Window functions are processed after WHERE, GROUP BY, HAVING
   - But before ORDER BY and LIMIT

2. **Performance**
   - PARTITION BY on large datasets can impact performance
   - Use appropriate indexes on PARTITION BY and ORDER BY columns

3. **Frame Specification**
   - Default frame depends on presence of ORDER BY
   - With ORDER BY: RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
   - Without ORDER BY: RANGE BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING

## Usage Restrictions

1. Window functions can only be used in:
   - SELECT clause
   - ORDER BY clause
   
2. Cannot be used in:
   - WHERE clause
   - GROUP BY clause
   - HAVING clause

3. GROUPS frame specification is only supported in PostgreSQL 11 and up

## Common Abbreviations

- `n PRECEDING` = between n rows before and current row
- `CURRENT ROW` = current row only
- `n FOLLOWING` = between current row and n rows after
- `UNBOUNDED PRECEDING` = from first row to current row
- `UNBOUNDED FOLLOWING` = from current row to last row 