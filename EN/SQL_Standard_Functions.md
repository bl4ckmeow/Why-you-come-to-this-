# SQL Standard Functions Guide

## 1. Text Functions

### Concatenation
```sql
-- Use || to concatenate strings
SELECT 'Hi ' || 'there!';  -- result: Hi there!

-- Works with numbers too
SELECT '4' || '2';  -- result: 42
```

### Pattern Matching
```sql
-- Use _ for single character
-- Use % for any number of characters
SELECT name FROM names WHERE name LIKE '_atherine';
SELECT name FROM names WHERE name LIKE '%a';
```

### String Functions
```sql
-- Get string length
SELECT LENGTH('LearnSQL.com');  -- result: 12

-- Convert to lowercase
SELECT LOWER('LEARNSQL.COM');  -- result: learnsql.com

-- Convert to uppercase
SELECT UPPER('learnsql.com');  -- result: LEARNSQL.COM

-- Extract substring
SELECT SUBSTRING('LearnSQL.com', 0, 6);  -- result: Learn

-- Replace text
SELECT REPLACE('LearnSQL.com', 'SQL', 'Python');
-- result: LearnPython.com
```

## 2. Numeric Functions

### Basic Operations
```sql
-- Use +, -, *, / for basic math
SELECT 60 * 60 * 24 * 7;  -- seconds in a week
```

### Type Casting
```sql
-- Cast to integer
SELECT CAST(1234.567 AS integer);  -- result: 1234

-- Cast column to double precision
SELECT CAST(column AS double precision);
```

### Mathematical Functions
```sql
-- Get remainder
SELECT MOD(13, 2);  -- result: 1

-- Round numbers
SELECT ROUND(1234.5678);  -- result: 1235
SELECT ROUND(1234.5678, 3);  -- result: 1234.568

-- Ceiling and floor
SELECT CEIL(-13.9);  -- result: -13
SELECT FLOOR(-13.2);  -- result: -14

-- Absolute value
SELECT ABS(-12);  -- result: 12

-- Square root
SELECT SQRT(9);  -- result: 3
```

## 3. NULL Handling

### NULL Checks
```sql
-- Find rows with NULL price
WHERE price IS NULL

-- Find rows with NULL weight
WHERE weight IS NULL
```

### COALESCE Function
```sql
-- Replace NULL with default value
SELECT COALESCE(domain, 'domain missing')
FROM contacts;
```

### NULLIF Function
```sql
-- Return NULL if values are equal
SELECT NULLIF(last_month, 0)
FROM video_plays;
```

## 4. CASE WHEN

### Basic Usage
```sql
SELECT 
    CASE fee
        WHEN 50 THEN 'normal'
        WHEN 30 THEN 'reduced'
        WHEN 0 THEN 'free'
        ELSE 'not available'
    END AS tariff
FROM ticket_types;
```

### Complex Conditions
```sql
SELECT
    CASE
        WHEN score >= 90 THEN 'A'
        WHEN score > 60 THEN 'B'
        ELSE 'F'
    END AS grade
FROM test_results;
```

## 5. Date and Time

### Date Format
```sql
-- Basic format: YYYY-MM-DD HH:MM:SS.ssssss+TZ
2021-12-31 14:39:53.662522-05

-- Components:
-- YYYY = four-digit year
-- MM = two-digit month (01-12)
-- DD = two-digit day
-- HH = hours (00-23)
-- MM = minutes
-- SS = seconds
-- ssssss = microseconds
-- TZ = timezone
```

### Date Functions
```sql
-- Get current date
SELECT CURRENT_DATE;

-- Get current time
SELECT CURRENT_TIME;

-- Get current timestamp
SELECT CURRENT_TIMESTAMP;

-- Calculate time difference
SELECT CAST('2021-12-31 23:59:59' AS timestamp) 
     - CAST('2021-06-01 12:00:00' AS timestamp) 
AS time_diff;

-- Add/subtract intervals
SELECT event_date + INTERVAL '3' MONTH
FROM calendar
WHERE event_date BETWEEN CURRENT_DATE AND
      CURRENT_DATE + INTERVAL '3' MONTH;
```

## Important Considerations

1. **Division by Zero**
   - Use NULLIF to prevent division by zero
   - Example: `count / NULLIF(count_all, 0)`

2. **Calculation Precision**
   - Use DECIMAL for precise calculations
   - Be careful with FLOAT/REAL for potential inaccuracies

3. **Time Zones**
   - Always specify time zones when working with timestamps
   - Use AT TIME ZONE for timezone conversions

4. **Type Casting**
   - Check database documentation for supported formats
   - Specify precision when casting to numeric types
``` 