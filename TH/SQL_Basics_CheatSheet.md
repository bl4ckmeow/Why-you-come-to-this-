# คู่มือพื้นฐาน SQL (SQL Basics Cheat Sheet)

## โครงสร้างข้อมูลตัวอย่าง

### ตาราง COUNTRY
```sql
CREATE TABLE country (
    id INT PRIMARY KEY,
    name VARCHAR(100),
    population INT,
    area INT
);
```

### ตาราง CITY
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

## การค้นหาข้อมูลจากตารางเดียว (Single Table Queries)

### การดึงข้อมูลพื้นฐาน
```sql
-- ดึงทุกคอลัมน์จากตาราง country
SELECT * FROM country;

-- ดึงเฉพาะคอลัมน์ id และ name จากตาราง city
SELECT id, name FROM city;
```

### การเรียงลำดับข้อมูล
```sql
-- เรียงตาม rating จากน้อยไปมาก
SELECT name FROM city ORDER BY rating ASC;

-- เรียงตาม rating จากมากไปน้อย
SELECT name FROM city ORDER BY rating DESC;
```

## การกรองข้อมูล (Filtering)

### ตัวดำเนินการเปรียบเทียบ
```sql
-- หาเมืองที่มี rating มากกว่า 3
SELECT name FROM city WHERE rating > 3;

-- หาเมืองที่ไม่ใช่ Berlin และ Madrid
SELECT name FROM city 
WHERE name != 'Berlin' AND name != 'Madrid';
```

### การค้นหาด้วย Pattern
```sql
-- หาเมืองที่ขึ้นต้นด้วย 'P' หรือลงท้ายด้วย 's'
SELECT name FROM city 
WHERE name LIKE 'P%' OR name LIKE '%s';

-- หาเมืองที่มีคำว่า 'ublin' (เช่น Dublin หรือ Lublin)
SELECT name FROM city 
WHERE name LIKE '_ublin';
```

### ตัวดำเนินการพิเศษ
```sql
-- หาเมืองที่มีประชากรระหว่าง 500,000 ถึง 5,000,000
SELECT name FROM city 
WHERE population BETWEEN 500000 AND 5000000;

-- หาเมืองที่มีค่า rating ไม่เป็น NULL
SELECT name FROM city 
WHERE rating IS NOT NULL;

-- หาเมืองที่อยู่ในประเทศรหัส 1, 4, 7 หรือ 8
SELECT name FROM city 
WHERE country_id IN (1, 4, 7, 8);
```

## การเชื่อมตาราง (JOIN Operations)

### INNER JOIN
```sql
-- เชื่อมข้อมูลเมืองกับประเทศที่มีข้อมูลตรงกัน
SELECT city.name, country.name
FROM city
INNER JOIN country ON city.country_id = country.id;
```

### LEFT JOIN
```sql
-- เชื่อมข้อมูลโดยเอาข้อมูลเมืองทั้งหมด แม้ไม่มีข้อมูลประเทศ
SELECT city.name, country.name
FROM city
LEFT JOIN country ON city.country_id = country.id;
```

### RIGHT JOIN
```sql
-- เชื่อมข้อมูลโดยเอาข้อมูลประเทศทั้งหมด แม้ไม่มีข้อมูลเมือง
SELECT city.name, country.name
FROM city
RIGHT JOIN country ON city.country_id = country.id;
```

### FULL JOIN
```sql
-- เชื่อมข้อมูลโดยเอาข้อมูลทั้งหมดจากทั้งสองตาราง
SELECT city.name, country.name
FROM city
FULL JOIN country ON city.country_id = country.id;
```

### CROSS JOIN
```sql
-- สร้างการจับคู่ทุกความเป็นไปได้ระหว่างสองตาราง
SELECT city.name, country.name
FROM city
CROSS JOIN country;
```

## การใช้นามแฝง (Aliases)

### การตั้งชื่อคอลัมน์
```sql
SELECT name AS city_name FROM city;
```

### การตั้งชื่อตาราง
```sql
SELECT c1.name, c2.name
FROM city AS c1
JOIN country AS c2 ON c1.country_id = c2.id;
```

## ข้อควรระวัง

1. **การใช้ CROSS JOIN**
   - ระวังการเกิด Cartesian Product ที่อาจสร้างข้อมูลจำนวนมาก
   - ใช้เมื่อจำเป็นเท่านั้น

