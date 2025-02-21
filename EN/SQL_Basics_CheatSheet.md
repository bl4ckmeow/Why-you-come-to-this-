# SQL Basics Cheat Sheet

## Sample Data Structure

### COUNTRY Table
```sql
CREATE TABLE country (
    id INT PRIMARY KEY,
    name VARCHAR(100),
    population INT,
    area INT
);
```

### CITY Table
```sql
CREATE TABLE city (
    id INT PRIMARY KEY,
    name VARCHAR(100),
    country_id INT,
    population INT,
    rating INT,
    FOREIGN KEY (country_id) REFERENCES country(id)
);
```

## Single Table Queries

### Basic Data Retrieval
```sql
-- Get all columns from country table
SELECT * FROM country;

-- Get specific columns from city table
SELECT id, name FROM city;
```

### Sorting Data
```sql
-- Sort by rating in ascending order
SELECT name FROM city ORDER BY rating ASC;

-- Sort by rating in descending order
SELECT name FROM city ORDER BY rating DESC;
```

## Filtering Data

### Comparison Operators
```sql
-- Find cities with rating above 3
SELECT name FROM city WHERE rating > 3;

-- Find cities that are neither Berlin nor Madrid
SELECT name FROM city 
WHERE name != 'Berlin' AND name != 'Madrid';
```

### Pattern Matching
```sql
-- Find cities that start with 'P' or end with 's'
SELECT name FROM city 
WHERE name LIKE 'P%' OR name LIKE '%s';

-- Find cities containing 'ublin' (like Dublin or Lublin)
SELECT name FROM city 
WHERE name LIKE '_ublin';
```

### Special Operators
```sql
-- Find cities with population between 500,000 and 5,000,000
SELECT name FROM city 
WHERE population BETWEEN 500000 AND 5000000;

-- Find cities with non-null rating
SELECT name FROM city 
WHERE rating IS NOT NULL;

-- Find cities in countries with IDs 1, 4, 7, or 8
SELECT name FROM city 
WHERE country_id IN (1, 4, 7, 8);
```

## JOIN Operations

### INNER JOIN
```sql
-- Match cities with their countries
SELECT city.name, country.name
FROM city
INNER JOIN country ON city.country_id = country.id;
```

### LEFT JOIN
```sql
-- Get all cities with matching countries if available
SELECT city.name, country.name
FROM city
LEFT JOIN country ON city.country_id = country.id;
```

### RIGHT JOIN
```sql
-- Get all countries with matching cities if available
SELECT city.name, country.name
FROM city
RIGHT JOIN country ON city.country_id = country.id;
```

### FULL JOIN
```sql
-- Get all records from both tables
SELECT city.name, country.name
FROM city
FULL JOIN country ON city.country_id = country.id;
```

### CROSS JOIN
```sql
-- Create all possible combinations between tables
SELECT city.name, country.name
FROM city
CROSS JOIN country;
```

## Aliases

### Column Aliases
```sql
SELECT name AS city_name FROM city;
```

### Table Aliases
```sql
SELECT c1.name, c2.name
FROM city AS c1
JOIN country AS c2 ON c1.country_id = c2.id;
```

## Important Considerations

1. **Using CROSS JOIN**
   - Be cautious of Cartesian Products that can generate large result sets
   - Use only when necessary

2. **Working with NULL**
   - Use IS NULL or IS NOT NULL instead of = NULL or != NULL
   - NULL is not equal to empty string or 0

3. **Using LIKE**
   - Be careful with wildcards (%) as they can slow down queries
   - Consider using indexes for frequently searched patterns

4. **Using JOINs**
   - Choose the appropriate JOIN type for your needs
   - Be aware of data loss possibility in INNER JOIN

## SQL Best Practices

1. Use meaningful names
2. Write clear and correct JOIN conditions
3. Avoid SELECT * in production
4. Use BETWEEN instead of multiple conditions
5. Test queries with diverse data sets 

## Aggregation and Functions

### Aggregate Functions
- `avg(expr)` - average value for rows within the group
- `count(expr)` - count of values for rows within the group
- `max(expr)` - maximum value within the group
- `min(expr)` - minimum value within the group
- `sum(expr)` - sum of values within the group

### Example Queries
```sql
-- Count total number of cities
SELECT COUNT(*) FROM city;

-- Count cities with non-null ratings
SELECT COUNT(rating) FROM city;

-- Count distinct countries
SELECT COUNT(DISTINCT country_id) FROM city;

-- Find smallest and largest country populations
SELECT MIN(population), MAX(population)
FROM country;

-- Sum population by country
SELECT country_id, SUM(population)
FROM city
GROUP BY country_id;

-- Average rating by country where average is above 3.0
SELECT country_id, AVG(rating)
FROM city
GROUP BY country_id
HAVING AVG(rating) > 3.0;
```

## Subqueries

### Single Value Subquery
```sql
-- Find cities with the same rating as Paris
SELECT name FROM city
WHERE rating = (
    SELECT rating
    FROM city
    WHERE name = 'Paris'
);
```

### Multiple Values Subquery
```sql
-- Find cities in countries with population over 20 million
SELECT name
FROM city
WHERE country_id IN (
    SELECT country_id
    FROM country
    WHERE population > 20000000
);
```

### Correlated Subquery
```sql
-- Find cities with population greater than their country's average
SELECT *
FROM city main_city
WHERE population > (
    SELECT AVG(population)
    FROM city average_city
    WHERE average_city.country_id = main_city.country_id
);

-- Find countries that have at least one city
SELECT name
FROM country
WHERE EXISTS (
    SELECT *
    FROM city
    WHERE city.country_id = country.id
);
```

## Set Operations

### UNION
```sql
-- Combine German cyclists and skaters (no duplicates)
SELECT name
FROM cycling
WHERE country = 'DE'
UNION
SELECT name
FROM skating
WHERE country = 'DE';

-- Combine all records (including duplicates)
SELECT name
FROM cycling
WHERE country = 'DE'
UNION ALL
SELECT name
FROM skating
WHERE country = 'DE';
```

### INTERSECT
```sql
-- Find German athletes who both cycle and skate
SELECT name
FROM cycling
WHERE country = 'DE'
INTERSECT
SELECT name
FROM skating
WHERE country = 'DE';
```

### EXCEPT
```sql
-- Find German cyclists who don't skate
SELECT name
FROM cycling
WHERE country = 'DE'
EXCEPT
SELECT name
FROM skating
WHERE country = 'DE';
``` 