2. **การใช้ NULL**
   - ใช้ IS NULL หรือ IS NOT NULL แทน = NULL หรือ != NULL
   - NULL ไม่เท่ากับค่าว่างหรือ 0

3. **การใช้ LIKE**
   - ระวังการใช้ wildcard (%) ที่อาจทำให้ query ช้า
   - ควรใช้ index ถ้าต้องค้นหาบ่อย

4. **การใช้ JOIN**
   - เลือกใช้ประเภท JOIN ให้เหมาะกับความต้องการ
   - ระวังการสูญหายของข้อมูลใน INNER JOIN

## แนวทางการเขียน SQL ที่ดี

1. ใช้ชื่อที่สื่อความหมายชัดเจน
2. เขียน JOIN condition ให้ถูกต้องและชัดเจน
3. ระวังการใช้ SELECT * ในระบบจริง
4. ใช้ BETWEEN แทนการเขียนเงื่อนไขซ้ำซ้อน
5. ทดสอบ query กับข้อมูลหลากหลายกรณี 

## การรวมกลุ่มและฟังก์ชันการคำนวณ (Aggregation and Functions)

### ฟังก์ชันการรวมกลุ่ม (Aggregate Functions)
- `avg(expr)` - ค่าเฉลี่ยของข้อมูลในกลุ่ม
- `count(expr)` - นับจำนวนแถวในกลุ่ม
- `max(expr)` - ค่าสูงสุดในกลุ่ม
- `min(expr)` - ค่าต่ำสุดในกลุ่ม
- `sum(expr)` - ผลรวมของข้อมูลในกลุ่ม

### ตัวอย่างการใช้งาน
```sql
-- นับจำนวนเมืองทั้งหมด
SELECT COUNT(*) FROM city;

-- นับจำนวนเมืองที่มีค่า rating ไม่เป็น NULL
SELECT COUNT(rating) FROM city;

-- นับจำนวนประเทศที่แตกต่างกัน
SELECT COUNT(DISTINCT country_id) FROM city;

-- หาประชากรน้อยที่สุดและมากที่สุดของประเทศ
SELECT MIN(population), MAX(population)
FROM country;

-- หาผลรวมประชากรแยกตามประเทศ
SELECT country_id, SUM(population)
FROM city
GROUP BY country_id;

-- หาค่าเฉลี่ย rating ของเมืองในแต่ละประเทศที่มีค่าเฉลี่ยมากกว่า 3.0
SELECT country_id, AVG(rating)
FROM city
GROUP BY country_id
HAVING AVG(rating) > 3.0;
```

## Subqueries (การสืบค้นย่อย)

### Single Value Subquery
```sql
-- หาเมืองที่มี rating เท่ากับ Paris
SELECT name FROM city
WHERE rating = (
    SELECT rating
    FROM city
    WHERE name = 'Paris'
);
```

### Multiple Values Subquery
```sql
-- หาเมืองในประเทศที่มีประชากรมากกว่า 20 ล้านคน
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
-- หาเมืองที่มีประชากรมากกว่าค่าเฉลี่ยของประเทศนั้น
SELECT *
FROM city main_city
WHERE population > (
    SELECT AVG(population)
    FROM city average_city
    WHERE average_city.country_id = main_city.country_id
);

-- หาประเทศที่มีอย่างน้อยหนึ่งเมือง
SELECT name
FROM country
WHERE EXISTS (
    SELECT *
    FROM city
    WHERE city.country_id = country.id
);
```

## Set Operations (การดำเนินการเซต)

### UNION
```sql
-- รวมรายชื่อนักปั่นจักรยานและนักสเก็ตชาวเยอรมัน (ไม่ซ้ำ)
SELECT name
FROM cycling
WHERE country = 'DE'
UNION
SELECT name
FROM skating
WHERE country = 'DE';

-- รวมรายชื่อทั้งหมด (รวมที่ซ้ำ)
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
-- หาคนที่เป็นทั้งนักปั่นจักรยานและนักสเก็ตชาวเยอรมัน
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
-- หานักปั่นจักรยานชาวเยอรมันที่ไม่ได้เป็นนักสเก็ต
SELECT name
FROM cycling
WHERE country = 'DE'
EXCEPT
SELECT name
FROM skating
WHERE country = 'DE';
``` 